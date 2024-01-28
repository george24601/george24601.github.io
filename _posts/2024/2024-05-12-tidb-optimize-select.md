---
layout: post
title: "Rule-based optimization for SELECT in tidb"
description: "" 
category: 
tags: [tidb, golang]
--- 


```go


func logicalOptimize(flag uint64, logic LogicalPlan) (LogicalPlan, error) {
    var err error
    for i, rule := range optRuleList {
        // The order of flags is same as the order of optRule in the list.
        // We use a bitmask to record which opt rules should be used. If the i-th bit is 1, it means we should
        // apply i-th optimizing rule.
        if flag&(1<<uint(i)) == 0 {
            continue
        }
        logic, err = rule.optimize(logic)
        if err != nil {
            return nil, errors.Trace(err)
        }
    }
    return logic, errors.Trace(err)
}


```

### Translate SQL into logical plan


```sql

select b from t1, t2 where t1.c = t2.c and t1.a > 5

```

* Projection: select b
  * Selection: where t1.a > 5
    * Join: from t1, t2 where t1.c = t2.c 
      * DataSource t1
      * DataSource t2


#### Example 2

```sql
select min(id) from t
```
* Aggregation
  * TableScan


```sql
select id from t order by id asc limit 1
```
* Projection
  * TableScan
    * Sort
      * Limit




### Column pruning

* Projection

```go
func (p *LogicalProjection) PruneColumns(parentUsedCols []*expression.Column) {
	child := p.children[0]
	used := getUsedList(parentUsedCols, p.schema)
	for i := len(used) - 1; i >= 0; i-- {
		if !used[i] && !exprHasSetVar(p.Exprs[i]) {
			p.schema.Columns = append(p.schema.Columns[:i], p.schema.Columns[i+1:]...)
			p.Exprs = append(p.Exprs[:i], p.Exprs[i+1:]...)
		}
	}
	selfUsedCols := make([]*expression.Column, 0, len(p.Exprs))
	selfUsedCols = expression.ExtractColumnsFromExpressions(selfUsedCols, p.Exprs, nil)
	child.PruneColumns(selfUsedCols)
}
```


* DataSource

```
// PruneColumns implements LogicalPlan interface.
func (ds *DataSource) PruneColumns(parentUsedCols []*expression.Column) {
	used := getUsedList(parentUsedCols, ds.schema)
	for i := len(used) - 1; i >= 0; i-- {
		if !used[i] {
			ds.schema.Columns = append(ds.schema.Columns[:i], ds.schema.Columns[i+1:]...)
			ds.Columns = append(ds.Columns[:i], ds.Columns[i+1:]...)
		}
	}
	for k, cols := range ds.schema.TblID2Handle {
		if ds.schema.ColumnIndex(cols[0]) == -1 {
			delete(ds.schema.TblID2Handle, k)
		}
	}
}
```

* Aggregation 
  * columns in group by
  * columns in the aggregation function 
* Sort

```go
func (ls *LogicalSort) PruneColumns(parentUsedCols []*expression.Column) {
	child := ls.children[0]
	for i := len(ls.ByItems) - 1; i >= 0; i-- {
		cols := expression.ExtractColumns(ls.ByItems[i].Expr)
		if len(cols) == 0 {
			ls.ByItems = append(ls.ByItems[:i], ls.ByItems[i+1:]...)
		} else {
			parentUsedCols = append(parentUsedCols, expression.ExtractColumns(ls.ByItems[i].Expr)...)
		}
	}
        // add the used columns from parents to its own required list
	child.PruneColumns(parentUsedCols)
}
```
* Selection

```go
// PruneColumns implements LogicalPlan interface.
func (p *LogicalSelection) PruneColumns(parentUsedCols []*expression.Column) {
	child := p.children[0]
	parentUsedCols = expression.ExtractColumnsFromExpressions(parentUsedCols, p.Conditions, nil)
	child.PruneColumns(parentUsedCols)
}
```

* Join
  * columns in the join
 

### Remove unnecessary projection 

* Projection(A) -> Projection(A, B, C)
* Aggregation(A) -> Projection(A, B, C)

```go
// eliminate eliminates the redundant projection in a logical plan.
func (pe *projectionEliminater) eliminate(p LogicalPlan, replace map[string]*expression.Column, canEliminate bool) LogicalPlan {
	proj, isProj := p.(*LogicalProjection)
	childFlag := canEliminate
	if _, isUnion := p.(*LogicalUnionAll); isUnion {
		childFlag = false
	} else if _, isAgg := p.(*LogicalAggregation); isAgg || isProj {
		childFlag = true
	}
	for i, child := range p.Children() {
		p.Children()[i] = pe.eliminate(child, replace, childFlag)
	}

	switch x := p.(type) {
	case *LogicalJoin:
		x.schema = buildLogicalJoinSchema(x.JoinType, x)
	case *LogicalApply:
		x.schema = buildLogicalJoinSchema(x.JoinType, x)
	default:
		for _, dst := range p.Schema().Columns {
			resolveColumnAndReplace(dst, replace)
		}
	}
	p.replaceExprColumns(replace)

	if !(isProj && canEliminate && canProjectionBeEliminatedLoose(proj)) {
		return p
	}
	exprs := proj.Exprs
	for i, col := range proj.Schema().Columns {
		replace[string(col.HashCode())] = exprs[i].(*expression.Column)
	}
	return p.Children()[0]
}
```




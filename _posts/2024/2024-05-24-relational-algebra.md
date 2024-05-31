---
layout: post
title: "Notes on Relational Algebra"
description: "" 
category: 
tags: [paper, db]
--- 


### Overview

* Relational algebra and relational calculus are formal languages associated with the relational model. Informally, relational algebra is a (high-level) procedural language and relational calculus a nonprocedural language.  However, formally both are equivalent to one another.
* Relational algebra operations work on one or more relations to define another relation without changing the original relations. Any operation on
relations produces a relation. This is why we call this an algebra
* A basic expression in the relational algebra consists of either a relation in the database or a constant relation
* Both operands and results are relations, so output from one operation can become input to another operation. 
* Composition of relational algebra operations: Allows expressions to be nested, just as in arithmetic. This property is called closure.
* Relational model is set-based (no duplicate tuples)
* RA is not universal. It is impossible in relational algebra (or standard SQL) to compute the relation Answer(Ancestor, Descendant), e.g., traverse through a graph
* Many relational databases use relational algebra operations for representing execution plans. Relatively easy to manipulate for query optimization


### Operations

#### Basic Operations

* Each operation takes one or two relations as input. Produces another relation as output
* Selection: a subset of rows. Sigma(predicate, R). Unary
* Projection: a subset of columns. Pi(col1, ... colN, R). Unary
   * Result of PROJECT operation is a set of distinct tuples, i.e., maps to a DISTINCT in SQL
* Cartesian product (X)
* Union
* Set Difference: R - S => items in R but not S. R and S must be union-compatible.


* Intersection: R - (R - S) 
* Division: Defines a relation over the attributes C that consists of set of tuples from R that match combination of every tuple in S. Binary, similar to join
* Unary RENAME operator: Sometimes we want to be able to give names to intermediate results of relational algebra operations


#### Join

* Equivalent to performing a Selection, using join predicate as selection formula, over Cartesian product of the two operand relations.
* One of the most difficult operations to implement efficiently in an RDBMS and one reason why RDBMSs have intrinsic performance problems.
* Inner join/Theta join: Defines a relation that contains tuples satisfying the predicate F from the Cartesian product of R and S
  * Can rewrite Theta join using basic Selection and Cartesian product operations.
  * If predicate F contains only equality (=), the term Equijoin is used. 
* Natural join: An Equijoin of the two relations R and S over all common attributes x. One occurrence of each common attribute is eliminated from the result.
  * No join condition
  * Equijoin on attributes having identical names followed by projection to remove duplicate (superfluous) attributes
  * Often attribute(s) in foreign keys have identical name(s) to the corresponding primary keys
* Outer join 
* Semi join: Defines a relation that contains the tuples of R that participate in the join of R with S. 
  * Can rewrite Semijoin using Projection and Join
* To push a projection operation inside a join requires that the result of the projection contain the attributes used in the join.

#### Aggregate Functions & Grouping

* F(grouping attributes, funciton list, R)
  * grouping attributes are attributes of R
  * function list is a list of (function, attribute) pairs
* If no renaming occurs, the attributes of the resulting relation are named by concatenating the name of the function and the attribute.
* Can append -distinct to any aggregate function to specify elimination of duplicates, e.g., count-distinct



#### Relation variables

* Refer to a specific relation: A specific set of tuples, with a particular schema
* A named relation is technically a relation variable


#### Assignment

* relvar <- E. E is an expression that evaluates to a relation
* assign a relation-value to a relation-variable
* the name relvar persists in the database
* Query evaluation becomes a sequence of steps, e.g., % operator



#### Rename

* Results of relational operations are unnamed. Result has a schema, but the relation itself is unnamed
* Used to resolve ambiguities within a specific relational algebra expression
  * Allow a derived relation and its attributes to be referred to by enclosing relational-algebra operations
  * Allow a base relation to be used multiple ways in one query, e.g., self-join
* rho(x, E): x - new name, E - named relation, relation-variable, or an expression that produces a relation
* does not create a new relation-variable
* The new name is only visible to enclosing relational-algebra expression



### Query tree

* Leaf nodes are base relations/operands --- either variables standing for relations or particular, constant relations
* Internal nodes are operators

### Relational algebra vs SQL

* SQL data model is a bag/multiset not a set, i.e. duplicates allowed.
* Some operations, like projection, are more efficient on bags than sets.
* SQL is much more on the “declarative” end of the spectrum (What you want). RA is procedural (how to do it)

#### Null

* Null signifies an unknown value or that a value does not exist.
* The result of any arithmetic expression involving null is null.
* Aggregate functions simply ignore null values (as in SQL)
* For duplicate elimination and grouping, null is treated like any other value, and two nulls are assumed to be the same (as in SQL)
* (unknown or true) = true, (unknown or false) = unknown, (unknown or unknown) = unknown




#### References

* [Lecture 9 – Introduction to Relational Algebra](https://home.adelphi.edu/~siegfried/cs443/443l9.pdf)
* [Relational Algebra](https://web.wlu.ca/science/physcomp/ikotsireas/CP465/W1-Intro-Review/RelationalAlgebra.pdf)
* [Relational algebra](https://www.cbcb.umd.edu/confcour/Spring2014/CMSC424/Relational_algebra.pdf)
* [Relational Algebra, Relational Calculus, and SQL](https://cs.nyu.edu/~jcf/classes/CSCI-GA.2433-001_sp15/slides/session5/RelationalAlgebra-RelationalCalculus-SQL.pdf)
* [Relational Algebra](http://users.cms.caltech.edu/~donnie/cs121/CS121Lec02.pdf)


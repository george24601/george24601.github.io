---
layout: post
title: "How uid genenerator generates snowflake id"
description: ""
category: 
tags: [java]
---

### Default uid generator

```java
    private long getCurrentSecond() {
        long currentSecond = TimeUnit.MILLISECONDS.toSeconds(System.currentTimeMillis());
        if (currentSecond - epochSeconds > bitsAllocator.getMaxDeltaSeconds()) {
            throw new UidGenerateException("Timestamp bits is exhausted. Refusing UID generate. Now: " + currentSecond);
        }

        return currentSecond;
    }

   protected synchronized long nextId() {
        long currentSecond = getCurrentSecond();

        // Clock moved backwards, refuse to generate uid
        if (currentSecond < lastSecond) {
            long refusedSeconds = lastSecond - currentSecond;
            throw new UidGenerateException("Clock moved backwards. Refusing for %d seconds", refusedSeconds);
        }

        // At the same second, increase sequence
        if (currentSecond == lastSecond) {
            sequence = (sequence + 1) & bitsAllocator.getMaxSequence();
            // Exceed the max sequence, we wait the next second to generate uid
            if (sequence == 0) {
                currentSecond = getNextSecond(lastSecond); //this one just self-spins
            }

        // At the different second, sequence restart from zero
        } else {
            sequence = 0L;
        }

        lastSecond = currentSecond;

        // Allocate bits for UID
        return bitsAllocator.allocate(currentSecond - epochSeconds, workerId, sequence);
    }
```

### CachedUidGenerator

Data structure

```java

public class BufferPaddingExecutor {

  /** Whether buffer padding is running */
    private final AtomicBoolean running;

    /** We can borrow UIDs from the future, here store the last second we have consumed */
    private final PaddedAtomicLong lastSecond;  // this is inited with system timestamp, after that it becomes the logical second

    /** RingBuffer & BufferUidProvider */
    private final RingBuffer ringBuffer;
    private final BufferedUidProvider uidProvider;

    /** Padding immediately by the thread pool */
    private final ExecutorService bufferPadExecutors;
    /** Padding schedule thread */
    private final ScheduledExecutorService bufferPadSchedule;
}

```

Init `CachedUidGenerator`

```java

    private void initRingBuffer() {
        // initialize RingBufferPaddingExecutor
        boolean usingSchedule = (scheduleInterval != null);
        this.bufferPaddingExecutor = new BufferPaddingExecutor(ringBuffer, this::nextIdsForOneSecond, usingSchedule);
        if (usingSchedule) {
            bufferPaddingExecutor.setScheduleInterval(scheduleInterval);
        }
}

 this.lastSecond = new PaddedAtomicLong(TimeUnit.MILLISECONDS.toSeconds(System.currentTimeMillis()));

```

Actual population logic

```java

 public void paddingBuffer() {
     boolean isFullRingBuffer = false;
        while (!isFullRingBuffer) {
            List<Long> uidList = uidProvider.provide(lastSecond.incrementAndGet()); //the only place lastSecond is mutated other than init
            for (Long uid : uidList) {
                isFullRingBuffer = !ringBuffer.put(uid);
                if (isFullRingBuffer) {
                    break;
                }
            }
        }
}

```


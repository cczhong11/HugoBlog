---
title: "Database concurrency control"
date: "2018-12-20"
description: "Some basic knowledge about Concurrency control in Database system"
categories:
    - "db"
---


# Concurrency control 

In the database system, it could execute several operations concurrently. The basic unit of **change** in DBMS is Transaction. Transaction is the **execution of a sequence of one or more operations** on a shared database to perform some higher level function. It is a sequence of read and write operations. 

The outcome of a transaction is either COMMIT or ABORT. If commit, all the transaction's modifications are saved to the database. If abort,  all the changes are undone so that this transaction is never happened. 

To make sure the rightness of concurrent operation, we need to define ACID for DBMS.

- Atomicity: all actions in the transaction happen or none happen.
- Consistency: if each transaction is consistent and database is consistent in the beginning, it is guaranteed to be consistent when the transaction completes.
- Isolation: the execution of one transaction is isolated from that of other transactions.
- Durability: If a transaction commits, then its effects on the database persist.

For atomicity, there are two ways to make it happen: shadow paging or logging. For shadow paging, DBMS makes copies of pages and transaction make changes to those copies. These changes only become visible when the transaction commits. DBMS could also logs all actions so that it can undo the actions of aborted transactions. 

For consistency, DBMS will make sure it return correct results. Database consistency means it can accurately represents the real world entity and the future transaction could see the effect of past committed transaction correctly. Transaction consistency make sure DB consistency after executing transaction.

For Isolation, transaction will not see other concurrent transaction's effect. It is equivalent to a system where transactions are executed in serial order. That's why we need concurrency control. It tells DBMS how to interleaving of operations from multiple transactions. There are two categories of concurrency control: pessimistic and optimistic. Pessimistic assume transactions will conflict, optimistic will assume conflicts are rare.

For durability. All of the changes of committed transactions must be durable (i.e., persistent) after a crash or restart. The
DBMS can either use logging or shadow paging to ensure that all changes are durable.

## Goal

The goal of a concurrency control is to generate an execution schedule that is equivalent to some serial execution:

- serial schedule: a schedule does not interleave the actions of different transactions
- equivalent schedules: the effect of executing two schedules are same
- serializable schedule: a schedule is equivalent to some serial execution of transactions.

In other words, we try to schedule concurrent transactions looks like some serial executions.

## Problems

There are some problems when interleaves the operations:

- Read-write conflicts (Unrepeatable reads): A transaction cannot get the same value when reading the same object multiple Times.
- Write-read conflicts(Dirty read): a transaction sees the write effects of a different transaction before that transaction committed its changes.
- Write-Write conflicts (Lost updates): one transaction overwrites the uncommitted data of another concurrent transitions.


Schedule S is **conflict serializable** if you are able to transform S into a serial schedule by **swapping consecutive non-conflicting operations** of different transactions. We could use dependency graphs to check. $T_i$ -> $T_j$ means if one operation $O_i$ of $T_i$ conflicts with an operation $O_j$ of $T_j$ and $O_i$ appears earlier in the schedule in $O_j$. A schedule is conflict serializable if and only the graph is acyclic. 

For View serializable, it means if one write is overwrote by another transaction and there is no read, it is fine. It allows all conflict serializable schedules and blind writes. It is hard to enforce efficiently. It allows more schedules.

## Two phase lock

We need to make all executes correct without knowing the entire schedule ahead of time, using locks to protect database objects could make it work. There are two lock types: Shared lock and Exclusive lock. Locks are requested by transactions from the **lock manager**. The lock manager grants or block requests based on what locks are currently held by other transactions. Transaction will release lock when they do not need them. The lock manager updates its internal lock-table and then gives locks to waiting transactions. 

Two phase locking is **pessimistic** concurrency control protocol. The first phase is **growing** phase, each transaction requests the locks that it needs from the DBMS's lock manager. The second phase is **shrinking** phase, transaction enters this phase when it releases its first lock. It cannot acquire new locks in this phase. The problem is **cascading aborts**, when one transaction aborts, another transaction must be rolled back. Some schedule is serializable but not allowed by 2PL. Strict 2PL means the transaction only releases exclusive-modes locks when it finishes. Rigorous 2PL will take all lock until finishes. A schedule is strict if a value **written** by a transaction is not read or overwritten by other transactions until that transaction finishes. There is no cascading aborts here. 

But it is possible to have deadlock in 2PL. A deadlock is a cycle of transactions waiting for locks to be released by each other. **Deadlock detection** will create a waits-for graph. Edge from $T_i$ to $T_j$ means $T_i$ is waiting $T_j$. The system will periodically check for cycles in waits-for graph and then make a decision on how to break it. When the DBMS detects a deadlock, it will select a “victim” transaction to rollback to break the cycle. DBMS can also decide the rollback length, it could just roll back.

**Deadlock prevention** will prevent transaction waiting a transaction. Assign priorities based on timestamps, older means higher. These schemes guarantee no deadlocks because only one type of direction is allowed when waiting for a lock. When a transaction restarts, its (new) priority is its old timestamp.

- Wait-Die (“Old waits for Young”): If T1 has higher priority, T1 waits for T2. Otherwise T1 aborts 
- Wound-Wait (“Young waits for Old”): If T1 has higher priority, T2 aborts. Otherwise T1 waits.

We could use a use a lock hierarchy that allow a transaction to take more coarse-grained locks in the system. Intention locks allow a **higher level node** to be locked in shared or exclusive mode without having to check all descendant nodes. If a node is in an intention mode, then **explicit locking is being done at a lower level**.


- Intention-Shared (IS): Indicates explicit locking at a lower level with shared locks.
- Intention-Exclusive (IX): Indicates explicit locking at a lower level with exclusive or shared locks. 
- Shared+Intention-Exclusive (SIX): The subtree rooted at that node is locked explicitly in shared mode and explicit locking is being done at a lower level with exclusive-mode locks.

## Timestamp Ordering Concurrency Control

Timestamp ordering (T/O) is an **optimistic** class of concurrency control protocols where the DBMS assumes that transaction conflicts are rare. DBMS instead uses timestamps to determine the serializability order of transactions. Timestamp could allocate using system lock, logical counter and hybrid.

For every database object X is tagged with timestamp of the last transaction that successfully did read/write. W-TS(X) and R-TS(X). If transaction tries to access an object “from the future”, then the DBMS aborts that transaction and restarts it.

In read operations, if $TS(T_i)$ < W-TS(X), the transaction will read a value that was already overwritten and this transaction will be rejected and abort, restart with same TS. If $TS(T_i)$ >= W-TS(X), it allows $TS(T_i)$ to read X and update R-TS(X). Depends on isolation levels, it can make a local copy of X to ensure repeatable reads for $T_i$.

In write operations, if $TS(T_i)$ < W-TS(X) or if $TS(T_i)$ < R-TS(X), it tries to write an obsolete value or the value needed is in the past. Transaction will be aborted and roll back. Else it could write X and update W-TS(X).

For Thomas Write Rule:

If $TS(T_i)$ < R-TS(X), it will abort and restart. If $TS(T_i)$ < W-TS(X), ignore the write and allow transaction to continue. It make use of view serializability, deleting obsolete write operations from the transactions that issue them. 

The basic timestamp ordering cannot have deadlocks because no transaction ever waits. But there is a possibility of starvation for long transactions if short transactions keep causing conflicts.

It also permits schedules that are not **recoverable**. A schedule is recoverable if transactions commit only after all transactions whose changes they read or commit. Otherwise, the DBMS cannot guarantee that transactions read data that will be restored after recovering from a crash.

Issues:

- High overhead from copying data to transaction’s workspace and from updating timestamps.
- Long running transactions can get starved: The likelihood that a transaction will read something from a newer transaction increases.
- Suffers from the timestamp allocation bottleneck on highly concurrent systems.

## Optimistic Concurrency control

OCC works well when the number of conflicts is low. The DBMS creates a private workspace for each transaction, all modifications are applied to the workspace, any object read is copied into workspace, no other transaction can read the changes made by another transaction in the private space. When a transaction commits, DBMS compares **write set** to see whether it conflicts with other transactions. If there are no conflicts, the write set is installed into the ”global” database.

- Read Phase: Track the read/write sets of transactions and store their writes in a private workspace.
- Validation Phase: When a transaction commits, check whether it conflicts with other transactions.
- Write Phase: If validation succeeds, apply private changes to database. Otherwise abort and restart the transaction.

If $TS(T_i) < TS(T_j)$, one of the following conditions must hold:

1. $T_i$ completes all three phases before $T_j$ begins
2. $T_i$ completes before $T_j$ starts its Write phase, and $T_i$ does not write to any object read by $T_j$ .
3. $T_i$ completes its Read phase before $T_j$ completes its Read phase, and $T_i$ does not write to any object that is either read or written by $T_j$ .

Potential Issues:

• High overhead for copying data locally into the transaction’s private workspace.
• Validation/Write phase bottlenecks.
• Aborts are potentially more wasteful than in other protocols because they only occur after a transaction has already executed.
• Suffers from timestamp allocation bottleneck.


## Partition-Based T/O

It is slow to check whether there is a conflict with concurrent transactions across the entire database. An alternative is to split the database up in disjoint subsets called **partitions**  and then only check for conflicts between transactions that are running in the same partition.

• The transaction acquires a partition’s lock if it has the **lowest** timestamp in that partition’s queue.
• The transaction starts when it has **all of the locks** for all the partitions that it will access during execution.
• Transactions can read/write anything that they want at the partitions that they have locked. **If a transaction tries to access a partition that it does not have the lock, it is aborted + restarted.**

It works well if DBMS knows which partition to go to before transaction starts and all transaction only needs to access a single partition. The protocol only works if (1) transactions are stored procedures (network communication causes the partition to idle because it has to wait for the next query to execute) and (2) transactions only touch one partition (multi partition transactions cause partitions to be idle because partitions have to wait for the next query to execute).

## Multi-Version Concurrency Control

The DBMS maintains multiple physical versions of a single logical object in the database. When a transaction writes to an object, the DBMS creates a new version of that object. When a transaction reads an object, it reads the newest version that existed when the transaction started.

The key properties: Writers don’t block the readers. Readers don’t block the writers. Read-only transactions can read a consistent snapshot without acquiring locks. Timestamps are used to determine visibility. It supports time-travel queries. 

Version storage will help DBMS decide how to store different physical versions of a logical object. The DBMS uses the tuple’s pointer field to create a version chain per logical tuple. Indexes always point to the head of the chain. A thread traverses chain until you find the version thats visible to you. 

- Append-Only Storage – New versions are appended to the same table space.
	- Oldest-To-Newest (O2N): Append new version to end of chain, look-ups require entire chain traversal.
	- Newest-To-Oldest (N2O): Head of chain is newest, look-ups are quick, but indexes need to be up- dated every version.
- Time-Travel Storage – Old versions are copied to separate table space.
- Delta Storage – The original values of the modified attributes are copied into a separate delta record space.

Garbage Collection: The DBMS needs to remove reclaimable physical versions from the database over time. 

- Tuple Level Garbage Collection – Find old versions by examining tuples directly
	- Background Vacuuming: Separate threads periodically scan the table and look for reclaimable ver- sions, works with any version storage scheme.
	- Cooperative Cleaning: Worker threads identify reclaimable versions as they traverse version chain. Only works with O2N.
- Transaction Level – Each transaction keeps track of its own read/write set. When a transaction completes, the garbage collector can use that to identify what tuples to reclaim.


Index Management: All primary key (pkey) indexes always point to version chain head. 

- Logical Pointers – Use a fixed identifier per tuple that does not change. Requires an extra indirection layer that maps the logical id to the physical location of the tuple
- Physical Pointers – Use the physical address to the version chain head

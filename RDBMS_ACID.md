

# RDBMS ACID Transactions

## Transaction
* A collection of queries
* One unit of atomic operation.

Ex : Account deposit - Send $100 from account1 to account2:
```sql
BEGIN TX1
  SELECT balance FROM account WHERE ID = 1
  BALANCE > 100
    UPDATE account SET balance = balance - 100 WHERE ID = 1
    UPDATE account SET balance = balance + 100 WHERE ID = 2
COMMIT TX1
```

## Atomicity
All or Nothing!

In the context of databases, atomicity means that you either:

 * Commit to the entirety of the transaction occurring
 * Have no transaction at all
 
 
Example:

Table: pictures:

|pict-id | url | likes  | 
| -------| ----| ------ |
|1       | url | 0      |

Table: likes

|user-id| pict-id |
| ----- | ------  |
|       |         |

If a user likes a picture following things need to happen for the above tables:
1. UPDATE pictures SET likes = likes+1 WHERE pict-id = 1
2. INSERT INTO likes VALUES (1, 'JON')

Both the above queries must execute as if they are one single operation. If first query succeeds, but 2nd fails, data ends up inconsisitent. There could be many reasons for failure due to which a query can fail.

ATOMICITY guarantees each TRANSACTION as a single unit, which either succeeds completely or fails completely. If any of the statements constituting a transaction fails to complete, the entire transaction fails and the database is left unchanged

## Consistency

Consistency refers to maintaining data integrity constraints.

A consistent transaction will not violate integrity constraints placed on the data by the database rules. Enforcing consistency ensures that if a database enters into an illegal state (if a violation of data integrity constraints occurs) the process will be aborted and changes rolled back to their previous, legal state.

Another way of ensuring consistency within a database throughout each transaction is by also enforcing declarative constraints placed on the database.

An example of a declarative constraint might be that all customer accounts must have a positive balance. If a transaction would bring a customer account into a negative balance, that transaction would be rolled back. This ensures changes are successful at maintaining data integrity or they are canceled completely.

## Isolation
Users' sessions dont affect each other.

Any reads or writes performed on the database will not be impacted by other reads and writes of separate transactions occurring on the same database. A global order is created with each transaction queueing up in line to ensure that the transactions complete in their entirety before another one begins.

Importantly, this doesn’t mean two operations can’t happen at the same time. Multiple transactions can occur as long as those transactions have no possibility of impacting the other transactions occurring at the same time.

Doing this can have impacts on the speed of transactions as it may force many operations to wait before they can initiate. However, this tradeoff is worth the added data security provided by isolation.

Examples for basic transactions with isolation: https://www.youtube.com/watch?v=NHKHzwolbKU

### Read Phenomena and Isolation Levels

| Read Phenomena | Description |
| -------------- | ---------------- |
| Dirty Reads | Occurrs when one transaction is allowed to read data from row that has been modified by another running transaction and not yet committed.|
| Non-Repeatable Reads | Occurs during a transaaction, a raw is retrieved twice and values within the row differ brtween reads.|
| Phantom Reads | Occurs when, during a transaction, new rows are added or removed by another transaftion to the records being read.|

Most DBMSs offer a number of transaction isolation levels, which control the degree of locking that occurs when selecting data. Majority of database transactions can be constructed to avoid requiring high isolation levels (e.g. SERIALIZABLE level), thus reducing the locking overhead for the system. 

Programmer must carefully analyze database access code to ensure that any relaxation of isolation does not cause software bugs that are difficult to find. Conversely, if higher isolation levels are used, the possibility of deadlock is increased, which also requires careful analysis and programming techniques to avoid.

|  Isolation Level/ Read Phenomena     | Dirty Reads | Lost Updates | Non-Repeatable reads | Phantom Reads |
| ----  | ----------- | ------------ | -------------------- | ------------- |
| Read Uncommitted | Possible | Possible | Possible | Possible |
| Read Committed   | NotPossible | Possible | Possible | Possible |
| Repeatable Read  | NotPossible | NotPossible | NotPossible | Possible | 
| Serializable  | NotPossible | NotPossible | NotPossible | NotPossible | 

## Durability
Once data is committed, its permanent.

Durability ensures that changes made to the database (transactions) that are successfully committed will survive permanently, even in the case of system failures.

Durability is achieved through the use of changelogs that are referenced when databases are restarted.



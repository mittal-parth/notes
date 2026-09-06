# ACID Transactions


## Atomicity

- Either all changes happen or nothing happens.
- Changes inside a txn are not treated as final until the txn is committed.

## Consistency

- After a txn commits, the data should still follow the rules we defined for the DB like primary key, valid data type, `NOT NULL` constraints, `CHECK` constraints etc.

## Isolation
Controls what transactions can see when they run at the same time.

### Common concurrency problems: 
- Dirty read: a txn reads data another txn has not committed
- Non-repeatable read: a txn reads the same row twice and sees different committed values
- Phantom read: a repeated query returns a different _set_ of matching rows. Similar to prev but its for multiple rows.
- Lost update: 2 txn's overwrite each other's changes
- Write skew: 2 txns read overlapping data but modify completely unrelated non-overlapping pieces of data, collectively violating a DB integrity rule together.

### Isolation Levels


## Durability
    - Once committed, the DB can recover the older state after a crash.
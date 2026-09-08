# ACID Transactions

## Atomicity

- Either all changes happen or nothing happens.
- Changes inside a txn are not treated as final until the txn is committed.

## Consistency

- After a txn commits, the data should still follow the rules we defined for the DB like primary key, valid data type, `NOT NULL` constraints, `CHECK` constraints etc.

## Isolation
Controls what transactions can see when they run at the same time.

### Common concurrency problems:

> Don't Nap on Pizza, Loser — We skewed

| Letter | Problem | Explanation | Example |
|--------|---------|-------------|---------|
| D | Dirty read | A txn reads data another txn has not committed | You read someone's diary before they hit save. It's not real yet — you're sniffing uncommitted drama. |
| N | Non-repeatable read | A txn reads the same row twice and sees different committed values | You check your bank balance → $100. Blink. Check again → $40. Same row, new committed truth. |
| P | Phantom read | A repeated query returns a different set of matching rows | You count pepperoni slices twice. The count changed — not one slice moved, the whole set did. Ghost toppings. |
| L | Lost update | Two txns overwrite each other's changes | Two people edit the same Google Doc. Both hit save. One person's work gets yeeted — last writer wins. |
| W | Write skew | Two txns read overlapping data but modify different non-overlapping parts, collectively violating a DB rule | Two surgeons each operate on a different organ. Neither sees the other's chart. Together they violate "patient must have ≥1 kidney." Fine alone, illegal together. |

### Isolation Levels

Higher isolation levels protect from more concurrency bugs but more time / conflicts.

| Isolation level   | Protects against                                      | Tradeoff                                           |
|-------------------|-------------------------------------------------------|----------------------------------------------------|
| Read uncommitted  | Very little                                           | Fast, but can read uncommitted changes           |
| Read committed    | Dirty reads                                           | Still allows some surprises across repeated reads |
| Repeatable read   | Many repeated read problems                           | Exact behaviour depends on DB                      |
| Serializable      | Makes txns behave as if they ran one at a time        | More blocking, failed txns, or retries             |

> same name setting can differ with db

DB use common techniques to enforce isolation: 
- Locks: make one txn wait for another
- MVCC (Multi Version Concurrency Control): lets readers see a stable snapshot while writes create newer _versions_ of same data. readers never block writers, and writers never block readers. Once fully committed, new version becomes the new stable snapshot.
- Range locks: locks over a group of rows
- Conflict detection: stop txn when not safe

## Durability

- Once committed, the DB can recover the older state after a crash.
- Write-Ahead Logging (WAL): Write the recovery record before relying on the changed data page. If the database crashes after commit but before the changed pages reach the main files, recovery can replay the WAL and restore the committed changes.
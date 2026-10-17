# Schema design

Owner: Person 1. Design baseline: 17 October 2026. Read alongside requirements.md and data-dictionary.md.

## Structure and normalization

Eleven relations separate catalogue reference data, physical inventory, users and transactions. BOOK_AUTHORS resolves the many-to-many authorship relationship with composite PK (book_id, author_id). Other tables have their named ID as single-column PK. ISBN and normalized email are alternate unique identifiers. FINES.borrowing_id is unique.

The design targets third normal form: columns are atomic, association rows depend on both key components, publisher/category/branch descriptions are kept in their own tables, and transaction records store identifiers rather than duplicated names. Computed totals, lateness and risk are derived. copy_status is operational state maintained atomically with loans, with consistency checks assigned below.

## Relationships and cardinalities

Every child listed below references exactly one parent through a non-null FK. Parent participation is optional: a parent can exist before its children.

| Parent | Child | Child FK | Children per parent |
|---|---|---|---|
| CATEGORIES | BOOKS | category_id | 0..many |
| PUBLISHERS | BOOKS | publisher_id | 0..many |
| BOOKS | BOOK_AUTHORS | book_id | 0..many |
| AUTHORS | BOOK_AUTHORS | author_id | 0..many |
| BOOKS | BOOK_COPIES | book_id | 0..many |
| BRANCHES | BOOK_COPIES | branch_id | 0..many |
| USERS | BORROWINGS | user_id | 0..many |
| BOOK_COPIES | BORROWINGS | copy_id | 0..many |
| USERS | RESERVATIONS | user_id | 0..many |
| BOOKS | RESERVATIONS | book_id | 0..many |
| BORROWINGS | FINES | borrowing_id | 0..1 |

BOOKS and AUTHORS therefore have optional many-to-many participation through BOOK_AUTHORS. Each borrowing concerns one user and one physical copy. Each reservation concerns one user and one edition.

## Integrity rules and implementation responsibility

Person 2 implements the following; these are specifications, not SQL scripts.

| Rule | Enforcement |
|---|---|
| All PKs, required columns, FKs and declared unique fields follow the dictionary | PK, NOT NULL, FK and UNIQUE constraints |
| Positive integer identifiers, publication year 1–9999, fine amount > 0 | CHECK plus declared numeric precision |
| Enumerated user/copy/return/reservation/fine domains | CHECK constraints with explicit NOT NULL where required |
| ISBN has 10 or 13 characters, ISBN-10 permits terminal X; email is trimmed lowercase | Normalize and validate in entry workflow; enforce format checks as supported; full ISBN checksum validation is outside scope |
| due_date > borrow_date | Row CHECK |
| return_date >= borrow_date and lost_date >= borrow_date when present | Row CHECK |
| return_date and lost_date cannot both be present | Row CHECK |
| return_condition is present exactly when return_date is present | Explicit paired NULL/NOT NULL CHECK branches; avoid relying on comparisons with NULL |
| PAID requires payment_date >= assessed_date; OUTSTANDING requires null payment_date | Explicit status/date CHECK branches |
| One open loan per copy (return_date and lost_date both null) | Conditional unique function-based index or equivalent concurrency-safe enforcement |
| One ACTIVE reservation per (user_id, book_id) | Conditional composite unique index or equivalent concurrency-safe enforcement |
| Checkout only AVAILABLE copies; atomically create loan and set ON_LOAN | Transaction with copy-row locking; recheck status after lock |
| Return an open loan once; GOOD sets AVAILABLE, DAMAGED sets DAMAGED | Locked atomic transaction updates loan and copy |
| Declare loss once on an open loan; set lost_date and copy LOST | Locked atomic transaction; no fabricated return timestamp |
| Available/damaged/lost/withdrawn copies have no open loans; ON_LOAN has exactly one | Transaction workflow plus Person 5 consistency validation |
| No future borrow/return/loss/payment events at entry; assessment not before borrowing | Transaction validation across rows/current time; not a static row CHECK |
| Reservation fulfillment must correspond to a checkout of that edition to that user | Same checkout transaction updates selected ACTIVE reservation; no permanent loan FK is required in this scope |
| Closed reservations are terminal; outstanding fines can become paid once | Transaction transition validation |
| Historical users, books, copies and loans must not be deleted while referenced | Restrictive FK behavior; no cascading deletes |

Do not assume a CHECK can query another table or safely enforce concurrent availability. Person 2 chooses the Oracle implementation mechanism and records it in oracle-setup.md. Updates involving multiple records must commit or roll back together. Copy branch and user category changes after borrowing history are excluded by the baseline workflow.

## Derived definitions and query grain

An open loan has null return_date and null lost_date. An overdue loan is open and due_date < the chosen as-of timestamp. A late return has return_date > due_date. A lost incident has lost_date present. A damaged incident has return_condition = DAMAGED. Outstanding amount sums FINES.amount for OUTSTANDING rows; an absent sum becomes zero.

Book circulation grain is one row per book. The user borrowing/fine history view should use one row per borrowing with an optional fine, preserving users without loans using an outer join if presenting all users. Person 4 must document the chosen view grain. For user summaries, aggregate loans/fines before joining authors; authorship joins otherwise duplicate loan counts and money. Count loan identifiers rather than outer-join placeholder rows.

## Build order and reporting paths

Create CATEGORIES, PUBLISHERS, AUTHORS, USERS and BRANCHES first; then BOOKS; then BOOK_AUTHORS and BOOK_COPIES; then BORROWINGS and RESERVATIONS; finally FINES.

Analytics paths: book → copy → borrowing; branch → copy → borrowing; category → book → copy → borrowing; author → association → book → copy → borrowing; user → borrowing → optional fine. Reservation reporting uses user → reservation → book. This covers all ten analytics, advanced joins and risk inputs without storing derived report columns.

## Validation handoff to Person 5

Verify duplicate ISBN/email, orphan FK, invalid status, negative fine, invalid date ordering, and mismatched payment/return fields are rejected. Attempt two concurrent checkouts of one copy and two active duplicate reservations. Verify good return, damaged return and loss transitions and rollback consistency. Include zero-loan users, never-borrowed editions, coauthored books, multiple branches, paid/outstanding fines and several months. Verify lost loans are not currently overdue and late returned loans remain historical late incidents. Compare report totals against manually calculated sample expectations.

## Remaining decisions

Person 1 design is ready for team review. Person 2 owns SQL implementation and Oracle version compatibility. Loan periods, rates and limits require team agreement. Person 5 owns risk thresholds and windows. No SQL execution or stakeholder approval is claimed.

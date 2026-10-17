# Data dictionary

Person 1 design baseline, 17 October 2026. Required means NOT NULL. All PK components are required. IDs are positive integers assigned by Person 2 using a consistent Oracle-supported mechanism. No column defaults are assumed. DATE includes time; use one agreed campus time basis. Amounts are INR.

## CATEGORIES

| Column | Oracle type | Keys / nullability | Meaning / domain |
|---|---|---|---|
| category_id | NUMBER(10) | PK | Category identifier |
| category_name | VARCHAR2(100 CHAR) | UNIQUE, required | Category name |

## PUBLISHERS

| Column | Oracle type | Keys / nullability | Meaning / domain |
|---|---|---|---|
| publisher_id | NUMBER(10) | PK | Publisher identifier |
| publisher_name | VARCHAR2(200 CHAR) | UNIQUE, required | Publisher name |

## AUTHORS

| Column | Oracle type | Keys / nullability | Meaning / domain |
|---|---|---|---|
| author_id | NUMBER(10) | PK | Author identifier |
| author_name | VARCHAR2(150 CHAR) | required | Names need not be unique |

## USERS

| Column | Oracle type | Keys / nullability | Meaning / domain |
|---|---|---|---|
| user_id | NUMBER(10) | PK | User identifier |
| full_name | VARCHAR2(150 CHAR) | required | Display name |
| email | VARCHAR2(254 CHAR) | UNIQUE, required | Store trimmed lowercase email |
| user_type | VARCHAR2(7 CHAR) | required | STUDENT or FACULTY |
| department | VARCHAR2(100 CHAR) | required | Academic department |
| phone | VARCHAR2(20 CHAR) | optional | Text preserves + and leading zeroes |

## BRANCHES

| Column | Oracle type | Keys / nullability | Meaning / domain |
|---|---|---|---|
| branch_id | NUMBER(10) | PK | Branch identifier |
| branch_name | VARCHAR2(100 CHAR) | UNIQUE, required | Branch name |
| location | VARCHAR2(200 CHAR) | required | Campus location |

## BOOKS

| Column | Oracle type | Keys / nullability | Meaning / domain |
|---|---|---|---|
| book_id | NUMBER(10) | PK | Bibliographic edition identifier |
| isbn | VARCHAR2(13 CHAR) | UNIQUE, required | Normalized ISBN-10 or ISBN-13 without separators |
| title | VARCHAR2(250 CHAR) | required | Edition title |
| category_id | NUMBER(10) | FK CATEGORIES, required | One category per edition |
| publisher_id | NUMBER(10) | FK PUBLISHERS, required | One publisher per edition |
| publication_year | NUMBER(4) | required | Integer from 1 to 9999 |

## BOOK_AUTHORS

| Column | Oracle type | Keys / nullability | Meaning / domain |
|---|---|---|---|
| book_id | NUMBER(10) | PK part, FK BOOKS | Edition |
| author_id | NUMBER(10) | PK part, FK AUTHORS | Author |

## BOOK_COPIES

| Column | Oracle type | Keys / nullability | Meaning / domain |
|---|---|---|---|
| copy_id | NUMBER(10) | PK | Physical copy identifier |
| book_id | NUMBER(10) | FK BOOKS, required | Edition |
| branch_id | NUMBER(10) | FK BRANCHES, required | Owning branch; fixed once circulation exists |
| copy_status | VARCHAR2(12 CHAR) | required | AVAILABLE, ON_LOAN, LOST, DAMAGED, WITHDRAWN |

## BORROWINGS

| Column | Oracle type | Keys / nullability | Meaning / domain |
|---|---|---|---|
| borrowing_id | NUMBER(10) | PK | Loan identifier |
| user_id | NUMBER(10) | FK USERS, required | Borrower |
| copy_id | NUMBER(10) | FK BOOK_COPIES, required | Physical copy |
| borrow_date | DATE | required | Checkout timestamp |
| due_date | DATE | required | Due timestamp |
| return_date | DATE | optional | Actual return timestamp, including damaged returns |
| return_condition | VARCHAR2(7 CHAR) | optional | GOOD or DAMAGED; present exactly when returned |
| lost_date | DATE | optional | Loss declaration timestamp; closes a loan without a return |

## RESERVATIONS

| Column | Oracle type | Keys / nullability | Meaning / domain |
|---|---|---|---|
| reservation_id | NUMBER(10) | PK | Reservation identifier |
| user_id | NUMBER(10) | FK USERS, required | Requester |
| book_id | NUMBER(10) | FK BOOKS, required | Reserved edition |
| reservation_date | DATE | required | Request timestamp |
| status | VARCHAR2(9 CHAR) | required | ACTIVE, FULFILLED, CANCELLED, EXPIRED |

## FINES

| Column | Oracle type | Keys / nullability | Meaning / domain |
|---|---|---|---|
| fine_id | NUMBER(10) | PK | Fine identifier |
| borrowing_id | NUMBER(10) | UNIQUE, FK BORROWINGS, required | At most one combined assessed fine per loan |
| amount | NUMBER(10,2) | required | Assessed INR amount greater than zero |
| assessed_date | DATE | required | Assessment timestamp |
| fine_status | VARCHAR2(11 CHAR) | required | OUTSTANDING or PAID |
| payment_date | DATE | optional | Required exactly when PAID |

Cross-column and transaction rules are specified in schema-design.md. No derived overdue count, balance, circulation count, or risk label is stored.
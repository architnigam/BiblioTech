# Requirements

Owner: Person 1 — Database Architect / Designer. Design baseline: 17 October 2026. Prepared for team review; not a claim of stakeholder approval or database implementation.

## Purpose and scope

Replace manual university library circulation records with an Oracle relational database. Support cataloguing, physical copies across branches, student/faculty membership, circulation, reservations, fines, utilization reports, and user risk identification. Authentication, a UI, acquisitions, interbranch transfers, renewal history, partial payments, and automated notifications are outside this assignment baseline.

## Functional requirements and acceptance criteria

| ID | Requirement | Acceptance criterion | Design coverage |
|---|---|---|---|
| FR01 | Catalogue editions, authors, publishers, categories | One edition has a unique normalized ISBN and may have multiple authors | BOOKS, AUTHORS, BOOK_AUTHORS, PUBLISHERS, CATEGORIES |
| FR02 | Track physical copies and branches | Distinguish multiple copies of an edition and their current status | BOOK_COPIES, BRANCHES |
| FR03 | Register users | Each user is exactly STUDENT or FACULTY, with unique normalized email | USERS |
| FR04 | Record borrowing and returns | Preserve each loan; prevent simultaneous open loans for a copy; record due and actual return timestamps | BORROWINGS, BOOK_COPIES |
| FR05 | Reserve editions | Track active and closed requests; prevent duplicate active requests for a user and edition | RESERVATIONS |
| FR06 | Assess and collect fines | Preserve assessed amount, assessment date, settlement status and payment date | FINES |
| FR07 | Analyze utilization | Support all ten reports below, including users/books with no activity | Shared relationships |
| FR08 | Demonstrate advanced SQL | Support overall-average and peer-group comparisons and the two required views | USERS, BORROWINGS, FINES, catalogue |
| FR09 | Identify user risk | Derive overdue count, outstanding fines, late returns, lost and damaged incidents per user | BORROWINGS, FINES |
| FR10 | Preserve integrity and history | Reject invalid references/statuses/dates and retain historical loans and fines | Constraints and transaction rules |

## Required analytics

1. Most borrowed books: count loan rows per edition, ranked descending; include ties.
2. Most active users: count loans per user, including zero-loan users.
3. Overdue books: open loans with due_date before the report timestamp.
4. Fine collection: assessed, paid and outstanding totals; collection over time uses payment_date.
5. Category circulation: loan counts per category, including zero activity.
6. Branch utilization: loan counts per owning branch, including zero activity; any utilization ratio must state its denominator and period.
7. Books never borrowed: editions with no loan through any copy, including editions with no copies.
8. Popular authors: each coauthor receives credit for a borrowing of their edition; author totals are not additive across authors.
9. Monthly borrowing trends: group borrow_date by calendar year and month.
10. Repeated overdue returns: at least two actual returns later than due_date; distinguish these from currently overdue loans.

## Advanced reporting and risk

Compare each user's lifetime loan count with the mean across all users, including zero-loan users. Peer comparisons use user_type and include all users in that category. Book circulation statistics must preserve zero-loan editions. User borrowing/fine history must avoid multiplying fine totals through joins.

Risk output contains user, category (user_type), overdue books, outstanding fine and risk status. Calculate lost and damaged incidents from borrower-linked loan history, not the current copy status. Person 5 owns numerical thresholds, time windows and classification precedence in risk-rules.md; these are deliberately not finalized here.

## Project assumptions for team review

- One BOOKS row represents an edition/ISBN, with one category and one publisher. Author names are not unique. Catalogue records may temporarily have no linked authors; curated sample books should have at least one.
- All branches share users and catalogue data. Reservations target editions and do not allocate a physical copy or promise a queue policy.
- Loans use explicit due timestamps; borrowing limits, duration and fine rates are entered/agreed by the team rather than invented as university policy.
- A lost declaration closes a loan; a later recovery workflow is outside scope. A damaged return closes the loan and leaves the copy unavailable.
- One combined assessed fine per loan; payment is in full. No partial payments, waivers or refunds in this baseline. No fine row means no assessed fine.
- Current overdue means unreturned and not declared lost. Repeated late returns count actual returned loans only. Use a fixed report timestamp when verifying sample outputs.
- Historical copy ownership and user categories are not versioned. Branch transfers and category changes after circulation are outside scope to keep attribution stable.

## Handoff and acceptance

Person 2 implements the dictionary and constraints; Persons 3 and 4 implement the defined reports; Person 5 validates the rejection cases and risk inputs. Team review must confirm these assumptions, Oracle environment, loan policies and risk thresholds. The design does not depend on a specific Oracle version or claim executed validation.

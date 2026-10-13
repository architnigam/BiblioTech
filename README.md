# BiblioTech

University Library Management and Resource Utilization System — a five-person **Oracle SQL database project**.

## Current status

**Scaffold only:** folders and empty placeholder files have been created. Database design, SQL, data, tests, and reports are still to be written. Person 1 (you) is the Database Architect / Designer. This project structure covers the database assignment; no application frontend or backend is planned here.

## Project scope

Manage books, authors, publishers, categories, physical copies, branches, student/faculty users, borrowings, returns, reservations, and fines. Analyze library resource utilization and identify users with overdue books, outstanding fines, repeated late returns, or lost/damaged books.

The team will use Oracle Database and Oracle-compatible SQL. The exact Oracle version and client will be documented by Person 2.

## Folder and file map

```text
BiblioTech/
├── README.md                         Shared project overview and ownership
├── docs/
│   ├── requirements.md               Person 1
│   ├── schema-design.md              Person 1
│   ├── data-dictionary.md            Person 1
│   ├── diagrams/
│   │   └── README.md                 Person 1: diagram notes placeholder
│   ├── oracle-setup.md               Person 2
│   ├── risk-rules.md                 Person 5
│   └── final-report.md               All five members
├── sql/
│   ├── 01_schema.sql                 Person 2
│   ├── 02_sample_data.sql            Person 2
│   ├── 03_transactions.sql           Person 2
│   ├── 04_analytics.sql              Person 3
│   ├── 05_advanced_queries.sql       Person 4
│   ├── 06_views.sql                  Person 4
│   └── 07_risk_analysis.sql          Person 5
├── tests/
│   ├── test-plan.md                  Person 5
│   ├── test-results.md               Person 5
│   ├── issues.md                     Person 5
│   └── validation.sql               Person 5
├── results/
│   ├── analytics/                    Person 3: query evidence
│   ├── advanced-sql/                 Person 4: query/view evidence
│   ├── risk-analysis/                Person 5: risk report evidence
│   └── testing/                      Person 5: integration logs/evidence
└── submission/                       All five members: final deliverables
```

All files apart from this README are intentionally empty. Output folders contain empty `.gitkeep` files so Git preserves them. Person 1 will later add an editable ER diagram source and `docs/diagrams/er-diagram.png`; an invalid empty PNG has not been created.

## Five-person responsibility split

### Person 1 — Database Architect / Designer (you)

Convert the requirements into entities, attributes, relationships, cardinalities, primary/foreign keys, and integrity constraints. Create the ER diagram and complete table/column specifications before implementation starts.

**Files:** `docs/requirements.md`, `docs/schema-design.md`, `docs/data-dictionary.md`, `docs/diagrams/README.md`, and the future ER diagram source/export in `docs/diagrams/`.

The design must cover users, books, authors, book–author associations, publishers, categories, branches, copies, borrowings/returns, reservations, and fines. The final entity list and publisher representation remain your design decisions. Agree valid statuses, user categories, ISBN uniqueness, fine rules, and lost/damaged-book representation with Persons 2 and 5.

**Done when:** the team has approved requirements, schema specifications, PK/FK mappings, constraints, relationships, cardinalities, and the ER diagram.

### Person 2 — Oracle Database Developer

Implement the approved design using Oracle tables in dependency order, including primary keys, foreign keys, `NOT NULL`, `UNIQUE`, and `CHECK` constraints. Prepare realistic sample data and borrowing, return, reservation, and fine transactions. Document the Oracle environment and execution instructions.

**Files:** `sql/01_schema.sql`, `sql/02_sample_data.sql`, `sql/03_transactions.sql`, `docs/oracle-setup.md`.

Include popular and never-borrowed books, active and inactive users, current overdue loans, on-time and late returns, paid and outstanding fines, reservations, and lost/damaged cases. Suggested data scale: 30–50 books, 15–25 authors, 30+ users, 5+ categories, 2–3 branches, 50+ copies, and 50+ borrowings. Scenario coverage matters more than exact counts.

**Done when:** the first three scripts create and populate a clean Oracle schema and support the other members' queries.

### Person 3 — SQL Analyst

Implement all ten required analytical queries:

1. Most borrowed books.
2. Most active users.
3. Currently overdue books.
4. Fine collection, including paid and outstanding amounts.
5. Category-wise circulation.
6. Branch-wise utilization.
7. Books never borrowed.
8. Popular authors.
9. Monthly borrowing trends.
10. Users with repeated overdue returns.

**Files:** `sql/04_analytics.sql`; save result evidence under `results/analytics/`.

**Done when:** all ten queries run correctly in Oracle and return checked, meaningful results from the shared sample data.

### Person 4 — Advanced SQL Developer

Develop multi-table joins, subqueries comparing borrowing frequency with the overall average, and correlated subqueries comparing users with their student/faculty peer group. Create the required book circulation statistics view and user borrowing/fine history view.

**Files:** `sql/05_advanced_queries.sql`, `sql/06_views.sql`; save evidence under `results/advanced-sql/`.

**Done when:** joins, subqueries, correlated subqueries, and both views execute correctly and their outputs have been checked.

### Person 5 — Risk Analysis and QA Developer

Calculate overdue-book counts, outstanding fines, repeated late returns, and lost/damaged-book incidents. Agree and document classification rules before implementing labels such as `NORMAL`, `MEDIUM RISK`, and `HIGH RISK`. Thresholds have not yet been decided.

Produce a risk report containing user, category, overdue books, outstanding fine, and risk status. Test the full project in a clean Oracle schema, record failures, assign fixes to file owners, and verify corrections.

**Files:** `sql/07_risk_analysis.sql`, `docs/risk-rules.md`, `tests/test-plan.md`, `tests/test-results.md`, `tests/issues.md`, `tests/validation.sql`. Save evidence under `results/risk-analysis/` and `results/testing/`.

**Done when:** risk classification works, all seven project scripts execute in order, and integration results are recorded.

### Shared responsibilities

All five members contribute their sections to `docs/final-report.md`, review the completed project, and prepare deliverables in `submission/`. Maintain this README when ownership or scope changes. Coordinate schema changes through Persons 1 and 2 before changing dependent queries.

## Work sequence

1. Person 1 finalizes requirements and database design.
2. Person 2 implements the approved schema and sample data.
3. Persons 3, 4, and 5 develop analytics, advanced SQL/views, and risk analysis against the shared schema.
4. Person 5 integration-tests; the relevant owners fix issues.
5. All five members review results and complete the report/submission.

## Planned Oracle execution order

Once implemented, execute:

1. `sql/01_schema.sql`
2. `sql/02_sample_data.sql`
3. `sql/03_transactions.sql`
4. `sql/04_analytics.sql`
5. `sql/05_advanced_queries.sql`
6. `sql/06_views.sql`
7. `sql/07_risk_analysis.sql`

Person 5 runs `tests/validation.sql` at the checkpoints documented in the test plan. Person 2 documents prerequisites, transaction effects, rerun behavior, and client-specific execution steps in `docs/oracle-setup.md`. The scripts are currently empty and do not create a working database.

## Final deliverables checklist

- [ ] Requirements, schema design, and data dictionary — Person 1.
- [ ] Editable ER diagram and exported image — Person 1.
- [ ] Oracle schema, sample data, transactions, and setup guide — Person 2.
- [ ] Ten analytical queries and result evidence — Person 3.
- [ ] Advanced queries, two views, and result evidence — Person 4.
- [ ] Risk rules, classification query, and risk report evidence — Person 5.
- [ ] Validation checks, integration results, and resolved issue log — Person 5.
- [ ] Final report and submission package — all five members.

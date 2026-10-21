# Graph Report - sql-server-tsql-study  (2026-10-21)

## Corpus Check
- 15 files · ~60,077 words
- Verdict: corpus is large enough that graph structure adds value.
- Unclassified: 2 file(s) not represented in the graph (top: (none) 2)

## Summary
- 410 nodes · 626 edges · 14 communities (13 shown, 1 thin omitted)
- Extraction: 86% EXTRACTED · 14% INFERRED · 0% AMBIGUOUS · INFERRED: 89 edges (avg confidence: 0.83)
- Token cost: 711,515 input · 0 output

## Community Hubs (Navigation)
- Practical Scenarios Mock
- Set Ops & Query Tuning
- Subquery Types
- LIKE Pattern Matching
- Forte Interview & AI Practice
- .NET / EF Core Interview Topics
- Tee-Time Scenarios (Perf & Integrity)
- Index, Dates & Bulk Load Q&A
- Booking Concurrency & Integrity
- T-SQL Cheat Sheet PDF
- Local vs Global Variables
- Live-Coding Mock Exercises
- Unpivoting & Anti-Joins
- SESSION_CONTEXT

## God Nodes (most connected - your core abstractions)
1. `Forte Group Live Coding & Mock Interview Practice Plan` - 30 edges
2. `SQL Mock Interview: Question Bank` - 23 edges
3. `Forte Group .NET & AI Practitioner Interview Prep Guide (Oct 2026)` - 15 edges
4. `Round 2 - Forte technical interview` - 13 edges
5. `Mock A - .NET Web API live coding` - 13 edges
6. `SQL Interview Q166: Types of Subquery` - 11 edges
7. `Compass artifact: Forte Group Interview Prep Guide (duplicate of Oct 2026 guide)` - 11 edges
8. `Mock D - SQL Server` - 11 edges
9. `Topics Covered Table` - 10 edges
10. `SQL Mock Interview: Practical Scenarios` - 10 edges

## Surprising Connections (you probably didn't know these)
- `Topics Covered Table` --references--> `T-SQL Interview Cheat Sheet (dark PDF)`  [EXTRACTED]
  README.md → source-database/tsql_cheatsheet_dark.pdf
- `Section 9: Performance Tuning (P9.1-P9.4)` --conceptually_related_to--> `SQL Server Query Optimization Guide`  [INFERRED]
  mock-interview-practical-scenarios.md → source-database/sql-server-query-optimization-guide.md
- `Mistake: LIKE on Dates (use ranges)` --semantically_similar_to--> `Sargable Predicates (no functions on indexed columns)`  [INFERRED] [semantically similar]
  source-database/186-pattern-matching-like.md → source-database/sql-server-query-optimization-guide.md
- `How to Use This Repo (answer first, then check)` --semantically_similar_to--> `Section 11: Full Mock Sessions A-E (timed, mixed)`  [INFERRED] [semantically similar]
  README.md → mock-interview-practical-scenarios.md
- `Topics Covered Table` --references--> `SQL Server Query Optimization Guide`  [EXTRACTED]
  README.md → source-database/sql-server-query-optimization-guide.md

## Import Cycles
- None detected.

## Hyperedges (group relationships)
- **Concurrent Tee-Time Booking Safety (atomic update, race, isolation, deadlock)** — mock_interview_practical_scenarios_usp_bookteetime, mock_interview_practical_scenarios_read_check_write_race, mock_interview_practical_scenarios_isolation_rcsi_snapshot, mock_interview_practical_scenarios_deadlock_lock_ordering, mock_interview_practical_scenarios_expire_holds_output [EXTRACTED 1.00]
- **Find Bad Data -> Fix Set-Based -> Add Constraint** — mock_interview_practical_scenarios_overbooking_counter_check, mock_interview_practical_scenarios_set_based_update_from, mock_interview_practical_scenarios_check_constraint_nocheck_trusted, mock_interview_practical_scenarios_filtered_unique_index [EXTRACTED 1.00]
- **Measure-Change-Measure Query Performance Techniques** — mock_interview_practical_scenarios_covering_index_design, mock_interview_practical_scenarios_sargability, mock_interview_practical_scenarios_parameter_sniffing, mock_interview_practical_scenarios_offset_vs_keyset_pagination [EXTRACTED 1.00]
- **Overbooking prevention under concurrency** — mock_interview_questions_read_check_write_race, mock_interview_questions_atomic_conditional_update, mock_interview_questions_usp_bookteetime, mock_interview_questions_xact_abort, mock_interview_questions_untrusted_check_constraint, mock_interview_questions_isolation_levels [EXTRACTED 1.00]
- **Sections 9-16 built on the tee-time booking schema** — mock_interview_questions_tee_time_booking_schema, mock_interview_questions_window_functions_gaps_islands, mock_interview_questions_transactions_error_handling_concurrency, mock_interview_questions_constraints_data_integrity, mock_interview_questions_index_design_storage, mock_interview_questions_query_tuning_sniffing_pagination, mock_interview_questions_isolation_levels_deadlocks, mock_interview_questions_dates_time_zones_numeric_traps, mock_interview_questions_bulk_loads_merge_log_code_review [EXTRACTED 1.00]
- **Patterns that prevent index seeks** — mock_interview_questions_sargability, mock_interview_questions_implicit_conversion, mock_interview_questions_like_wildcards, mock_interview_questions_at_time_zone, mock_interview_questions_composite_index_key_order [INFERRED 0.85]
- **Four Ways to Fetch Common Records** — source_database_sql_180_common_records_two_tables_intersect, source_database_sql_180_common_records_two_tables_inner_join_distinct, source_database_sql_180_common_records_two_tables_in_subquery, source_database_sql_180_common_records_two_tables_exists [EXTRACTED 1.00]
- **Recursive CTE Structure and Applications** — source_database_sql_employee_hierarchy_exercise_anchor_member, source_database_sql_employee_hierarchy_exercise_recursive_member, source_database_sql_employee_hierarchy_exercise_union_all, source_database_sql_employee_hierarchy_exercise_employeehierarchy, source_database_sql_server_query_optimization_guide_blockingchain [INFERRED 0.85]
- **Patterns That Defeat Index Seeks** — source_database_sql_server_query_optimization_guide_sargable_predicates, source_database_sql_server_query_optimization_guide_implicit_conversion, source_database_sql_server_query_optimization_guide_leading_wildcard_antipattern, source_database_186_pattern_matching_like_like_index_performance, source_database_186_pattern_matching_like_like_case_sensitivity [INFERRED 0.85]
- **Subquery Classification by What It Returns** — source_database_sql_subquery_types_scalar_subquery, source_database_sql_subquery_types_multi_row_subquery, source_database_sql_subquery_types_multi_column_subquery, source_database_sql_subquery_types_table_subquery [EXTRACTED 1.00]
- **Techniques to Retrieve the Row Holding the Per-Group Max** — source_database_tsql_duplicates_and_max_per_group_pattern_a_group_by_correlated_subquery, source_database_tsql_duplicates_and_max_per_group_pattern_b_cte_window_functions, source_database_sql_subquery_types_correlated_subquery, source_database_sql_subquery_types_row_number_tie_breaker, source_database_tsql_duplicates_and_max_per_group_ties_and_determinism [INFERRED 0.85]
- **Sharing Values Beyond a Local Variable's Scope** — source_database_tsql_local_vs_global_variables_stored_procedure_parameter, source_database_tsql_local_vs_global_variables_output_parameter, source_database_tsql_local_vs_global_variables_temp_table, source_database_tsql_local_vs_global_variables_global_temp_table, source_database_tsql_local_vs_global_variables_session_context [EXTRACTED 1.00]
- **Tee-time double booking defense pattern** — source_other_interviews_forte_group_senior_full_stack__net_engineer_and_ai_practitioner__interview_prep_guide__october_2026__tee_time_double_booking, source_other_interviews_forte_group_senior_full_stack__net_engineer_and_ai_practitioner__interview_prep_guide__october_2026__short_lived_holds, source_other_interviews_forte_group_senior_full_stack__net_engineer_and_ai_practitioner__interview_prep_guide__october_2026__idempotency_keys, source_other_interviews_forte_group_senior_full_stack__net_engineer_and_ai_practitioner__interview_prep_guide__october_2026__outbox_pattern, source_other_interviews_forte_group_senior_full_stack__net_engineer_and_ai_practitioner__interview_prep_guide__october_2026__utc_iana_time_zone [EXTRACTED 1.00]
- **Forte interview round sequence** — source_other_interviews_forte_group_senior_full_stack__net_engineer_and_ai_practitioner__interview_prep_guide__october_2026__round_1_recruiter_screen, source_other_interviews_forte_group_senior_full_stack__net_engineer_and_ai_practitioner__interview_prep_guide__october_2026__round_2_forte_technical_interview, source_other_interviews_forte_group_senior_full_stack__net_engineer_and_ai_practitioner__interview_prep_guide__october_2026__round_3_practical_live_coding, source_other_interviews_forte_group_senior_full_stack__net_engineer_and_ai_practitioner__interview_prep_guide__october_2026__round_4_client_interview, source_other_interviews_forte_group_senior_full_stack__net_engineer_and_ai_practitioner__interview_prep_guide__october_2026__final_offer_wrap_up [EXTRACTED 1.00]
- **Claude Code guardrailed AI-assisted workflow** — source_other_interviews_forte_live_coding_mock_plan_f1_write_claude_md, source_other_interviews_forte_live_coding_mock_plan_f2_hooks_and_permissions, source_other_interviews_forte_live_coding_mock_plan_f3_feature_with_claude_code, source_other_interviews_forte_live_coding_mock_plan_f4_review_ai_generated_pr, source_other_interviews_forte_live_coding_mock_plan_f5_mcp_setup [EXTRACTED 1.00]
- **Two approaches to find duplicates and max values over Sales** — source_database_tsql_cheatsheet_dark_group_by_correlated_subquery, source_database_tsql_cheatsheet_dark_cte_row_number, source_database_tsql_cheatsheet_dark_sales_table, source_database_tsql_cheatsheet_dark_window_functions [EXTRACTED 1.00]
- **Query optimization toolkit** — source_database_tsql_cheatsheet_dark_query_optimization, source_database_tsql_cheatsheet_dark_dbcc_cache_commands, source_database_tsql_cheatsheet_dark_sargability_killers, source_database_tsql_cheatsheet_dark_covering_index, source_database_tsql_cheatsheet_dark_execution_plan_warning_signs [EXTRACTED 1.00]
- **UNION ALL-based CTE patterns (unpivot and recursion)** — source_database_tsql_cheatsheet_dark_union_all, source_database_tsql_cheatsheet_dark_unpivot_union_all, source_database_tsql_cheatsheet_dark_recursive_cte, source_database_tsql_cheatsheet_dark_cte [INFERRED 0.75]

## Communities (14 total, 1 thin omitted)

### Community 0 - "Practical Scenarios Mock"
Cohesion: 0.06
Nodes (48): SQL Mock Interview: Practical Scenarios, Section 5: Common Records Between Tables - Student/Exam (P5.1-P5.4), Correlated vs Non-correlated Subqueries, CROSS APPLY (VALUES ...) vs Stacked UNION ALL, Section 1: Duplicates and Max Value per Group (P1.1-P1.5), Employees Self-Referencing Table (FK_Employees_Manager), First/Last Login Ordered by Date then Time (MIN/MAX trap), Section 11: Full Mock Sessions A-E (timed, mixed) (+40 more)

### Community 1 - "Set Ops & Query Tuning"
Cohesion: 0.06
Nodes (40): Full-Text Search / Trigram Indexes (pg_trgm), ILIKE (PostgreSQL case-insensitive LIKE), LIKE Case Sensitivity and Collation, LIKE Index Usage (prefix vs leading wildcard), EXCEPT / MINUS, EXISTS (correlated subquery), IN Subquery, NOT IN NULL Trap (prefer NOT EXISTS) (+32 more)

### Community 2 - "Subquery Types"
Cohesion: 0.06
Nodes (37): SQL Interview Q166: Types of Subquery, Missing Aliases Un-correlate the Query, ANY / ALL Operators, Correlated Subquery, Common Table Expression (CTE), Derived Table (subquery in FROM), Games/Scores Sample Database, Subquery in HAVING (+29 more)

### Community 3 - "LIKE Pattern Matching"
Cohesion: 0.06
Nodes (37): ESCAPE Clause (literal % or _), LIKE NULL Handling (LIKE '%' excludes NULL), LIKE Operator, NOT LIKE, % Wildcard (zero or more characters), Product Table (ProductCode, ESCAPE demo), Regular Expressions (REGEXP, ~, REGEXP_LIKE), SIMILAR TO / SQLite GLOB / MS Access wildcards (+29 more)

### Community 4 - "Forte Interview & AI Practice"
Cohesion: 0.09
Nodes (28): Compass artifact: Forte Group Interview Prep Guide (duplicate of Oct 2026 guide), AI Practitioner question bank, BRS Golf (UK tee sheet), Best practices for Claude Code (Anthropic docs), Anthropic Claude Partner Network (Select tier), Tee-time booking demo repo recommendation, Forte Group .NET & AI Practitioner Interview Prep Guide (Oct 2026), EZLinks product family (EZTee, EZSuite, EZEngage, TeeOff.com, EZGolf) (+20 more)

### Community 5 - ".NET / EF Core Interview Topics"
Cohesion: 0.07
Nodes (29): Code review of AI-generated booking PR, Glassdoor Forte interview reports, Parameter sniffing, React Redux click -> fetch -> render flow, Round 2 - Forte technical interview, Round 3 - Practical live coding / code review / AI-assisted exercise, SQL Server isolation levels (RCSI/SNAPSHOT/SERIALIZABLE), A2 - GET list with filter, sort, paging (+21 more)

### Community 6 - "Tee-Time Scenarios (Perf & Integrity)"
Cohesion: 0.10
Nodes (30): AT TIME ZONE Windows Names vs IANA Mapping, CHECK Constraint WITH NOCHECK / is_not_trusted, Covering Index Design ((CourseId, StartUtc) INCLUDE ...), Section 7: Data Integrity and Constraints (P7.1-P7.4), Deadlock (error 1205, system_health graph, consistent lock order fix), Expire Holds with OUTPUT (set-based, idempotent), Filtered Unique Index for Nullable Idempotency Key, Gaps & Islands (free consecutive tee-time windows) (+22 more)

### Community 7 - "Index, Dates & Bulk Load Q&A"
Cohesion: 0.08
Nodes (29): AT TIME ZONE (Windows zone names, DST) and UTC storage, Blocking vs deadlock, Section 16: Bulk Loads, MERGE, Transaction Log and SQL Code Review, Composite index key order (equality first, range/sort second), Correlated vs non-correlated subquery, Covering index (key vs INCLUDE columns), Section 15: Dates, Time Zones and Numeric Traps, GetCourseBookings SQL code review exercise (+21 more)

### Community 8 - "Booking Concurrency & Integrity"
Cohesion: 0.10
Nodes (26): Section 11: Constraints and Data Integrity, Denormalized counter BookedPlayers vs SUM(Players), Filtered (unique) index, Read-check-write race condition (overbooking), @@ROWCOUNT, Tee-time booking schema (Courses, TeeTimes, Golfers, Bookings, Payments), Section 10: Transactions, Error Handling and Concurrency, CHECK constraint WITH NOCHECK / untrusted (is_not_trusted) (+18 more)

### Community 9 - "T-SQL Cheat Sheet PDF"
Cohesion: 0.15
Nodes (16): T-SQL Interview Cheat Sheet (dark PDF), Correlated Subquery (TOP 1), Common Table Expression (CTE) Pattern, DBCC DROPCLEANBUFFERS / DBCC FREEPROCCACHE, Employees table hierarchy (EmployeeID, ManagerID), Execution Plan Warning Signs (Table Scan, Key Lookup, Sort, Hash Match, Fat arrows), Finding Duplicates & Max Values, GROUP BY + HAVING with Aggregates (+8 more)

### Community 10 - "Local vs Global Variables"
Cohesion: 0.11
Nodes (21): Section 3: Local and Global Variables, @@IDENTITY vs SCOPE_IDENTITY() vs IDENT_CURRENT, SET vs SELECT variable assignment, Q167: Local vs Global Variables (T-SQL), Batch Scope Ends at GO, Naming Own Variables with @@ Mistake, Dynamic SQL with sp_executesql and QUOTENAME, @@ERROR vs TRY...CATCH (+13 more)

### Community 11 - "Live-Coding Mock Exercises"
Cohesion: 0.15
Nodes (16): A10 - Typed HttpClient to payment API with resilience, A11 - JWT authorization policies, A12 - Integration tests with WebApplicationFactory + Testcontainers.MsSql, A1 - GET tee time by id, A3 - POST create booking with concurrency, A5 - DELETE cancel booking, A7 - Optimistic concurrency with ETag / If-Match, A8 - Hold expiry with BackgroundService (+8 more)

### Community 12 - "Unpivoting & Anti-Joins"
Cohesion: 0.14
Nodes (13): EXISTS / NOT EXISTS, Rewrite: LEFT JOIN ... IS NULL (anti-join), NOT IN with NULL Pitfall, Problem 1: Address Lines (MemberAddress), CTE for Organizing UNION ALL Blocks, De Morgan's Law, OR vs AND Exclusion Logic (exclude with OR, include with AND), Problem 2: Contact Phone Numbers (CustomerContacts) (+5 more)

## Ambiguous Edges - Review These
- `Student Table (id, studentname)` → `Student Table (StudentID, Name)`  [AMBIGUOUS]
  source-database/186-pattern-matching-like.md · relation: semantically_similar_to

## Knowledge Gaps
- **113 isolated node(s):** `Running the Examples (SSMS, Azure Data Studio, sqlfiddle, db<>fiddle)`, `First/Last Login Ordered by Date then Time (MIN/MAX trap)`, `Exclude NULL or Empty Values when Unpivoting`, `INTERSECT vs JOIN vs IN vs EXISTS`, `NOT IN vs NOT EXISTS (NULL safety)` (+108 more)
  These have ≤1 connection - possible missing edges or undocumented components. (Counts symbols only; 118 node(s) total have ≤1 connection when file, concept and rationale nodes are included.)
- **1 thin communities (<3 nodes) omitted from report** — run `graphify query` to explore isolated nodes.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `Forte Group Live Coding & Mock Interview Practice Plan` connect `Booking Concurrency & Integrity` to `Practical Scenarios Mock`, `Forte Interview & AI Practice`, `.NET / EF Core Interview Topics`, `Tee-Time Scenarios (Perf & Integrity)`, `Index, Dates & Bulk Load Q&A`, `Live-Coding Mock Exercises`?**
  _High betweenness centrality (0.478) - this node is a cross-community bridge._
- **Are the 2 inferred relationships involving `Forte Group .NET & AI Practitioner Interview Prep Guide (Oct 2026)` (e.g. with `Compass artifact: Forte Group Interview Prep Guide (duplicate of Oct 2026 guide)` and `Forte Group Live Coding & Mock Interview Practice Plan`) actually correct?**
  _`Forte Group .NET & AI Practitioner Interview Prep Guide (Oct 2026)` has 2 INFERRED edges - model-reasoned connections that need verification._
- **What connects `Running the Examples (SSMS, Azure Data Studio, sqlfiddle, db<>fiddle)`, `First/Last Login Ordered by Date then Time (MIN/MAX trap)`, `Exclude NULL or Empty Values when Unpivoting` to the rest of the system?**
  _113 weakly-connected nodes found - possible documentation gaps or missing edges._
- **Should `Practical Scenarios Mock` be split into smaller, more focused modules?**
  _Cohesion score 0.058069381598793365 - nodes in this community are weakly interconnected._
- **Why does `SQL Mock Interview: Question Bank` connect `Practical Scenarios Mock` to `Booking Concurrency & Integrity`, `Local vs Global Variables`, `Tee-Time Scenarios (Perf & Integrity)`, `Index, Dates & Bulk Load Q&A`?**
  _High betweenness centrality (0.367) - this node is a cross-community bridge._
- **Are the 2 inferred relationships involving `Round 2 - Forte technical interview` (e.g. with `Compass artifact: Forte Group Interview Prep Guide (duplicate of Oct 2026 guide)` and `Full mock interview scripts (#1 Forte technical, #2 client, #3 AI-assisted)`) actually correct?**
  _`Round 2 - Forte technical interview` has 2 INFERRED edges - model-reasoned connections that need verification._

### Low-confidence Hints
_AMBIGUOUS edges — the extractor was unsure. Verify before acting on these._

- **What is the exact relationship between `Student Table (id, studentname)` and `Student Table (StudentID, Name)`?**
  _Edge tagged AMBIGUOUS (relation: semantically_similar_to) - confidence is low._
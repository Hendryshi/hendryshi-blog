---
title: Different SQL JOIN Type
cover: https://img.hendryshi.com/i/2023/04/6431b69f23c17.webp
categories:
  - Tech
tags:
  - SQL
abbrlink: f0f14fb5
date: 2021-03-25 19:00:42
---

## Context
SQL Server performs sort, intersect, union, and difference operations using in-memory sorting and hash join technology. Using this type of query plan, SQL Server supports vertical table partitioning, sometimes called columnar storage.

SQL Server employs three types of join operations:

-   Nested loops joins (By default)
-   Merge joins
-   Hash joins

<!-- more -->

## Use default Join

If one join input is small (fewer than 10 rows) and the other join input is fairly large and indexed on its join columns

## Use Merge Join

If the two join inputs are not small but are sorted on their join column (for example, if they were obtained by scanning sorted indexes), a merge join is the fastest join operation. If both join inputs are large and the two inputs are of similar sizes, a merge join with prior sorting and a hash join offer similar performance. However, hash join operations are often much faster if the two input sizes differ significantly from each other. For more information, see Understanding Merge Joins.

## Use Hash Join

Hash joins can efficiently process large, unsorted, nonindexed inputs. They are useful for intermediate results in complex queries because: Intermediate results are not indexed (unless explicitly saved to disk and then indexed) and often are not suitably sorted for the next operation in the query plan.

[See more information in Microsoft document](https://docs.microsoft.com/en-us/previous-versions/sql/sql-server-2008/ms191426(v=sql.100)?redirectedfrom=MSDN)
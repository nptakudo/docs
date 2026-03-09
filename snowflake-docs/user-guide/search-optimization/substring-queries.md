---
title: "Speeding up substring and regular expression queries with search optimization"
url: "https://docs.snowflake.com/en/user-guide/search-optimization/substring-queries"
---

# Speeding up substring and regular expression queries with search optimization

[![Snowflake logo in black (no text)](../../_images/logo-snowflake-black.png)](../../_images/logo-snowflake-black.png) [Enterprise Edition Feature](../intro-editions)

This feature requires Enterprise Edition (or higher). To inquire about upgrading, please contact [Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).

Search optimization can improve the performance of queries with predicates that search for substrings or use
regular expressions in text or semi-structured data. For details on how substring searches work with semi-structured
data, see [Speeding up queries of semi-structured data with search optimization](semi-structured-queries).

The following sections provide more information about search optimization support for substring and regular
expression queries:

* [Enabling search optimization for substring and regular expression queries](#label-search-optimization-service-substring-support-setup)
* [Supported predicates](#label-search-optimization-service-substring-support-predicates)

## Enabling search optimization for substring and regular expression queries

To improve the performance of substring and regular expression queries on a table, use the
[ON SUBSTRING clause in the ALTER TABLE … ADD SEARCH OPTIMIZATION command](../../sql-reference/sql/alter-table.html#label-alter-table-searchoptimizationaction-add)
for specific columns.

For example:

CopyExpand

```
ALTER TABLE mytable ADD SEARCH OPTIMIZATION ON SUBSTRING(mycol);
```

Show lessSee more

Scroll to top

For more information, see [Enabling and disabling search optimization](enabling).

## Supported predicates

The search optimization service can improve the performance of queries with predicates that use:

* [LIKE](../../sql-reference/functions/like)
* [LIKE ANY](../../sql-reference/functions/like_any)
* [LIKE ALL](../../sql-reference/functions/like_all)
* [ILIKE](../../sql-reference/functions/ilike)
* [ILIKE ANY](../../sql-reference/functions/ilike_any)
* [CONTAINS](../../sql-reference/functions/contains)
* [ENDSWITH](../../sql-reference/functions/endswith)
* [STARTSWITH](../../sql-reference/functions/startswith)
* [SPLIT\_PART](../../sql-reference/functions/split_part)
* [RLIKE](../../sql-reference/functions/rlike)
* [REGEXP](../../sql-reference/functions/regexp)
* [REGEXP\_LIKE](../../sql-reference/functions/regexp_like)

The search optimization service can improve performance when searching for substrings that are five or more characters
long. (More selective substrings can result in better performance.) The search optimization service doesn’t
use search access paths for the following predicate because the substring is shorter than five characters:

CopyExpand

```
LIKE '%TEST%'
```

Show lessSee more

Scroll to top

For the following predicate, the search optimization service can optimize this query, using search access paths to search for the
substrings for `SEARCH` and `OPTIMIZED`. However, search access paths are not used for `IS` because the substring is shorter
than five characters.

CopyExpand

```
LIKE '%SEARCH%IS%OPTIMIZED%'
```

Show lessSee more

Scroll to top

For queries that use RLIKE, REGEXP, and REGEXP\_LIKE against text:

* The `subject` argument must be a TEXT column in a table that has search optimization enabled.
* The `pattern` argument must be a string constant.

For regular expressions, the search optimization service works best when:

* The pattern contains at least one substring literal that is five or more characters long.
* The pattern specifies that the substring should appear at least once.

For example, the following pattern specifies that `string` should appear one or more times in the subject:

CopyExpand

```
RLIKE '(string)+'
```

Show lessSee more

Scroll to top

The search optimization service can improve the performance of queries with the following patterns because each predicate
specifies that a substring of five or more characters must appear at least once. (Note that the first example uses a
[dollar-quoted string constant](../../sql-reference/data-types-text.html#label-dollar-quoted-string-constants) to avoid escaping the backslash characters.)

CopyExpand

```
RLIKE $$.*email=[\w\.]+@snowflake\.com.*$$
```

Show lessSee more

Scroll to top

CopyExpand

```
RLIKE '.*country=(Germany|France|Spain).*'
```

Show lessSee more

Scroll to top

CopyExpand

```
RLIKE '.*phone=[0-9]{3}-?[0-9]{3}-?[0-9]{4}.*'
```

Show lessSee more

Scroll to top

In contrast, search optimization does not use search access paths for queries with the following patterns:

* Patterns without any substrings:

  CopyExpand

  ```
  RLIKE '.*[0-9]{3}-?[0-9]{3}-?[0-9]{4}.*'
  ```

  Show lessSee more

  Scroll to top
* Patterns that only contain substrings shorter than five characters:

  CopyExpand

  ```
  RLIKE '.*tel=[0-9]{3}-?[0-9]{3}-?[0-9]{4}.*'
  ```

  Show lessSee more

  Scroll to top
* Patterns that use the alternation operator where one option is a substring shorter than five characters:

  CopyExpand

  ```
  RLIKE '.*(option1|option2|opt3).*'
  ```

  Show lessSee more

  Scroll to top
* Patterns in which the substring is optional:

  CopyExpand

  ```
  RLIKE '.*[a-zA-z]+(string)?[0-9]+.*'
  ```

  Show lessSee more

  Scroll to top

Even when the substring literals are shorter than five characters, the search optimization service can still improve query
performance if expanding the regular expression produces a substring literal that is five characters or longer.

For example, consider the pattern:

Expand

```
.*st=(CA|AZ|NV).*(-->){2,4}.*
```

Show lessSee more

Scroll to top

In this example:

* Although the substring literals (e.g. `st=`, `CA`, etc) are shorter than five characters, the search optimization service
  recognizes that the substring `st=CA`, `st=AZ`, or `st=NV` (each of which is five characters long) must appear in the text.
* Similarly, even though the substring literal `-->` is shorter than five characters, the search optimization service determines
  that the substring `-->-->` (which is longer than five characters) must appear in the text.

The search optimization service can use search access paths to match these substrings, which can improve the performance of the
query.

---
title: Excel tips - VLOOKUP & MATCH
cover: https://img.hendryshi.com/i/2023/04/6431c3c0e10b6.webp
categories:
  - Tech
tags:
  - Excel
abbrlink: 3759b196
date: 2022-01-08 23:39:00
---

## VLOOKUP

VLOOKUP is an Excel function to look up data in a table organized vertically. 

### Syntax

```
=VLOOKUP (lookup_value, table_array, col_index, [range_lookup])
```
<!-- more -->

### Arguments

- lookup_value: The value to look for in the first column of a table.
- table_array: The table from which to retrieve a value.
- col_index: The column in the table from which to retrieve a value.
- range_lookup: [optional] TRUE = approximate match (default). FALSE = exact match.

### Example1: Exact Match

![vlookupExact](https://img.hendryshi.com/i/2023/04/6431c3d70ce70.webp)

In this example, the range_lookup argument = "False", if in the table we cannot find the "Peach", then it will return "N/A". We can use "IFERROR(VLOOKUP(H4,B3:F8,3,0),"Not found")" to avoid the "N/A".

### Example2: Approximate match

![vlookupAppro](https://img.hendryshi.com/i/2023/04/6431c3dbbd1c9.webp)

In this example, we use the approximate match: 10006 is not exists in the table, so vlookup return the value of "10005" as the result.




## MATCH

MATCH is an excel function used to locate the position of a lookup value in a row, column, or table. It is always used with function VLOOKUP to locate the index value

### Syntax

```
=MATCH (lookup_value, lookup_array, [match_type])
```

### Arguments

- lookup_value: The value to match in lookup_array.
- lookup_array: A range of cells or an array reference.
- match_type:  [optional] 1 = exact or next smallest (default), 0 = exact match, -1 = exact or next largest.

### Example1: Exact match

![matchExact](https://img.hendryshi.com/i/2023/04/6431c3e7ee259.webp)

If "Charles Monaghan" is not in the list, the function will return "N/A"

### Example2: Approximate match ASC

![matchAppro1](https://img.hendryshi.com/i/2023/04/6431c3ed3a7e8.webp)

{% note info modern no-icon %}
**If we want to use the "match_type = 1", the list must be order by ascendant**
{% endnote %}


### Example3: Approximate match DESC

![matchAppro2](https://img.hendryshi.com/i/2023/04/6431c45b2bc63.webp)

{% note info modern no-icon %}
**If we want to use the "match_type = -1", the list must be order by descendant**
{% endnote %}

{% note modern no-icon %}
**References:**
https://zh-cn.extendoffice.com/excel/functions/excel-match-function.html
https://exceljet.net/excel-functions/excel-match-function
{% endnote %}


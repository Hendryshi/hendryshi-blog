---
title: Some tips when using GROUP BY in SQL Server
toc: true
thumbnail: /2020/05/12/SQL-Server-Study-GROUPBY/cover.png
tags:
  - SQL Server
categories:
  - SQL
abbrlink: 3ee492a7
date: 2020-05-12 22:05:07
update: 2020-05-12 22:05:07
---

## Using ROLLUP

The GROUP UP clause is used to group the results of aggregate functions according to a specified column. However, the GROUP BY clause doesn't perform aggregate operations on multiple levels of a hierarchy. In that case, you can use the `ROLL UP` to calculate the subtotals and grand totals for a set of columns.

See example below:

```sql
    SELECT R.nameNode AS Register, COUNT(*) AS Mark_Numbers
    FROM C_Node R 
    JOIN C_ClientMark CM ON CM.idNode = R.idNode
    WHERE R.isActive = 1 AND CM.isActive = 1
    GROUP BY ROLLUP (R.nameNode)
```
<!-- more -->

Results:

![](RollUpResult.png)

If you group by two different criterion, the result will give you the subtotal according to your criterion's order. See example below:

```sql
    SELECT brand, category, SUM (sales) sales
    FROM sales.sales_summary
    GROUP BY ROLLUP(category, brand)
```



Results:

![](RollUpResult2.png)

In this example, the query assumes that there is a hierarchy between brand & category, which is the brand > category.

{% raw %}<article class="message is-info"><div class="message-body">{% endraw %}
**Note**: 

If you change the order of brand and category, the result will be different
{% raw %}</div></article>{% endraw %}

See the following query:

```sql
    SELECT brand, category, SUM (sales) sales
    FROM sales.sales_summary
    GROUP BY ROLLUP(brand, category)
```

Results:

![](RollUpResult3.png)

## Using Grouping SETS (分组集)

A grouping set is a group of columns by which you group. It has the similar result as the `GROUP BY ROLL UP`.

When you use `GROUP BY` by default, it defines a grouping set (brand):

```sql
    SELECT brand, SUM (sales) sales
    FROM sales.sales_summary
    GROUP BY brand -- GROUP BY GROUPING SETS (brand)
```

When you use `GROUP BY ROLLUP(brand, category)`, it defines a grouping sets ((brand, category), (brand), ()): 

```sql
    SELECT brand, SUM (sales) sales
    FROM sales.sales_summary
    GROUP BY ROLLUP(brand, category) 
    -- GROUP BY GROUPING SETS ((brand, category), (brand), ()) 
```

{% raw %}<article class="message is-success"><div class="message-body">{% endraw %}
**conclusion**: 

we can say that the `GROUP BY ROLLUP` is a special grouping sets.
{% raw %}</div></article>{% endraw %}

---
layout: post
title:  "Arcadia Data join expressions"
date:   2017-07-27
categories: ArcadiaData
---

Recently, I've worked a bit with [Arcadia Data](https://www.arcadiadata.com/) - a straightforward tool to create engaging visualisations and dashboards straight from the data held in Hive databases on a Hadoop cluster. One of the key features of Arcadia is the ability to define your own datasets easily with join conditions discovery done automatically for you.

Sometimes though, you need a more complex join condition than a straightforward `A.id` = `B.id`.

## Custom join expressions

To add a custom join expression, go to your dataset, turn on the edit mode, click on a join and the click the button seen below:

![join expression button](/images/arcadia-join-expressions/join_expr.png)

Ease your life by ticking the "Autocomplete on" checkbox.

Now you can add join conditions as you please, starting with simple equality joins:

    [id] = [second_table id]

Table name isn't needed for the main table in the join.

Cast a field to a string before joining:

    [string_id] = cast([second_table num_id] as string)

Join on case-insensitive strings:

    lower([inconsistent_casing_id]) = lower([second_table other_inconsistently_cased_id])

Or do some REGEXP goodness:

    [prefixed_id] REGEXP CONCAT('[^.]*\\.', [second_table non_prefixed_id], '$')


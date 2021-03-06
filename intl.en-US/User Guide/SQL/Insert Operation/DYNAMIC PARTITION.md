# DYNAMIC PARTITION {#concept_b1p_qdb_wdb .concept}

To ‘insert  overwrite’ into a partition table, you can specify the partition value in the statement. It can also be realized in a more flexible way, to specify a partition column in a partition table but not give the value. Correspondingly, the columns in Select clause are used to specify these partition values.

**Statement format**:

```
insert overwrite table tablename partition (partcol1, partcol2 ...) select_statement from from_statement;
```

**Note:** 

-   In the ‘select\_statement’ field, the following field provides the dynamic partition value for the target table. If the target table only has a one-level dynamic partition, the last field value of select\_statement is the dynamic partition value of the target table.
-   Currently, a single worker can only output up to 512 dynamic partitions in a distributed environment, otherwise it leads to abnormality.
-   Currently, any dynamic partition SQL cannot generate more than 2,000 dynamic partitions; otherwise it causes abnormality.
-   The value of dynamic partition cannot be NULL, and also does not support special characters and Chinese, otherwise exception is thrown. The exception is as follows:

    ```
    FAILED: ODPS-0123031:Partition exception - invalid dynamic partition value:
                  province=xxx
    ```

-   If the destination table has multiple-level partitions, it is allowed to specify parts of partitions to be static partitions through ‘Insert’ statement, but the static partitions must be advanced partitions.

**A simple example to explain dynamic partition is as follows**:

```
create table total_revenues (revenue bigint) partitioned by (region string);
    insert overwrite table total_revenues partition(region)
        select total_price as revenue, region
            from sale_detail;
```

As preceding mentioned, user is unable to know which partitions are generated before running SQL. Only after the Select statement running ends, user can confirm which partitions have been generated through the value of ‘region’. This is why the partition is called **Dynamic Partition**.

**Other Examples**:

```
create table sale_detail_dypart like sale_detail; --Create target table.
```

--Example 1:

```
insert overwrite table sale_detail_dypart partition (sale_date, region)
select shop_name,customer_id,total_price,sale_date,region from sale_detail;
    -- Return successfully.
```

-   In ‘sales\_detail’ table, the value of the sale\_date determines the sales\_date partition value of the target table, and the value of the region determines the region partition value of the target table.
-   **In a dynamic partition, the correspondence between the select\_statement field and the dynamic partition of the target table is determined by the order of the fields.** In this example, if the Select statement is written as

    ```
    select shop_name,customer_id,total_price,region,sale_date from
              sale_detail;
    ```

    the region value determines the sale\_date partition value of the target table, and the value of  sale\_date determines the region partition value of the target table.


--Example 2:

```
insert overwrite table sale_detail_dypart partition (sale_date='2013', region)
        select shop_name,customer_id,total_price,region from sale_detail;
    -- Return successfully; multiple partitions; specify a secondary partition.
```

--Example 3:

```
insert overwrite table sale_detail_dypart partition (sale_date='2013', region)
        select shop_name,customer_id,total_price from sale_detail;
    -- Return failure information. When inserting a dynamic partition, the dynamic partition column must appear in Select list.
```

--Example 4:

```
insert overwrite table sales partition (region='china', sale_date)
select shop_name,customer_id,total_price,region from sale_detail;
    -- Return failure information. User cannot specify the lowsubpartition only, but needs to insert advanced partition dynamically.
```

When the old version of MaxCompute performs dynamic partitioning, if the partition column type is not exactly the same as the column type in the corresponding select list, an error is reported.  MaxCompute 2.0 supports implicit conversion, an example is as follows:

```
create table parttable(a int, b double) partitioned by (p string);
insert into parttable partition(p) select key, value, current_timestmap() from src;
select * from parttable;
```

The result is as follows:

|a|b|c|
|:-|:-|:-|
|0|NULL|2017-01-23 22:30:47.130406621|
|0|NULL|2017-01-23 22:30:47.130406621|


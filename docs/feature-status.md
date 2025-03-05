# Antalya Feature Status

## Antalya Data Lake Feature Roadmap

Antalya operates on Iceberg tables with Parquet files. The following
table shows implemented and planned new features. Look at the 
[Command and Configuration Reference](./reference.md) for documentation on how
to use features.

(This roadmap format is provisional.)

* Parquet read performance
  * [x] Add parquet bloom filters support https://github.com/ClickHouse/ClickHouse/pull/62966
  * [x] Bloom filter and min/max evaluation
  * [x] Implement Parquet metadata cache
  * [x] Utilize Parquet metadata cache for file skipping
  * [ ] Parquet native reader improvements
  * [ ] Clickhouse scanning huge amount of data while querying parquet files even in the latest build which has predicate pushdown https://github.com/ClickHouse/ClickHouse/issues/54977
* Iceberg Reads
  * [ ] Apply Iceberg/Delta partition information for file skipping []
  * [x] Read Iceberg data with iceberg() Function
  * [x] Read Iceberg data with iceberg table engine
  * [x] Read specific iceberg snapshot []
  * [ ] Add setting to query Iceberg tables as of a specific timestamp https://github.com/ClickHouse/ClickHouse/pull/71072
  * [ ] Positional deletes in Iceberg https://github.com/ClickHouse/ClickHouse/pull/74146
* Swarm clusters
  * [x] Use swarm cluster with s3()
  * [x] Use swarm cluster with icebergS3Cluster()
  * [ ] Use swarm cluster with iceberg() function
  * [ ] Use swarm cluster with iceberg table engine
  * [x] Auxiliary keeper for swarm cluster auto-discovery
  * [ ] Discover any swarm cluster in Keeper
  * [x] Swarm cluster demo in Docker Compose
  * [ ] Swarm cluster demo in Kubernetes
  * [ ] Swarm cluster autoscaling
* Catalog support
  * [x] Integrate with Iceberg REST Catalog https://github.com/ClickHouse/ClickHouse/pull/71542
  * [ ] AWS S3 table bucket catalog
  * [ ] Unity catalog
  * [ ] Polaris catalog
  * [ ] AWS Glue catalog
* Iceberg Writes
  * [ ] Iceberg CREATE TABLE 
  * [ ] Iceberg ALTER TABLE
  * [ ] Parquet multi-chunk upload 
  * [ ] ALTER TABLE MOVE to external table
  * [ ] TTL MOVE to external table
  * [ ] Merge Engine with TTL
  * [ ] Tiered table engine for Iceberg
  * [ ] Insert to Iceberg table
* Iceberg Merges
  * [ ] Merge Parquet files in external table

See also the [ClickHouse Upstream 2025 Roadmap](https://github.com/ClickHouse/ClickHouse/issues/74046). 
In general any opean source data lake features in the roadmap will be added to Antalya. 

## Known Bugs

### Iceberg table and database engines in ClickHouse/Antalya

1. Bug in path processing: https://github.com/ClickHouse/ClickHouse/issues/76439
2. Insert is permitted but does not work correctly: https://github.com/ClickHouse/ClickHouse/issues/76165 
3. If we create two tables with different schema in one namespace and one
   database, we will only correctly read data from one that was populated
   lately, other table in ClickHouse will show null values for all rows and
   columns. 
   - If table has the same structure, we will always see same rows
     in both tables, even if they were actually populated with different data
   - This is probably also because of https://github.com/ClickHouse/ClickHouse/issues/76439
4.  Iceberg engine and table function show old data after Iceberg table re-creation; 
    random metadata.json objects is chosen.  https://github.com/ClickHouse/ClickHouse/issues/75187 
5.  Iceberg unable to read column with name includes special characters
    https://github.com/ClickHouse/ClickHouse/issues/73829 

## Features that work as expected (Surprise!)

The following features have been verified by Altinity QA. 

1. RBAC
    1. row policy 
    2. column grants
2. SELECT
    1.  WHERE clause
    2. GROUP BY
    3. HAVING
    4. LIMIT
    5. DISTINCT
    6. Joins
3. ALTER TABLE COMMENT COLUMN (only for Iceberg table engine)
4. SELECT after changing schema
5. SELECT after deleting rows

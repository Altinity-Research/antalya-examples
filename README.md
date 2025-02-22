<a href="https://altinity.com/slack">
  <img src="https://img.shields.io/static/v1?logo=slack&logoColor=959DA5&label=Slack&labelColor=333a41&message=join%20conversation&color=3AC358" alt="AltinityDB Slack" />
</a>

# Altinity Antalya Examples Project

Altinity Antalya is a new branch of ClickHouse designed to integrate
real-time analytic query with data lakes. 

## Goals of Antalya

* Adapt ClickHouse to use Iceberg tables as shared storage.
* Enable ClickHouse clusters to extend existing tables onto unlimited
Iceberg storage.  
* Support novel analytic architectures that integrate ClickHouse
with tools like Spark and pyiceberg.  
* Scale ingest, compaction, transformation, and query easily and 
independently.  
* Allow users to query native ClickHouse and shared Iceberg data 
seamlessly using a single SQL connection.
* Simplify backup and DR by leveraging Iceberg snapshots.
* Maintain full compability with upstream ClickHouse features and
bug fixes.

Antalya is licensed under Apached 2.0. There are no feature hold-backs. 

This project provides documentation as well as working code examples 
to help you use and contribute to Antalya. 

## Quick Start

See the [Docker Quick Start](./docker/README.md) to try out Antalya in
a few minutes using Docker.

## Antalya Binaries

### Packages

Antalya clickhouse server and keeper packages are available on the 
[builds.altinity.cloud](https://builds.altinity.cloud/) page. Scan to the last 
section to find them. 

### Containers

Antalya clickhouse server and clickhouse keeper containers are available
on Docker Hub. To start Antalya run the following Docker commands:

```
docker run altinity/clickhouse-server:24.12.2.20101.altinityantalya
docker run altinity/clickhouse-keeper:24.12.2.20101.altinityantalya
```

## Documentation

Look in the docs directory for current documentation. 

* [Antalya Concepts Guide](docs/concepts.md) 
* [Antalya Feature Status](docs/feature-status.md) 
* [Command and Configuration Reference](docs/reference.md)

## Code

To access Antalya code run the following commands. 

```
git clone git@github.com:Altinity/ClickHouse.git Antalya-Clickhouse
cd Antalya-ClickHouse
git checkout project-antalya-24.12.2
```

We will be changing the active Antalya branch name to be more 
memory-friendly shortly. 

## Building

Follow ClickHouse build instructions. 

## Contributing

We welcome contributions. We're setting up procedures for community
contribution now. Please contact us to find out how to join the project.

## Community Support

Join the [AltinityDB Slack Workspace](https://altinity.com/slack) or 
[log an issue](https://github.com/Altinity-Research/antalya-examples/issues) to get help. 

## Commercial Support

Altinity is the primary maintainer of Antalya. It is the basis of our data 
lake-enabled Altinity.Cloud and is also used in self-managed installations. 
Altinity offers a range of services related to ClickHouse and data lakes. 

- [Official website](https://altinity.com/) - Get a high level overview of Altinity and our offerings.
- [Altinity.Cloud](https://altinity.com/cloud-database/) - Run ClickHouse in our cloud or yours.
- [Altinity Support](https://altinity.com/support/) - Get Enterprise-class support for ClickHouse.
- [Slack](https://altinity.com/slack) - Talk directly with ClickHouse users and Altinity devs.
- [Contact us](https://hubs.la/Q020sH3Z0) - Contact Altinity with your questions or issues.
- [Free consultation](https://hubs.la/Q020sHkv0) - Get a free consultation with a ClickHouse expert today.

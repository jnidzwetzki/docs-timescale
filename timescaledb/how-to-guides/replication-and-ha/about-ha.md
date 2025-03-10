---
title: High availability
excerpt: Strategies for increasing redundancy and resilience of your database
keywords: [high availability, backup, replicas, failover]
tags: [redundancy]
---

# High availability (HA)
High availability (HA) is achieved by increasing redundancy and
resilience. To increase redundancy, parts of the system are replicated, so that
they are on standby in the event of a failure. To increase resilience, recovery
processes switch between these standby resources as quickly as possible.

<highlight type="cloud" button="Try Timescale Cloud for free" header="Enable high availability in one click">
Timescale Cloud simplifies the process of configuring and maintaining high availability for your databases.
You can create replicas with a single click and enjoy automatic recovery and fail-over for all your 
replicated instances (including multiple availability zones). 
[Learn more](https://docs.timescale.com/cloud/latest/service-operations/replicas/#create-a-database-replica) 
about HA replicas on Timescale Cloud.
</highlight>

## Backups
For some systems, recovering from backup alone can be a suitable availability
strategy. 

For more information about backups in self-hosted TimescaleDB, see the
[backup and restore section][db-backup] in the TimescaleDB documentation.

For more information about backups in Timescale Cloud, see
the [backup and restore section][cloud-backup] in the Cloud documentation.

## Storage redundancy
Storage redundancy refers to having multiple copies of a database's data files.
If the storage currently attached to a PostgreSQL instance corrupts or otherwise
becomes unavailable, the system can replace its current storage with one of the
copies. 

## Instance redundancy
Instance redundancy refers to having replicas of your database running
simultaneously. In the case of a database failure, a replica is an up-to-date,
running database that can take over immediately.

## Zonal redundancy
While the public cloud is highly reliable, entire portions of the cloud can be
unavailable at times. Timescale Cloud does not protect against Availability Zone
failures unless the user is using HA replicas. We do not currently offer
multi-cloud solutions or protection from an AWS Regional failure.

For more information about HA replicas in Timescale Cloud, see
the [high availability section][cloud-ha] in the Cloud documentation.

## Replication
TimescaleDB supports replication using PostgreSQL's built-in
[streaming replication][postgres-streaming-replication-docs]. Using
[logical replication][postgres-logrep-docs] with TimescaleDB is not recommended,
as it requires schema synchronization between the primary and replica nodes and
replicating partition root tables, which are
[not currently supported][postgres-partition-limitations].

PostgreSQL achieves streaming replication by having replicas continuously stream
the WAL from the primary database. See the official
[replication documentation](https://www.postgresql.org/docs/current/static/warm-standby.html#STREAMING-REPLICATION)
for details. For more information about how PostgreSQL implements Write-Ahead
Logging, see their
[WAL Documentation](https://www.postgresql.org/docs/current/static/wal-intro.html).

## Failover
PostgreSQL offers failover functionality where a replica is promoted to primary
in the event of a failure on the primary. This is done using
[pg_ctl][pgctl-docs] or the `trigger_file`, but it does not provide
out-of-the-box support for automatic failover. Read more in the PostgreSQL
[failover documentation][failover-docs]. [Patroni][patroni-github] offers a
configurable high availability solution with automatic failover functionality.

[cloud-backup]: /cloud/:currentVersion:/backup-restore-cloud/
[cloud-ha]: /cloud/:currentVersion:/high-availability/
[db-backup]: /timescaledb/:currentVersion:/how-to-guides/backup-and-restore/
[failover-docs]: https://www.postgresql.org/docs/current/static/warm-standby-failover.html
[patroni-github]: https://github.com/zalando/patroni
[pgctl-docs]: https://www.postgresql.org/docs/current/static/app-pg-ctl.html
[postgres-logrep-docs]: https://www.postgresql.org/docs/current/static/logical-replication.html
[postgres-partition-limitations]: https://www.postgresql.org/docs/current/static/logical-replication-restrictions.html
[postgres-streaming-replication-docs]: https://www.postgresql.org/docs/current/static/warm-standby.html#STREAMING-REPLICATION

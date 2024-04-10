---
title: Limits
description: This article describes limits in Azure Database for PostgreSQL - Flexible Server, such as number of connection and storage engine options.
author: varun-dhawan
ms.author: varundhawan
ms.reviewer: kabharati
ms.service: postgresql
ms.subservice: flexible-server
ms.topic: conceptual
ms.date: 2/1/2024
---

# Limits in Azure Database for PostgreSQL - Flexible Server

[!INCLUDE [applies-to-postgresql-flexible-server](../includes/applies-to-postgresql-flexible-server.md)]

The following sections describe capacity and functional limits in Azure Database for PostgreSQL flexible server. If you'd like to learn about resource (compute, memory, storage) tiers, see the [compute and storage](concepts-compute-storage.md) article.

## Maximum connections

Below, you'll find the _default_ maximum number of connections for each pricing tier and vCore configuration. Please note, Azure Database for PostgreSQL flexible server reserves 15 connections for physical replication and monitoring of the Azure Database for PostgreSQL flexible server instance. Consequently, the `max user connections` value listed in the table is reduced by 15 from the total `max connections`.

|SKU Name                                 |vCores|Memory Size|Max Connections|Max User Connections|
|-----------------------------------------|------|-----------|---------------|--------------------|
|**Burstable**                            |      |           |               |                    |
|B1ms                                     |1     |2 GiB      |50             |35                  |
|B2s                                      |2     |4 GiB      |429            |414                 |
|B2ms                                     |2     |8 GiB      |859            |844                 |
|B4ms                                     |4     |16 GiB     |1718           |1703                |
|B8ms                                     |8     |32 GiB     |3437           |3422                |
|B12ms                                    |12    |48 GiB     |5000           |4985                |
|B16ms                                    |16    |64 GiB     |5000           |4985                |
|B20ms                                    |20    |80 GiB     |5000           |4985                |
|**General Purpose**                      |      |           |               |                    |
|D2s_v3 / D2ds_v4 / D2ds_v5 / D2ads_v5    |2     |8 GiB      |859            |844                 |
|D4s_v3 / D4ds_v4 / D4ds_v5 / D4ads_v5    |4     |16 GiB     |1718           |1703                |
|D8s_v3 / D8ds_V4 / D8ds_v5 / D8ads_v5    |8     |32 GiB     |3437           |3422                |
|D16s_v3 / D16ds_v4 / D16ds_v5 / D16ads_v5|16    |64 GiB     |5000           |4985                |
|D32s_v3 / D32ds_v4 / D32ds_v5 / D32ads_v5|32    |128 GiB    |5000           |4985                |
|D48s_v3 / D48ds_v4 / D48ds_v5 / D48ads_v5|48    |192 GiB    |5000           |4985                |
|D64s_v3 / D64ds_v4 / D64ds_v5 / D64ads_v5|64    |256 GiB    |5000           |4985                |
|D96ds_v5 / D96ads_v5                     |96    |384 GiB    |5000           |4985                |
|**Memory Optimized**                     |      |           |               |                    |
|E2s_v3 / E2ds_v4 / E2ds_v5 / E2ads_v5    |2     |16 GiB     |1718           |1703                |
|E4s_v3 / E4ds_v4 / E4ds_v5 / E4ads_v5    |4     |32 GiB     |3437           |3422                |
|E8s_v3 / E8ds_v4 / E8ds_v5 / E8ads_v5    |8     |64 GiB     |5000           |4985                |
|E16s_v3 / E16ds_v4 / E16ds_v5 / E16ads_v5|16    |128 GiB    |5000           |4985                |
|E20ds_v4 / E20ds_v5 / E20ads_v5          |20    |160 GiB    |5000           |4985                |
|E32s_v3 / E32ds_v4 / E32ds_v5 / E32ads_v5|32    |256 GiB    |5000           |4985                |
|E48s_v3 / E48ds_v4 / E48ds_v5 / E48ads_v5|48    |384 GiB    |5000           |4985                |
|E64s_v3 / E64ds_v4 / E64ds_v5 / E64ads_v5|64    |432 GiB    |5000           |4985                |
|E96ds_v5 / E96ads_v5                     |96    |672 GiB    |5000           |4985                |

> [!NOTE]
> The reserved connection slots, presently at 15, could change. We advise regularly verifying the total reserved connections on the server. This is calculated by summing the values of 'reserved_connections' and 'superuser_reserved_connections' server parameters. The maximum available user connections is `max_connections - (reserved_connections + superuser_reserved_connections`).

> [!NOTE]
> That default value for the max_connections server parameter is calculated when the instance of Azure Database for PostgreSQL Flexible Server is first provisioned, based on the SKU name selected for its compute. Any subsequent changes of SKU to the compute supporting that flexible server, won't have any effect on the currently set neither on the default value chosen for max_connections server parameter of that instance. Therefore it is recommended that, whenever you change the SKU assigned to an instance, you also adjust the currently set value for the max_connections parameter as per the values provided in the table above.


### Changing the max_connections value

When you first set up your Azure Postgres Flexible Server, it automatically decides the highest number of connections it can handle concurrently. This number is based on your server's configuration and cannot be changed.
 
However, you can adjust how many connections are allowed at any given time. To do this, change the 'max_connections' setting. Remember, after you change this setting, you'll need to restart your server for the new limit to start working.

> [!CAUTION]
> While it is possible to increase the value of `max_connections` beyond the default setting, it is not advisable. The rationale behind this recommendation is that instances may encounter difficulties when the workload expands and demands more memory. As the number of connections increases, memory usage also rises. Instances with limited memory may face issues such as crashes or high latency. Although a higher value for `max_connections` might be acceptable when most connections are idle, it can lead to significant performance problems once they become active. Instead, if you require additional connections, we suggest utilizing pgBouncer, Azure's built-in connection pool management solution, in transaction mode. To start, it is recommended to use conservative values by multiplying the vCores within the range of 2 to 5. Afterward, carefully monitor resource utilization and application performance to ensure smooth operation. For detailed information on pgBouncer, please refer to the [PgBouncer in Azure Database for PostgreSQL - Flexible Server](concepts-pgbouncer.md).

When connections exceed the limit, you may receive the following error:

`FATAL:  sorry, too many clients already.`

When using Azure Database for PostgreSQL flexible server for a busy database with a large number of concurrent connections, there may be a significant strain on resources. This strain can result in high CPU utilization, particularly when many connections are established simultaneously and when connections have short durations (less than 60 seconds). These factors can negatively impact overall database performance by increasing the time spent on processing connections and disconnections. It's important to note that each connection in Azure Database for PostgreSQL flexible server, regardless of whether it is idle or active, consumes a significant amount of resources from your database. This consumption can lead to performance issues beyond high CPU utilization, such as disk and lock contention. The topic is discussed in more detail in the PostgreSQL Wiki article on the [Number of Database Connections](https://wiki.postgresql.org/wiki/Number_Of_Database_Connections). To learn more, visit [Identify and solve connection performance in Azure Database for PostgreSQL flexible server](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/identify-and-solve-connection-performance-in-azure-postgres/ba-p/3698375).

## Functional limitations

### Scale operations

- At this time, scaling up the server storage requires a server restart.
- Server storage can only be scaled in 2x increments, see [Compute and Storage](concepts-compute-storage.md) for details.
- Decreasing server storage size is currently not supported. The only way to do is [dump and restore](../howto-migrate-using-dump-and-restore.md) it to a new Azure Database for PostgreSQL flexible server instance.
   
### Storage

- Once configured, storage size can't be reduced. You have to create a new server with desired storage size, perform manual [dump and restore](../howto-migrate-using-dump-and-restore.md) and migrate your database(s) to the new server.
- When the storage usage reaches 95% or if the available capacity is less than 5 GiB whichever is more, the server is automatically switched to **read-only mode** to avoid errors associated with disk-full situations. In rare cases, if the rate of data growth outpaces the time it takes to switch to read-only mode, your Server may still run out of storage. You can enable storage autogrow to avoid these issues and automatically scale your storage based on your workload demands.
- We recommend setting alert rules for `storage used` or `storage percent` when they exceed certain thresholds so that you can proactively take action such as increasing the storage size. For example, you can set an alert if the storage percentage exceeds 80% usage.
- If you're using logical replication, then you must drop the logical replication slot in the primary server if the corresponding subscriber no longer exists. Otherwise, the WAL files accumulate in the primary filling up the storage. If the storage threshold exceeds certain threshold and if the logical replication slot isn't in use (due to a non-available subscriber), Azure Database for PostgreSQL flexible server automatically drops that unused logical replication slot. That action releases accumulated WAL files and avoids your server becoming unavailable due to storage getting filled situation. 
- We don't support the creation of tablespaces, so if you're creating a database, don’t provide a tablespace name. Azure Database for PostgreSQL flexible server uses the default one that is inherited from the template database. It's unsafe to provide a tablespace like the temporary one because we can't ensure that such objects will remain persistent after server restarts, HA failovers, etc.
   
### Networking

- Moving in and out of VNET is currently not supported.
- Combining public access with deployment within a VNET is currently not supported.
- Firewall rules aren't supported on VNET, Network security groups can be used instead.
- Public access database servers can connect to the public internet, for example through `postgres_fdw`, and this access can't be restricted. VNET-based servers can have restricted outbound access using Network Security Groups.

### High availability (HA)

- See [HA Limitations documentation](concepts-high-availability.md#high-availability---limitations).

### Availability zones

- Manually moving servers to a different availability zone is currently not supported. However, using the preferred AZ as the standby zone, you can enable HA. Once established, you can fail over to the standby and then disable HA.

### Postgres engine, extensions, and PgBouncer

- Postgres 10 and older aren't supported as those are already retired by the open-source community. If you must use one of these versions, you need to use the [Azure Database for PostgreSQL single server](../overview-single-server.md) option, which supports the older major versions 9.5, 9.6 and 10.
- Azure Database for PostgreSQL flexible server supports all `contrib` extensions and more. Please refer to [PostgreSQL extensions](/azure/postgresql/flexible-server/concepts-extensions).
- Built-in PgBouncer connection pooler is currently not available for Burstable servers.
   
### Stop/start operation

- Once you stop the Azure Database for PostgreSQL flexible server instance, it automatically starts after 7 days. 

### Scheduled maintenance

- You can change custom maintenance window to any day/time of the week. However, any changes made after receiving the maintenance notification will have no impact on the next maintenance. Changes only take effect with the following monthly scheduled maintenance.
   
### Backing up a server

- Backups are managed by the system, there's currently no way to run these backups manually. We recommend using `pg_dump` instead.
- The first snapshot is a full backup and consecutive snapshots are differential backups. The differential backups only back up the changed data since the last snapshot backup. For example, if the size of your database is 40 GB and your provisioned storage is 64 GB, the first snapshot backup will be 40 GB. Now, if you change 4 GB of data, then the next differential snapshot backup size will only be 4 GB. The transaction logs (write ahead logs - WAL) are separate from the full/differential backups, and are archived continuously.
   
### Restoring a server

- When using the Point-in-time-Restore feature, the new server is created with the same compute and storage configurations as the server it is based on.
- VNET based database servers are restored into the same VNET when you restore from a backup.
- The new server created during a restore doesn't have the firewall rules that existed on the original server. Firewall rules need to be created separately for the new server.
- Restore to a different subscription isn't supported but as a workaround, you can restore the server within the same subscription and then migrate the restored server to a different subscription.
   
## Next steps

- Understand [what’s available for compute and storage options](concepts-compute-storage.md)
- Learn about [Supported PostgreSQL database versions](concepts-supported-versions.md)
- Review [how to back up and restore a server in Azure Database for PostgreSQL flexible server using the Azure portal](how-to-restore-server-portal.md)


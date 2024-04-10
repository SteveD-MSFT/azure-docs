---
title: Supported versions
description: Describes the supported PostgreSQL major and minor versions in Azure Database for PostgreSQL - Flexible Server.
ms.author: sunila
author: sunilagarwal
ms.service: postgresql
ms.subservice: flexible-server
ms.custom:
  - ignite-2023
ms.topic: conceptual
ms.date: 12/21/2023
---

# Supported PostgreSQL major versions in Azure Database for PostgreSQL - Flexible Server

[!INCLUDE [applies-to-postgresql-flexible-server](../includes/applies-to-postgresql-flexible-server.md)]

Azure Database for PostgreSQL flexible server currently supports the following major versions:

## PostgreSQL version 16

PostgreSQL version 16 is now generally available in all Azure regions. The current minor release is **16.0**. Refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/16/release-16.html) to learn more about improvements and fixes in this release. New servers are created with this minor version. 


## PostgreSQL version 15

The current minor release is **15.4**. Refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/release/15.4/) to learn more about improvements and fixes in this release. New servers are created with this minor version. 

## PostgreSQL version 14

The current minor release is **14.9**. Refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/release/14.9/) to learn more about improvements and fixes in this release. New servers are created with this minor version.


## PostgreSQL version 13

The current minor release is **13.12**. Refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/release/13.12/) to learn more about improvements and fixes in this release. New servers are created with this minor version. 

## PostgreSQL version 12

The current minor release is **12.16**. Refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/release/12.16/) to learn more about improvements and fixes in this release. New servers are created with this minor version. Your existing servers are automatically upgraded to the latest supported minor version in your future scheduled maintenance window.

## PostgreSQL version 11

The current minor release is **11.21**. Refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/release/11.21/) to learn more about improvements and fixes in this release. New servers are created with this minor version. Your existing servers are automatically upgraded to the latest supported minor version in your future scheduled maintenance window.

## PostgreSQL version 10 and older

We don't support PostgreSQL version 10 and older for Azure Database for PostgreSQL flexible server. Use the [Azure Database for PostgreSQL single server](../concepts-supported-versions.md) deployment option if you require older versions.

## Managing upgrades

The PostgreSQL project regularly issues minor releases to fix reported bugs. Azure Database for PostgreSQL flexible server automatically patches servers with minor releases during the service's monthly deployments.

It is also possible to do in-place major version upgrades by means of the [Major Version Upgrade](./concepts-major-version-upgrade.md) feature. This feature greatly simplifies the upgrade process of an instance from a given major version (PostgreSQL 11, for example) to any higher supported version (like PostgreSQL 16).

## Supportability and retirement policy of the underlying operating system

Azure Database for PostgreSQL flexible server is a fully managed open-source database. The underlying operating system is an integral part of the service. Microsoft continually works to ensure ongoing security updates and maintenance for security compliance and vulnerability mitigation, regardless of whether it is provided by a third-party or an internal vendor. Automatic upgrades during scheduled maintenance keep your managed database secure, stable, and up-to-date.


## Managing PostgreSQL engine defects

Microsoft has a team of committers and contributors who work full time on the open source Postgres project and are long term members of the community. Our contributions include but aren't  limited to features, performance enhancements, bug fixes, security patches among other things. Our open source team also incorporates feedback from our Azure fleet (and customers) when prioritizing work, however please keep in mind that Postgres project has its own independent contribution guidelines, review process and release schedule.

When a defect with PostgreSQL engine is identified, Microsoft takes immediate action to mitigate the issue. If it requires code change, Microsoft fixes the defect to address the production issue, if possible, and work with the community to incorporate the fix as quickly as possible.


<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->

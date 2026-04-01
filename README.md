# SQL Multitenant Elastic Shard SQL Utility

A .NET 10 console application for managing [Azure SQL Database Elastic Scale](https://learn.microsoft.com/en-us/azure/azure-sql/database/elastic-scale-introduction) sharded multi-tenant databases. It provides a command-line interface for provisioning tenants, querying shard mappings, executing cross-shard SQL scripts, and recovering from mapping inconsistencies.

This application originated from a demo project at [2023-GlobalAzure-Multitenant-SaaS-Application](https://github.com/calloncampbell/2023-GlobalAzure-Multitenant-SaaS-Application) and was moved to its own repository for easier maintenance.


## Table of Contents

- [Requirements](#requirements)
- [Project Structure](#project-structure)
- [Configuration](#configuration)
- [Getting Started](#getting-started)
- [Commands](#commands)
  - [shard-map-manager](#shard-map-manager)
  - [shard](#shard)
  - [recovery-manager](#recovery-manager)
- [References](#references)


## Requirements

- [.NET 10.0 SDK](https://dotnet.microsoft.com/download/dotnet/10.0)
- Azure SQL Database server (or SQL Server)


## Project Structure

```
src/ElasticShardSqlUtil/
├── Program.cs                  # Entry point and CLI builder
├── appsettings.json            # Application configuration
├── InitializeShard.sql         # SQL schema applied to new shards
├── Interfaces/                 # Service contracts
│   ├── IShardManagement.cs
│   └── IRecoveryManagement.cs
├── Models/                     # Data models
│   ├── DatabaseShardTable.cs
│   └── LogEvents.cs
├── Options/                    # CLI option classes
│   ├── ShardMapManagerOptions.cs
│   ├── ShardOptions.cs
│   └── RecoveryOptions.cs
├── Services/                   # Business logic
│   ├── ShardManagement.cs
│   └── RecoveryManagement.cs
└── Utils/                      # Utility helpers
    ├── ConfigurationUtils.cs
    ├── ConsoleUtils.cs
    ├── ShardManagementUtils.cs
    └── SqlDatabaseUtils.cs
```


## Configuration

All settings are read from `appsettings.json` in the output directory. Copy and edit the file before running the application.

| Setting | Description |
|---|---|
| `DatabaseServerName` | Fully qualified Azure SQL server name (e.g. `server.database.windows.net`) |
| `SqlAuthenticationMethod` | `SqlPassword` \| `ActiveDirectoryIntegrated` \| `ActiveDirectoryManagedIdentity` \| `ActiveDirectoryServicePrincipal` |
| `IntegratedSecurity` | `true` / `false` |
| `SqlUsername` | SQL login username (used with `SqlPassword` auth) |
| `SqlPassword` | SQL login password (used with `SqlPassword` auth) |
| `SqlCommandTimeout` | Command timeout in seconds (default `900`) |
| `ShardMapManagerDatabaseName` | Name of the catalog database that holds the Global Shard Map (GSM) |
| `ShardMapName` | Name of the shard map (e.g. `TenantIDShardMap`) |
| `ShardDatabaseNameFormat` | Database name format string; `{0}` is replaced with the tenant ID |
| `DatabaseReferenceTables` | Array of reference table names (replicated to every shard) |
| `DatabaseShardTables` | Array of `{ TableName, KeyColumnName }` objects for sharded tables |

Example:

```json
{
  "DatabaseServerName": "myserver.database.windows.net",
  "SqlAuthenticationMethod": "SqlPassword",
  "IntegratedSecurity": false,
  "SqlUsername": "sqladmin",
  "SqlPassword": "<password>",
  "SqlCommandTimeout": 900,
  "ShardMapManagerDatabaseName": "catalog-db",
  "ShardMapName": "TenantIDShardMap",
  "ShardDatabaseNameFormat": "tenant-{0}-db",
  "DatabaseReferenceTables": ["Regions", "Products"],
  "DatabaseShardTables": [
    { "TableName": "Customers", "KeyColumnName": "CustomerId" },
    { "TableName": "Orders",    "KeyColumnName": "CustomerId" }
  ]
}
```


## Getting Started

1. **Clone the repository**

   ```bash
   git clone https://github.com/calloncampbell/SQL-Multitenant-ElasticShardSqlUtil.git
   cd SQL-Multitenant-ElasticShardSqlUtil
   ```

2. **Build the project**

   ```bash
   cd src
   dotnet build
   ```

3. **Edit configuration**

   Update `src/ElasticShardSqlUtil/bin/Debug/net10.0/appsettings.json` with your Azure SQL server details.

4. **Create the Shard Map Manager database**

   ```bash
   dotnet run --project ElasticShardSqlUtil -- shard-map-manager create
   ```

5. **Add your first tenant**

   ```bash
   # Database-per-tenant
   dotnet run --project ElasticShardSqlUtil -- shard add --tenants 1001 --tenant-type database-per-tenant --file InitializeShard.sql

   # Shared (sharded multi-tenant) database
   dotnet run --project ElasticShardSqlUtil -- shard add --tenants 1001 1002 --tenant-type sharded-multi-tenant --database-name shared-db --file InitializeShard.sql
   ```


## Commands

### shard-map-manager

Manages the central catalog database (Global Shard Map).

| Subcommand | Description |
|---|---|
| `create` | Creates the Shard Map Manager database and initializes the GSM |
| `status` | Displays all shards and their tenant mappings |
| `cleanup` | Removes shards that have no tenant mappings |

```bash
ElasticShardSqlUtil shard-map-manager create
ElasticShardSqlUtil shard-map-manager status
ElasticShardSqlUtil shard-map-manager cleanup
```

### shard

Manages individual shards and tenant mappings.

| Subcommand | Option | Description |
|---|---|---|
| `add` | `--tenants <ids>` | Space-separated tenant IDs to add |
| | `--tenant-type` | `database-per-tenant` or `sharded-multi-tenant` |
| | `--database-name` | Target database name (sharded-multi-tenant only) |
| | `--file` | SQL initialization script to execute on the new shard |
| `get` | `--tenants <ids>` | Retrieves shard location details for the specified tenants |
| `delete` | `--tenants <ids>` | Removes tenant mappings (and optionally the shard) |
| `sql-script` | `--file` | Executes a SQL script against all shards |

```bash
# Add a tenant (database-per-tenant)
ElasticShardSqlUtil shard add --tenants 2871 --tenant-type database-per-tenant --file InitializeShard.sql

# Add multiple tenants to a shared shard
ElasticShardSqlUtil shard add --tenants 1000 2000 --tenant-type sharded-multi-tenant --database-name demos --file InitializeShard.sql

# Query tenant location
ElasticShardSqlUtil shard get --tenants 2871

# Remove a tenant mapping
ElasticShardSqlUtil shard delete --tenants 2871

# Execute a SQL script on all shards
ElasticShardSqlUtil shard sql-script --file myscript.sql
```

### recovery-manager

Detects and resolves inconsistencies between the Global Shard Map (GSM) and Local Shard Maps (LSM).

| Subcommand | Option | Description |
|---|---|---|
| `detach-shard` | `--tenants <ids>` | Detaches a shard and removes its mappings from the GSM |
| `detect-mapping-issues` | `--tenants <ids>` | Reports mapping differences between the GSM and LSM |
| `resolve-mapping-issues` | `--tenants <ids>` | Reconciles mapping differences |
| | `--resolution-type` | `KeepShardMapMapping` or `KeepShardMapping` |
| `attach-shard` | `--tenants <ids>` | Attaches a restored shard *(not yet implemented)* |

```bash
ElasticShardSqlUtil recovery-manager detect-mapping-issues --tenants 2871
ElasticShardSqlUtil recovery-manager resolve-mapping-issues --tenants 2871 --resolution-type KeepShardMapping
ElasticShardSqlUtil recovery-manager detach-shard --tenants 2871
```


## References

- [Azure SQL Database Elastic Scale overview](https://learn.microsoft.com/en-us/azure/azure-sql/database/elastic-scale-introduction)
- [Elastic Scale client library for .NET](https://learn.microsoft.com/en-us/azure/azure-sql/database/elastic-database-client-library)
- [Shard map management](https://learn.microsoft.com/en-us/azure/azure-sql/database/elastic-scale-shard-map-management)
- [Multi-tenant SaaS patterns with Azure SQL Database](https://learn.microsoft.com/en-us/azure/azure-sql/database/saas-tenancy-app-design-patterns)
- [Original demo project – 2023 Global Azure](https://github.com/calloncampbell/2023-GlobalAzure-Multitenant-SaaS-Application)


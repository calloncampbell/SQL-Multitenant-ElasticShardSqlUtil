# Elastic Shard SQL Utility

The `Elastic Shard SQL Utility` is a .NET 10 console application that provides a set of utility commands for managing SQL sharded databases built with the Azure SQL Elastic Scale pattern.

This application originated from a demo project located at https://github.com/calloncampbell/2023-GlobalAzure-Multitenant-SaaS-Application. It was moved to its own repository to make it easier to manage and maintain.


## Requirements

- [.NET 10.0 SDK](https://dotnet.microsoft.com/download/dotnet/10.0)
- Azure SQL Database server (or SQL Server)


## Getting Started

1. Edit `appsettings.json` with your Azure SQL server details and authentication settings.

2. Build and run:

   ```bash
   dotnet build
   dotnet run -- --help
   ```

3. Create the Shard Map Manager:

   ```bash
   dotnet run -- shard-map-manager create
   ```

4. Add a tenant:

   ```bash
   dotnet run -- shard add --tenants 1001 --tenant-type database-per-tenant --file InitializeShard.sql
   ```

See the [repository README](../../README.md) for the full command reference and configuration details.


## References

- [Azure SQL Database Elastic Scale overview](https://learn.microsoft.com/en-us/azure/azure-sql/database/elastic-scale-introduction)
- [Elastic Scale client library for .NET](https://learn.microsoft.com/en-us/azure/azure-sql/database/elastic-database-client-library)
- [Multi-tenant SaaS patterns with Azure SQL Database](https://learn.microsoft.com/en-us/azure/azure-sql/database/saas-tenancy-app-design-patterns)
- [Original demo project – 2023 Global Azure](https://github.com/calloncampbell/2023-GlobalAzure-Multitenant-SaaS-Application)

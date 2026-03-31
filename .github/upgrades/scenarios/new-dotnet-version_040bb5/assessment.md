# Projects and dependencies analysis

This document provides a comprehensive overview of the projects and their dependencies in the context of upgrading to .NETCoreApp,Version=v10.0.

## Table of Contents

- [Executive Summary](#executive-Summary)
  - [Highlevel Metrics](#highlevel-metrics)
  - [Projects Compatibility](#projects-compatibility)
  - [Package Compatibility](#package-compatibility)
  - [API Compatibility](#api-compatibility)
- [Aggregate NuGet packages details](#aggregate-nuget-packages-details)
- [Top API Migration Challenges](#top-api-migration-challenges)
  - [Technologies and Features](#technologies-and-features)
  - [Most Frequent API Issues](#most-frequent-api-issues)
- [Projects Relationship Graph](#projects-relationship-graph)
- [Project Details](#project-details)

  - [ElasticShardSqlUtil\ElasticShardSqlUtil.csproj](#elasticshardsqlutilelasticshardsqlutilcsproj)


## Executive Summary

### Highlevel Metrics

| Metric | Count | Status |
| :--- | :---: | :--- |
| Total Projects | 1 | All require upgrade |
| Total NuGet Packages | 8 | 4 need upgrade |
| Total Code Files | 14 |  |
| Total Code Files with Incidents | 2 |  |
| Total Lines of Code | 2079 |  |
| Total Number of Issues | 166 |  |
| Estimated LOC to modify | 161+ | at least 7.7% of codebase |

### Projects Compatibility

| Project | Target Framework | Difficulty | Package Issues | API Issues | Est. LOC Impact | Description |
| :--- | :---: | :---: | :---: | :---: | :---: | :--- |
| [ElasticShardSqlUtil\ElasticShardSqlUtil.csproj](#elasticshardsqlutilelasticshardsqlutilcsproj) | net8.0 | 🟢 Low | 4 | 161 | 161+ | DotNetCoreApp, Sdk Style = True |

### Package Compatibility

| Status | Count | Percentage |
| :--- | :---: | :---: |
| ✅ Compatible | 4 | 50.0% |
| ⚠️ Incompatible | 0 | 0.0% |
| 🔄 Upgrade Recommended | 4 | 50.0% |
| ***Total NuGet Packages*** | ***8*** | ***100%*** |

### API Compatibility

| Category | Count | Impact |
| :--- | :---: | :--- |
| 🔴 Binary Incompatible | 0 | High - Require code changes |
| 🟡 Source Incompatible | 161 | Medium - Needs re-compilation and potential conflicting API error fixing |
| 🔵 Behavioral change | 0 | Low - Behavioral changes that may require testing at runtime |
| ✅ Compatible | 1578 |  |
| ***Total APIs Analyzed*** | ***1739*** |  |

## Aggregate NuGet packages details

| Package | Current Version | Suggested Version | Projects | Description |
| :--- | :---: | :---: | :--- | :--- |
| Microsoft.Azure.SqlDatabase.ElasticScale.Client | 2.4.2 |  | [ElasticShardSqlUtil.csproj](#elasticshardsqlutilelasticshardsqlutilcsproj) | ✅Compatible |
| Microsoft.Data.SqlClient | 6.0.1 |  | [ElasticShardSqlUtil.csproj](#elasticshardsqlutilelasticshardsqlutilcsproj) | ✅Compatible |
| Microsoft.Extensions.Configuration | 8.0.0 | 10.0.5 | [ElasticShardSqlUtil.csproj](#elasticshardsqlutilelasticshardsqlutilcsproj) | NuGet package upgrade is recommended |
| Microsoft.Extensions.Configuration.Json | 8.0.1 | 10.0.5 | [ElasticShardSqlUtil.csproj](#elasticshardsqlutilelasticshardsqlutilcsproj) | NuGet package upgrade is recommended |
| Microsoft.Extensions.Hosting | 8.0.0 | 10.0.5 | [ElasticShardSqlUtil.csproj](#elasticshardsqlutilelasticshardsqlutilcsproj) | NuGet package upgrade is recommended |
| System.CommandLine | 2.0.0-beta4.22272.1 |  | [ElasticShardSqlUtil.csproj](#elasticshardsqlutilelasticshardsqlutilcsproj) | ✅Compatible |
| System.CommandLine.Hosting | 0.4.0-alpha.22272.1 |  | [ElasticShardSqlUtil.csproj](#elasticshardsqlutilelasticshardsqlutilcsproj) | ✅Compatible |
| System.Configuration.ConfigurationManager | 8.0.1 | 10.0.5 | [ElasticShardSqlUtil.csproj](#elasticshardsqlutilelasticshardsqlutilcsproj) | NuGet package upgrade is recommended |

## Top API Migration Challenges

### Technologies and Features

| Technology | Issues | Percentage | Migration Path |
| :--- | :---: | :---: | :--- |

### Most Frequent API Issues

| API | Count | Percentage | Category |
| :--- | :---: | :---: | :--- |
| T:System.CommandLine.Invocation.ICommandHandler | 22 | 13.7% | Source Incompatible |
| T:System.CommandLine.ArgumentArity | 21 | 13.0% | Source Incompatible |
| M:System.CommandLine.Command.AddCommand(System.CommandLine.Command) | 14 | 8.7% | Source Incompatible |
| T:System.CommandLine.Command | 14 | 8.7% | Source Incompatible |
| M:System.CommandLine.Command.#ctor(System.String,System.String) | 14 | 8.7% | Source Incompatible |
| P:System.CommandLine.Option.IsRequired | 12 | 7.5% | Source Incompatible |
| M:System.CommandLine.Command.Add(System.CommandLine.Option) | 12 | 7.5% | Source Incompatible |
| T:System.CommandLine.NamingConventionBinder.CommandHandler | 11 | 6.8% | Source Incompatible |
| P:System.CommandLine.Command.Handler | 11 | 6.8% | Source Incompatible |
| P:System.CommandLine.Option.AllowMultipleArgumentsPerToken | 7 | 4.3% | Source Incompatible |
| P:System.CommandLine.ArgumentArity.OneOrMore | 7 | 4.3% | Source Incompatible |
| T:System.CommandLine.Builder.CommandLineBuilder | 5 | 3.1% | Source Incompatible |
| M:System.CommandLine.Builder.CommandLineBuilder.#ctor(System.CommandLine.Command) | 1 | 0.6% | Source Incompatible |
| T:System.CommandLine.RootCommand | 1 | 0.6% | Source Incompatible |
| M:System.CommandLine.RootCommand.#ctor(System.String) | 1 | 0.6% | Source Incompatible |
| T:System.CommandLine.Hosting.HostingExtensions | 1 | 0.6% | Source Incompatible |
| M:System.CommandLine.Hosting.HostingExtensions.UseHost(System.CommandLine.Builder.CommandLineBuilder,System.Func{System.String[],Microsoft.Extensions.Hosting.IHostBuilder},System.Action{Microsoft.Extensions.Hosting.IHostBuilder}) | 1 | 0.6% | Source Incompatible |
| T:System.CommandLine.Builder.CommandLineBuilderExtensions | 1 | 0.6% | Source Incompatible |
| M:System.CommandLine.Builder.CommandLineBuilderExtensions.UseDefaults(System.CommandLine.Builder.CommandLineBuilder) | 1 | 0.6% | Source Incompatible |
| T:System.CommandLine.Parsing.Parser | 1 | 0.6% | Source Incompatible |
| M:System.CommandLine.Builder.CommandLineBuilder.Build | 1 | 0.6% | Source Incompatible |
| T:System.CommandLine.Parsing.ParserExtensions | 1 | 0.6% | Source Incompatible |
| M:System.CommandLine.Parsing.ParserExtensions.InvokeAsync(System.CommandLine.Parsing.Parser,System.String[],System.CommandLine.IConsole) | 1 | 0.6% | Source Incompatible |

## Projects Relationship Graph

Legend:
📦 SDK-style project
⚙️ Classic project

```mermaid
flowchart LR
    P1["<b>📦&nbsp;ElasticShardSqlUtil.csproj</b><br/><small>net8.0</small>"]
    click P1 "#elasticshardsqlutilelasticshardsqlutilcsproj"

```

## Project Details

<a id="elasticshardsqlutilelasticshardsqlutilcsproj"></a>
### ElasticShardSqlUtil\ElasticShardSqlUtil.csproj

#### Project Info

- **Current Target Framework:** net8.0
- **Proposed Target Framework:** net10.0
- **SDK-style**: True
- **Project Kind:** DotNetCoreApp
- **Dependencies**: 0
- **Dependants**: 0
- **Number of Files**: 14
- **Number of Files with Incidents**: 2
- **Lines of Code**: 2079
- **Estimated LOC to modify**: 161+ (at least 7.7% of the project)

#### Dependency Graph

Legend:
📦 SDK-style project
⚙️ Classic project

```mermaid
flowchart TB
    subgraph current["ElasticShardSqlUtil.csproj"]
        MAIN["<b>📦&nbsp;ElasticShardSqlUtil.csproj</b><br/><small>net8.0</small>"]
        click MAIN "#elasticshardsqlutilelasticshardsqlutilcsproj"
    end

```

### API Compatibility

| Category | Count | Impact |
| :--- | :---: | :--- |
| 🔴 Binary Incompatible | 0 | High - Require code changes |
| 🟡 Source Incompatible | 161 | Medium - Needs re-compilation and potential conflicting API error fixing |
| 🔵 Behavioral change | 0 | Low - Behavioral changes that may require testing at runtime |
| ✅ Compatible | 1578 |  |
| ***Total APIs Analyzed*** | ***1739*** |  |


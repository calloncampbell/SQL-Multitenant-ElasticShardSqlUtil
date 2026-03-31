# .NET 10 Upgrade Plan
## ElasticShardSqlUtil Solution

---

## Table of Contents

- [Executive Summary](#executive-summary)
- [Migration Strategy](#migration-strategy)
- [Detailed Dependency Analysis](#detailed-dependency-analysis)
- [Project-by-Project Plans](#project-by-project-plans)
  - [ElasticShardSqlUtil.csproj](#elasticshardsqlutilcsproj)
- [Package Update Reference](#package-update-reference)
- [Breaking Changes Catalog](#breaking-changes-catalog)
- [Testing & Validation Strategy](#testing--validation-strategy)
- [Complexity & Effort Assessment](#complexity--effort-assessment)
- [Risk Management](#risk-management)
- [Source Control Strategy](#source-control-strategy)
- [Success Criteria](#success-criteria)

---

## Executive Summary

### Overview
This plan outlines the upgrade of the **ElasticShardSqlUtil** solution from **.NET 8.0** to **.NET 10.0 (LTS)**.

### Scope
- **Projects**: 1 (ElasticShardSqlUtil.csproj)
- **Total Issues**: 166
  - Mandatory: 1 (TFM change)
  - Potential: 165 (API incompatibilities + package updates)
- **Affected Files**: 2
- **Lines of Code**: ~2,079

### Key Challenges
1. **System.CommandLine API Incompatibilities**: 161 source-incompatible API usages detected in `Program.cs`, primarily related to the beta version of System.CommandLine (2.0.0-beta4) used in the project
2. **NuGet Package Updates**: 4 Microsoft.Extensions.* packages need updating to version 10.0.5
3. **Target Framework Modernization**: Update from net8.0 to net10.0

### Complexity Rating
**LOW** - Single project with no dependencies or dependants, straightforward upgrade path with localized breaking changes.

### Estimated Effort
- **Developer Time**: 2-4 hours
- **Testing Time**: 1-2 hours
- **Total**: 3-6 hours

### Migration Approach
**All-At-Once Strategy** - Given this is a single, standalone project with no internal dependencies, we will upgrade the entire project in one coordinated update.

---

## Migration Strategy

### Strategy: All-At-Once

Given the solution's characteristics, we will use a coordinated **All-At-Once** migration strategy:

**Rationale:**
- Single project with no internal dependencies
- Low complexity (2,079 LOC, 14 files)
- Localized breaking changes (primarily in Program.cs)
- No risk of circular dependency issues
- Faster overall completion

### Execution Phases

#### Phase 1: Pre-Migration Preparation
1. Validate .NET 10 SDK installation
2. Create backup/feature branch (`upgrade-to-NET10`)
3. Document current build/test baseline

#### Phase 2: Project & Package Updates
1. Update TargetFramework from `net8.0` to `net10.0`
2. Update NuGet packages:
   - Microsoft.Extensions.Configuration: 8.0.0 → 10.0.5
   - Microsoft.Extensions.Configuration.Json: 8.0.1 → 10.0.5
   - Microsoft.Extensions.Hosting: 8.0.0 → 10.0.5
   - System.Configuration.ConfigurationManager: 8.0.1 → 10.0.5
3. Retain compatible packages at current versions

#### Phase 3: API Compatibility Remediation
1. Address 161 System.CommandLine breaking changes in `Program.cs`
   - Update CommandLineBuilder usage
   - Migrate from deprecated CommandHandler.Create pattern
   - Update Option initialization (Arity, IsRequired, AllowMultipleArgumentsPerToken)
   - Replace obsolete Command.Handler property
2. Review and test all command-line functionality

#### Phase 4: Build & Validation
1. Build project targeting net10.0
2. Fix any remaining compilation errors
3. Run existing tests (if available)
4. Perform manual smoke testing of CLI commands

#### Phase 5: Finalization
1. Update project metadata (version, release notes)
2. Commit changes to upgrade branch
3. Create pull request for review

---

## Detailed Dependency Analysis

### Project Hierarchy

This solution contains a single project with no internal dependencies:

```
ElasticShardSqlUtil.csproj (net8.0 → net10.0)
  └── No internal project dependencies
```

### External Dependencies (NuGet Packages)

#### Packages Requiring Updates (4)
| Package | Current | Target | Impact |
|---------|---------|--------|--------|
| Microsoft.Extensions.Configuration | 8.0.0 | 10.0.5 | Low - Version alignment |
| Microsoft.Extensions.Configuration.Json | 8.0.1 | 10.0.5 | Low - Version alignment |
| Microsoft.Extensions.Hosting | 8.0.0 | 10.0.5 | Low - Version alignment |
| System.Configuration.ConfigurationManager | 8.0.1 | 10.0.5 | Low - Version alignment |

#### Compatible Packages (4)
| Package | Version | Status |
|---------|---------|--------|
| Microsoft.Azure.SqlDatabase.ElasticScale.Client | 2.4.2 | ✅ Compatible - No update needed |
| Microsoft.Data.SqlClient | 6.0.1 | ✅ Compatible - No update needed |
| System.CommandLine | 2.0.0-beta4.22272.1 | ✅ Compatible - Has breaking API changes |
| System.CommandLine.Hosting | 0.4.0-alpha.22272.1 | ✅ Compatible - No update needed |

### Migration Order

**Single-Step Migration:**
Since there are no internal project dependencies, the migration can be completed in a single pass:
1. ElasticShardSqlUtil.csproj - Update TFM, packages, and code simultaneously

---

## Project-by-Project Plans

### ElasticShardSqlUtil.csproj

**Project Type:** Console Application (SDK-style)  
**Current TFM:** net8.0  
**Target TFM:** net10.0  
**Complexity:** Low  
**Issues:** 166 total (1 mandatory, 165 potential)

---

#### Project Overview
Console utility for managing Azure SQL Database Elastic Scale (sharding) operations. Provides command-line interface for recovery management, shard operations, and database utilities.

**Key Components:**
- Command-line interface using System.CommandLine (beta)
- Elastic Scale client integration
- Configuration management
- Hosting infrastructure

**Files:** 14 total, 2 with upgrade issues
- `ElasticShardSqlUtil.csproj` - 5 issues (TFM + packages)
- `Program.cs` - 161 issues (System.CommandLine API breaking changes)

---

#### Step-by-Step Migration Plan

##### Step 1: Update Target Framework
**File:** `ElasticShardSqlUtil.csproj`

**Action:** Update TargetFramework property
```xml
<TargetFramework>net10.0</TargetFramework>
```

**Impact:** Mandatory - Required for .NET 10 compilation  
**Risk:** None - Straightforward property change

---

##### Step 2: Update NuGet Packages
**File:** `ElasticShardSqlUtil.csproj`

**Actions:**
1. **Microsoft.Extensions.Configuration**: 8.0.0 → 10.0.5
   ```xml
   <PackageReference Include="Microsoft.Extensions.Configuration" Version="10.0.5" />
   ```

2. **Microsoft.Extensions.Configuration.Json**: 8.0.1 → 10.0.5
   ```xml
   <PackageReference Include="Microsoft.Extensions.Configuration.Json" Version="10.0.5" />
   ```

3. **Microsoft.Extensions.Hosting**: 8.0.0 → 10.0.5
   ```xml
   <PackageReference Include="Microsoft.Extensions.Hosting" Version="10.0.5" />
   ```

4. **System.Configuration.ConfigurationManager**: 8.0.1 → 10.0.5
   ```xml
   <PackageReference Include="System.Configuration.ConfigurationManager" Version="10.0.5" />
   ```

**Packages to Keep at Current Versions:**
- Microsoft.Azure.SqlDatabase.ElasticScale.Client: 2.4.2 ✅
- Microsoft.Data.SqlClient: 6.0.1 ✅
- System.CommandLine: 2.0.0-beta4.22272.1 ⚠️ (has breaking changes)
- System.CommandLine.Hosting: 0.4.0-alpha.22272.1 ✅

**Impact:** Low - These are standard version alignments for .NET 10  
**Risk:** Low - Well-tested packages with stable APIs

---

##### Step 3: Address System.CommandLine Breaking Changes
**File:** `Program.cs`  
**Issues:** 161 API incompatibilities

**Critical Breaking Changes to Address:**

1. **CommandHandler.Create Pattern** (Deprecated)
   - **Issue:** `CommandHandler.Create<T>()` is obsolete
   - **Current Usage:** Lines 196, 177, 158, etc.
   ```csharp
   // ❌ Old Pattern (Deprecated)
   command.Handler = CommandHandler.Create<RecoveryOptions, IHost>(RecoveryManagerCommandAttachShard);
   ```
   - **Migration Path:**
   ```csharp
   // ✅ New Pattern - Use SetHandler extension method
   command.SetHandler<RecoveryOptions, IHost>(RecoveryManagerCommandAttachShard);
   ```

2. **Command.Handler Property** (Removed)
   - **Issue:** `Command.Handler` property no longer exists
   - **Affected Lines:** Multiple (196, 177, 158, etc.)
   - **Migration:** Replace with `SetHandler()` method

3. **Option Initialization Changes**
   - **Properties Deprecated:**
     - `IsRequired` (use `Option<T>` constructor parameter)
     - `AllowMultipleArgumentsPerToken` (use different initialization pattern)
     - `Arity` property assignment (use constructor parameter)

   ```csharp
   // ❌ Old Pattern
   new Option<int[]>(new []{ "--tenants", "-t" }, "Description")
   {
       Arity = ArgumentArity.OneOrMore,
       AllowMultipleArgumentsPerToken = true,
       IsRequired = true
   }

   // ✅ New Pattern
   var option = new Option<int[]>(
       aliases: new[] { "--tenants", "-t" },
       description: "Description"
   );
   option.IsRequired = true; // Or use Option constructor with isRequired parameter
   ```

4. **CommandLineBuilder Updates**
   - **Issue:** API surface changes in CommandLineBuilder
   - **Impact:** Initialization pattern may need updates
   - **Action:** Review `BuildCommandLine()` method (line 63-201)

**Recommended Approach:**
1. Review System.CommandLine migration guide for beta4 → stable API changes
2. Consider migrating to stable System.CommandLine release (if available)
3. Update all command definitions systematically
4. Test each command after migration

**Impact:** High - Core functionality depends on command-line parsing  
**Risk:** Medium - Requires careful testing of all CLI commands

---

##### Step 4: Update Project Metadata
**File:** `ElasticShardSqlUtil.csproj`

**Actions:**
Update version information and release notes:
```xml
<Version>1.4.0</Version>
<AssemblyVersion>1.4.0.0</AssemblyVersion>
<FileVersion>1.4.0.0</FileVersion>
<InformationalVersion>Version 1.4.0</InformationalVersion>
<PackageReleaseNotes>
    - Updated to .NET 10.0 (LTS).
    - Updated NuGet packages to version 10.0.5.
    - Migrated System.CommandLine API usage to latest patterns.
</PackageReleaseNotes>
```

**Impact:** Low - Documentation only  
**Risk:** None

---

#### Testing Requirements

**Build Validation:**
1. Clean build with zero errors
2. No warnings related to obsolete API usage
3. Successful NuGet restore

**Functional Testing:**
Test all CLI commands:
- [ ] Recovery Manager commands
  - [ ] `attach-shard` with `--tenants` parameter
  - [ ] Other recovery commands
- [ ] Shard management operations
- [ ] Database utility functions
- [ ] Configuration loading
- [ ] Error handling scenarios

**Integration Testing:**
- [ ] Test with actual Azure SQL Database (if applicable)
- [ ] Validate Elastic Scale client operations
- [ ] Verify command-line argument parsing for all scenarios

---

#### Rollback Plan

If issues are encountered:
1. Revert to `net10-update` branch
2. Review specific breaking changes causing failures
3. Address incrementally with targeted fixes
4. Re-attempt migration

**No external dependencies** - rollback has no downstream impact.

---

## Package Update Reference

### Packages Requiring Updates

#### 1. Microsoft.Extensions.Configuration
- **Current Version:** 8.0.0
- **Target Version:** 10.0.5
- **Change Type:** Minor version update
- **Breaking Changes:** None expected
- **Migration Notes:** Standard framework alignment
- **Documentation:** https://www.nuget.org/packages/Microsoft.Extensions.Configuration

---

#### 2. Microsoft.Extensions.Configuration.Json
- **Current Version:** 8.0.1
- **Target Version:** 10.0.5
- **Change Type:** Minor version update
- **Breaking Changes:** None expected
- **Migration Notes:** Standard framework alignment
- **Documentation:** https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json

---

#### 3. Microsoft.Extensions.Hosting
- **Current Version:** 8.0.0
- **Target Version:** 10.0.5
- **Change Type:** Minor version update
- **Breaking Changes:** None expected
- **Migration Notes:** Standard framework alignment
- **Documentation:** https://www.nuget.org/packages/Microsoft.Extensions.Hosting

---

#### 4. System.Configuration.ConfigurationManager
- **Current Version:** 8.0.1
- **Target Version:** 10.0.5
- **Change Type:** Minor version update
- **Breaking Changes:** None expected
- **Migration Notes:** Standard framework alignment, supports legacy configuration
- **Documentation:** https://www.nuget.org/packages/System.Configuration.ConfigurationManager

---

### Packages Staying at Current Versions

#### 1. Microsoft.Azure.SqlDatabase.ElasticScale.Client
- **Version:** 2.4.2
- **Status:** ✅ Compatible with .NET 10
- **Reason:** Already compatible, no update available/needed
- **Notes:** Core elastic database tools client library

---

#### 2. Microsoft.Data.SqlClient
- **Version:** 6.0.1
- **Status:** ✅ Compatible with .NET 10
- **Reason:** Already compatible
- **Notes:** Modern SQL Server data provider

---

#### 3. System.CommandLine
- **Version:** 2.0.0-beta4.22272.1
- **Status:** ⚠️ Compatible but has breaking API changes
- **Reason:** Pre-release package, assess if newer beta/stable exists
- **Notes:** **CRITICAL** - 161 API incompatibilities detected. Consider upgrading to latest stable/beta if available
- **Alternative:** Check for System.CommandLine 2.0.0 stable release or newer beta

---

#### 4. System.CommandLine.Hosting
- **Version:** 0.4.0-alpha.22272.1
- **Status:** ✅ Compatible with .NET 10
- **Reason:** Pre-release package, works with current System.CommandLine
- **Notes:** Provides hosting integration for System.CommandLine

---

### Package Update Commands

```bash
# Navigate to project directory
cd D:\GitHub\calloncampbell\SQL-Multitenant-ElasticShardSqlUtil\src\ElasticShardSqlUtil

# Update packages individually
dotnet add package Microsoft.Extensions.Configuration --version 10.0.5
dotnet add package Microsoft.Extensions.Configuration.Json --version 10.0.5
dotnet add package Microsoft.Extensions.Hosting --version 10.0.5
dotnet add package System.Configuration.ConfigurationManager --version 10.0.5

# Or restore to apply project file changes
dotnet restore
```

---

### Optional: System.CommandLine Upgrade Investigation

Given the 161 breaking changes, investigate if a newer version exists:

```bash
# Check for latest System.CommandLine version
dotnet list package --outdated
dotnet list package --include-prerelease

# If newer version exists, evaluate upgrade
# Note: May require additional code changes beyond .NET 10 migration
```

---

## Breaking Changes Catalog

### High Impact - Code Changes Required

#### BC-001: System.CommandLine Handler API Changes
**Affected APIs:**
- `CommandHandler.Create<T>()` method
- `Command.Handler` property
- `ICommandHandler` interface

**Locations:** 
- `Program.cs` (161 occurrences across multiple commands)

**Issue:**
The command handler binding API has changed between System.CommandLine beta versions. The older `CommandHandler.Create()` pattern and direct `Command.Handler` assignment are deprecated/removed.

**Current Pattern:**
```csharp
command.Handler = CommandHandler.Create<RecoveryOptions, IHost>(RecoveryManagerCommandAttachShard);
```

**Required Changes:**
```csharp
// Option 1: Use SetHandler extension method
command.SetHandler<RecoveryOptions, IHost>(RecoveryManagerCommandAttachShard);

// Option 2: Use SetHandler with parameter binding
command.SetHandler(async (context) => 
{
    var options = context.ParseResult.GetValueForOption(optionInstance);
    var host = context.BindingContext.GetService<IHost>();
    await RecoveryManagerCommandAttachShard(options, host);
});
```

**Impact:** HIGH - Affects all command definitions  
**Effort:** 2-3 hours  
**Risk:** Medium - Core functionality change requiring thorough testing

---

#### BC-002: Option Initialization Property Changes
**Affected APIs:**
- `Option.IsRequired` property
- `Option.AllowMultipleArgumentsPerToken` property
- `Option.Arity` property
- `ArgumentArity` enum

**Locations:**
- `Program.cs` (Multiple option definitions)

**Issue:**
Option initialization patterns have changed. Some properties that were previously settable via object initializers may now require constructor parameters or different initialization patterns.

**Current Pattern:**
```csharp
new Option<int[]>(new []{ "--tenants", "-t" }, "Space separated list of tenant IDs.")
{
    Arity = ArgumentArity.OneOrMore,
    AllowMultipleArgumentsPerToken = true,
    IsRequired = true
}
```

**Required Changes:**
```csharp
// Check current System.CommandLine documentation for exact syntax
var option = new Option<int[]>(
    aliases: new[] { "--tenants", "-t" },
    description: "Space separated list of tenant IDs."
);
option.IsRequired = true;
// Arity and multiple arguments may be inferred from type or require different configuration
```

**Impact:** MEDIUM - Affects all Option declarations  
**Effort:** 1-2 hours  
**Risk:** Low - Compilation errors will identify all locations

---

#### BC-003: CommandLineBuilder API Surface Changes
**Affected APIs:**
- `CommandLineBuilder` constructor
- `CommandLineBuilder` configuration methods

**Locations:**
- `Program.cs` line 63 (`BuildCommandLine()` method)
- `Program.cs` line 201 (CommandLineBuilder instantiation)

**Issue:**
CommandLineBuilder API may have changed between beta versions.

**Current Pattern:**
```csharp
return new CommandLineBuilder(root);
```

**Required Investigation:**
- Review if CommandLineBuilder constructor signature changed
- Check if configuration methods (UseDefaults, UseHelp, etc.) remain compatible
- Verify middleware pipeline configuration

**Impact:** MEDIUM  
**Effort:** 1 hour  
**Risk:** Low - Should be evident during compilation

---

#### BC-004: Command.AddCommand Method Changes
**Affected APIs:**
- `Command.AddCommand(Command)` method

**Locations:**
- `Program.cs` (Multiple locations where commands are composed)

**Issue:**
Command composition API may have changed.

**Current Pattern:**
```csharp
root.AddCommand(commandRecoveryManager);
commandRecoveryManager.AddCommand(commandRecoveryManagerAttachShard);
```

**Required Investigation:**
- Verify AddCommand method signature
- Check if alternative composition methods exist
- Confirm hierarchical command structure approach

**Impact:** LOW  
**Effort:** < 1 hour  
**Risk:** Low - Straightforward verification

---

### Low Impact - Package Updates

#### BC-005: Microsoft.Extensions.* Package Version Alignment
**Affected Packages:**
- Microsoft.Extensions.Configuration (8.0.0 → 10.0.5)
- Microsoft.Extensions.Configuration.Json (8.0.1 → 10.0.5)
- Microsoft.Extensions.Hosting (8.0.0 → 10.0.5)
- System.Configuration.ConfigurationManager (8.0.1 → 10.0.5)

**Issue:**
Standard version alignment with .NET 10 framework.

**Breaking Changes:** None expected - these are minor version updates  
**Impact:** VERY LOW  
**Effort:** 5 minutes  
**Risk:** Minimal

---

### Summary Table

| ID | Category | Affected Area | Impact | Effort | Risk |
|----|----------|---------------|--------|--------|------|
| BC-001 | API Breaking Change | Command Handler Binding | HIGH | 2-3h | Medium |
| BC-002 | API Breaking Change | Option Initialization | MEDIUM | 1-2h | Low |
| BC-003 | API Breaking Change | CommandLineBuilder | MEDIUM | 1h | Low |
| BC-004 | API Breaking Change | Command Composition | LOW | <1h | Low |
| BC-005 | Package Update | Microsoft.Extensions.* | VERY LOW | 5m | Minimal |

**Total Estimated Effort:** 4.5 - 7 hours

---

## Testing & Validation Strategy

### Pre-Migration Baseline

#### 1. Document Current State
- [ ] Build current solution with .NET 8.0 SDK
- [ ] Document build output (warnings, errors if any)
- [ ] Run manual tests of all CLI commands
- [ ] Document command behavior and outputs

**Commands to Test:**
```bash
# Build baseline
dotnet build

# Test help system
dotnet run -- --help
dotnet run -- recovery-manager --help
dotnet run -- recovery-manager attach-shard --help

# Document all available commands
# (Add specific commands based on Program.cs review)
```

---

### Build Validation

#### Phase 1: Initial Build After TFM Change
**Goal:** Verify .NET 10 SDK compatibility

**Steps:**
1. Update TargetFramework to net10.0
2. Run `dotnet restore`
3. Run `dotnet build`

**Expected Result:** Build errors related to System.CommandLine APIs

**Success Criteria:**
- [ ] Project loads successfully
- [ ] NuGet restore completes
- [ ] Errors are limited to expected System.CommandLine breaking changes

---

#### Phase 2: Build After Package Updates
**Goal:** Verify package compatibility

**Steps:**
1. Update all Microsoft.Extensions.* packages to 10.0.5
2. Run `dotnet restore`
3. Run `dotnet build`

**Success Criteria:**
- [ ] All packages restore successfully
- [ ] No new errors introduced by package updates
- [ ] Errors remain limited to System.CommandLine API changes

---

#### Phase 3: Build After API Migration
**Goal:** Achieve clean build

**Steps:**
1. Apply all System.CommandLine API fixes
2. Run `dotnet build --no-restore`

**Success Criteria:**
- [ ] **Zero build errors**
- [ ] **Zero warnings** (or documented acceptable warnings)
- [ ] All projects build successfully

---

### Functional Testing

#### Command-Line Interface Testing

**Test Matrix:**

| Command | Parameters | Expected Behavior | Test Status |
|---------|-----------|-------------------|-------------|
| `--help` | None | Display help text | ⬜ Not Tested |
| `--version` | None | Display version | ⬜ Not Tested |
| `recovery-manager` | `--help` | Display recovery manager help | ⬜ Not Tested |
| `recovery-manager attach-shard` | `--tenants 1 2 3` | Attach specified shards | ⬜ Not Tested |
| `recovery-manager attach-shard` | `--tenants` (no values) | Show error - required | ⬜ Not Tested |
| `recovery-manager attach-shard` | Missing `--tenants` | Show error - required | ⬜ Not Tested |
| [Other commands] | [Various] | [Expected behavior] | ⬜ Not Tested |

**Testing Approach:**
1. Test each command individually
2. Verify error handling for invalid inputs
3. Confirm required parameters are enforced
4. Check optional parameters work correctly
5. Validate output formatting

---

#### Configuration Testing
**Goal:** Verify configuration loading still works

**Tests:**
- [ ] Verify `appsettings.json` loads correctly
- [ ] Test configuration binding to option types
- [ ] Validate connection strings (if applicable)
- [ ] Test IHost integration with dependency injection

---

#### Integration Testing
**Goal:** Verify Elastic Scale operations (if test environment available)

**Tests:**
- [ ] Connect to Azure SQL Database
- [ ] Execute shard management operations
- [ ] Verify Elastic Scale client functionality
- [ ] Test recovery manager operations

**Note:** May require test database environment

---

### Regression Testing

#### Comparison Testing
**Goal:** Ensure behavior is identical to .NET 8 version

**Method:**
1. Run same commands on .NET 8 version (baseline branch)
2. Run same commands on .NET 10 version (upgrade branch)
3. Compare outputs for consistency

**Critical Scenarios:**
- [ ] Help text output matches (except version numbers)
- [ ] Parameter parsing behavior unchanged
- [ ] Error messages remain consistent
- [ ] Success scenarios produce same results

---

### Performance Testing (Optional)

**Benchmarks to Consider:**
- Startup time
- Command execution time
- Memory usage patterns

**Goal:** Ensure no performance regression

---

### Automated Testing

#### Unit Tests
**Status:** 🔍 Check if project has existing tests

**If tests exist:**
- [ ] Run all existing unit tests
- [ ] Verify 100% pass rate
- [ ] Review any test code for .NET 10 compatibility

**If no tests exist:**
- Consider adding basic smoke tests for critical commands
- Implement automated CLI command testing

```bash
# Run tests (if available)
dotnet test
```

---

### Validation Checklist

#### Pre-Deployment Validation
- [ ] Clean build with zero errors
- [ ] All critical commands tested manually
- [ ] Configuration loading verified
- [ ] No obsolete API warnings
- [ ] Version metadata updated
- [ ] Release notes documented

#### Sign-Off Criteria
- [ ] Build succeeds on .NET 10 SDK
- [ ] All CLI commands functional
- [ ] No breaking changes in command behavior
- [ ] Performance acceptable
- [ ] Code review completed
- [ ] Documentation updated

---

### Rollback Testing

**Scenario:** If issues are discovered post-migration

**Rollback Steps:**
1. Switch back to `net10-update` branch
2. Verify .NET 8 version still builds
3. Confirm functionality restored

**Validation:**
- [ ] Rollback completes in < 5 minutes
- [ ] Original functionality intact
- [ ] No data loss or corruption

---

## Complexity & Effort Assessment

### Overall Complexity: **LOW** ⚡

**Justification:**
- Single project with no internal dependencies
- Small codebase (~2,079 lines of code)
- No database migrations
- No multi-project coordination required
- Localized breaking changes (primarily in one file)

---

### Effort Breakdown

#### Development Effort

| Task | Estimated Time | Complexity | Risk |
|------|---------------|------------|------|
| **Phase 1: Preparation** | | | |
| Setup branch & environment | 15 minutes | Trivial | None |
| Document baseline | 15 minutes | Trivial | None |
| **Phase 2: Project Updates** | | | |
| Update TargetFramework | 2 minutes | Trivial | None |
| Update NuGet packages | 5 minutes | Trivial | Low |
| Resolve package dependencies | 10 minutes | Low | Low |
| **Phase 3: API Migration** | | | |
| Analyze breaking changes | 30 minutes | Medium | Low |
| Update CommandHandler patterns | 1.5 hours | Medium | Medium |
| Update Option initializations | 1 hour | Medium | Low |
| Update CommandLineBuilder | 30 minutes | Low | Low |
| Fix remaining API issues | 1 hour | Medium | Medium |
| **Phase 4: Testing** | | | |
| Build validation | 15 minutes | Low | Low |
| Manual CLI testing | 1 hour | Medium | Medium |
| Regression testing | 30 minutes | Low | Low |
| **Phase 5: Finalization** | | | |
| Update metadata & docs | 15 minutes | Trivial | None |
| Code review preparation | 15 minutes | Low | None |
| **Total** | **~7 hours** | | |

**Time Estimates:**
- **Minimum:** 4 hours (if no unexpected issues)
- **Expected:** 6-7 hours (including thorough testing)
- **Maximum:** 10 hours (if System.CommandLine migration is complex)

---

### Skill Level Required

**Recommended:** Intermediate .NET Developer

**Required Knowledge:**
- C# and .NET SDK
- NuGet package management
- Command-line application development
- Git branching and version control

**Nice to Have:**
- Experience with System.CommandLine library
- Understanding of Azure SQL Elastic Scale
- .NET migration experience

---

### Risk Assessment

#### Low Risk Factors ✅
- Single project scope
- No external consumers/dependants
- SDK-style project (modern format)
- Comprehensive error reporting from compiler
- Easy rollback path (Git branch)
- No breaking changes in Microsoft.Extensions.* packages

#### Medium Risk Factors ⚠️
- System.CommandLine beta package with 161 API changes
- Limited documentation for beta API migrations
- Manual testing required for CLI commands
- Potential for subtle behavioral changes in command parsing

#### Mitigation Strategies
1. **Thorough Testing:** Test all CLI commands manually
2. **Incremental Approach:** Fix and test breaking changes systematically
3. **Documentation:** Reference System.CommandLine migration guides
4. **Version Control:** Commit frequently with clear messages
5. **Baseline Comparison:** Compare behavior with .NET 8 version

---

### Dependencies & Prerequisites

#### Required Software
- [x] .NET 10.0 SDK installed
- [x] Git (for version control)
- [x] Visual Studio / VS Code / Rider (IDE)

#### Optional
- [ ] Azure SQL Database test environment (for integration testing)
- [ ] System.CommandLine migration documentation

#### Knowledge Prerequisites
- Understanding of project's command structure
- Familiarity with elastic shard SQL operations
- Access to original requirements/specifications

---

### Resource Requirements

**Personnel:**
- 1 Developer (primary)
- 1 Reviewer (code review)
- 1 Tester (optional - for integration testing)

**Infrastructure:**
- Development workstation with .NET 10 SDK
- Git repository access
- (Optional) Azure SQL Database for integration testing

**Timeline:**
- **Ideal:** 1 day (focused effort)
- **Realistic:** 2-3 days (with other responsibilities)
- **With Testing:** Add 1 day for comprehensive integration testing

---

### Success Indicators

**Technical Success:**
- ✅ Clean build with .NET 10 SDK
- ✅ All CLI commands function identically to .NET 8 version
- ✅ No performance degradation
- ✅ Zero compilation warnings for obsolete APIs

**Process Success:**
- ✅ Changes committed to upgrade branch
- ✅ Documentation updated
- ✅ Code review completed
- ✅ Merge to main branch approved

**Business Success:**
- ✅ Application remains fully functional
- ✅ No disruption to users
- ✅ Foundation for future .NET updates established

---

## Risk Management

### Risk Register

#### RISK-001: System.CommandLine Breaking Changes
**Category:** Technical  
**Probability:** HIGH  
**Impact:** HIGH  
**Severity:** 🔴 Critical

**Description:**
161 API incompatibilities detected in System.CommandLine usage. The beta package may have significant breaking changes that require substantial code refactoring.

**Mitigation:**
- ✅ Compile comprehensive list of breaking changes before starting
- ✅ Review System.CommandLine migration documentation
- ✅ Consider upgrading to stable System.CommandLine 2.0 if available
- ✅ Implement changes incrementally, testing each command
- ✅ Use compiler errors as checklist for required changes

**Contingency:**
- If migration is too complex, consider alternative CLI parsing libraries
- Investigate if older System.CommandLine version is compatible with .NET 10
- Budget additional time for comprehensive refactoring

**Status:** 🟡 Active - Requires attention during Phase 3

---

#### RISK-002: Undocumented Behavioral Changes
**Category:** Functional  
**Probability:** MEDIUM  
**Impact:** MEDIUM  
**Severity:** 🟡 Moderate

**Description:**
System.CommandLine beta may have subtle behavioral changes not caught by compilation errors (e.g., argument parsing, validation, error messages).

**Mitigation:**
- ✅ Create comprehensive test matrix for all CLI commands
- ✅ Document current behavior before migration
- ✅ Compare outputs between .NET 8 and .NET 10 versions
- ✅ Test edge cases and error scenarios

**Contingency:**
- Maintain .NET 8 version for behavior reference
- Document any intentional behavioral changes
- Update user documentation if command behavior changes

**Status:** 🟡 Active - Monitor during testing

---

#### RISK-003: Integration Testing Limitations
**Category:** Process  
**Probability:** MEDIUM  
**Impact:** MEDIUM  
**Severity:** 🟡 Moderate

**Description:**
Full integration testing requires Azure SQL Database environment, which may not be readily available during migration.

**Mitigation:**
- ✅ Prioritize CLI command parsing tests (can be done without database)
- ✅ Use mocking/stubbing for database-dependent operations
- ✅ Schedule integration testing separately with test environment
- ✅ Document manual test procedures for database operations

**Contingency:**
- Deploy to staging environment for integration testing
- Partner with database administrator for test environment
- Consider containerized SQL Server for local testing

**Status:** 🟢 Manageable - Plan separate integration phase

---

#### RISK-004: Package Compatibility Issues
**Category:** Technical  
**Probability:** LOW  
**Impact:** MEDIUM  
**Severity:** 🟢 Low

**Description:**
Updated Microsoft.Extensions.* packages (10.0.5) may have unforeseen incompatibilities with existing code or other packages.

**Mitigation:**
- ✅ Update packages incrementally
- ✅ Build and test after each package update
- ✅ Review package release notes for breaking changes
- ✅ Verify compatibility matrix for all dependencies

**Contingency:**
- Revert specific package updates if issues arise
- Use compatible intermediate versions if necessary
- Report issues to package maintainers

**Status:** 🟢 Low Priority - Standard packages with stable APIs

---

#### RISK-005: .NET 10 SDK Deployment
**Category:** Environmental  
**Probability:** LOW  
**Impact:** HIGH  
**Severity:** 🟡 Moderate

**Description:**
Production or deployment environments may not have .NET 10 SDK/runtime installed.

**Mitigation:**
- ✅ Verify deployment environment capabilities early
- ✅ Plan .NET 10 runtime deployment alongside application update
- ✅ Consider self-contained deployment if runtime control is limited
- ✅ Update CI/CD pipelines for .NET 10 SDK

**Contingency:**
- Use self-contained publishing: `dotnet publish --self-contained`
- Create deployment package with embedded runtime
- Coordinate with infrastructure team for runtime installation

**Status:** 🟢 Manageable - Standard deployment concern

---

#### RISK-006: Rollback Complexity
**Category:** Process  
**Probability:** LOW  
**Impact:** LOW  
**Severity:** 🟢 Low

**Description:**
Need to rollback to .NET 8 version if critical issues discovered.

**Mitigation:**
- ✅ Maintain .NET 8 version in separate branch
- ✅ Use clear Git tagging for versions
- ✅ Document rollback procedure
- ✅ Test rollback process before production deployment

**Contingency:**
- Quick branch switch: `git checkout net10-update`
- No data migration means clean rollback
- Single project means no cascading dependencies

**Status:** 🟢 Well-Controlled - Simple rollback path

---

### Risk Heat Map

```
         Impact →
         LOW    MEDIUM   HIGH
       ┌──────┬────────┬────────┐
HIGH   │      │ RISK-2 │ RISK-1 │
       ├──────┼────────┼────────┤
MEDIUM │      │ RISK-3 │        │
       ├──────┼────────┼────────┤
LOW    │RISK-6│ RISK-4 │ RISK-5 │
       └──────┴────────┴────────┘
       Probability →
```

**Critical Path Risks:** RISK-001 (System.CommandLine migration)

---

### Risk Monitoring Plan

**Weekly Checkpoints:**
- Review progress on breaking change fixes
- Track time spent vs. estimates
- Document any unexpected issues

**Escalation Triggers:**
- Development time exceeds 10 hours
- Inability to resolve System.CommandLine API changes
- Discovery of major behavioral differences
- Inability to complete functional testing

**Escalation Path:**
1. Consult System.CommandLine documentation/community
2. Consider architectural alternatives
3. Engage .NET migration specialists
4. Evaluate cost/benefit of staying on .NET 8

---

### Acceptance Criteria for Risk Closure

**RISK-001:** All System.CommandLine API changes resolved, clean build achieved  
**RISK-002:** Comprehensive functional testing completed, no behavioral deviations  
**RISK-003:** Integration test plan documented, key scenarios validated  
**RISK-004:** All packages updated, no compatibility issues  
**RISK-005:** Deployment environment verified or self-contained deployment prepared  
**RISK-006:** Rollback procedure tested and documented

---

## Source Control Strategy

### Branch Structure

```
main (net8.0)
│
├── net10-update (source branch - current state)
│   └── upgrade-to-NET10 (active upgrade branch) ⭐ YOU ARE HERE
```

**Current Status:**
- ✅ Working on branch: `upgrade-to-NET10`
- ✅ Source branch: `net10-update`
- ✅ No pending changes (clean starting state)

---

### Commit Strategy

#### Recommended Commit Sequence

**1. Setup Commit**
```bash
git commit -m "chore: initialize .NET 10 upgrade - add assessment and plan"
```
**Changes:**
- Assessment documentation
- Upgrade plan
- Initial configuration

---

**2. Target Framework Commit**
```bash
git commit -m "build: update target framework to net10.0"
```
**Changes:**
- `ElasticShardSqlUtil.csproj` - TargetFramework property

**Note:** This commit will NOT build successfully (expected)

---

**3. Package Update Commit**
```bash
git commit -m "build: update NuGet packages to .NET 10 compatible versions

- Microsoft.Extensions.Configuration: 8.0.0 -> 10.0.5
- Microsoft.Extensions.Configuration.Json: 8.0.1 -> 10.0.5
- Microsoft.Extensions.Hosting: 8.0.0 -> 10.0.5
- System.Configuration.ConfigurationManager: 8.0.1 -> 10.0.5"
```
**Changes:**
- `ElasticShardSqlUtil.csproj` - PackageReference updates

**Note:** This commit will NOT build successfully (System.CommandLine issues remain)

---

**4. API Migration Commit(s)**

**Option A - Single Commit:**
```bash
git commit -m "refactor: migrate System.CommandLine API to .NET 10 compatible patterns

- Replace CommandHandler.Create with SetHandler
- Update Option initialization patterns
- Remove deprecated Command.Handler property usage
- Update CommandLineBuilder configuration

Fixes 161 API compatibility issues in Program.cs"
```

**Option B - Incremental Commits:**
```bash
git commit -m "refactor: update CommandHandler.Create to SetHandler pattern"
git commit -m "refactor: update Option initialization for .NET 10"
git commit -m "refactor: update CommandLineBuilder configuration"
```

**Changes:**
- `Program.cs` - All System.CommandLine API updates

**Note:** Final commit in this sequence SHOULD build successfully ✅

---

**5. Metadata Update Commit**
```bash
git commit -m "chore: update version and release notes for .NET 10

Version 1.3.0 -> 1.4.0"
```
**Changes:**
- `ElasticShardSqlUtil.csproj` - Version, AssemblyVersion, PackageReleaseNotes

---

**6. Documentation Commit (if applicable)**
```bash
git commit -m "docs: update documentation for .NET 10 compatibility"
```
**Changes:**
- README.md updates
- Any other documentation changes

---

### Pull Request Strategy

#### PR Title
```
Upgrade ElasticShardSqlUtil to .NET 10.0 (LTS)
```

#### PR Description Template
```markdown
## Summary
Upgrades the ElasticShardSqlUtil project from .NET 8.0 to .NET 10.0 (LTS).

## Changes Made
- ✅ Updated TargetFramework to net10.0
- ✅ Updated 4 Microsoft.Extensions.* NuGet packages to version 10.0.5
- ✅ Migrated System.CommandLine API usage to .NET 10 compatible patterns
- ✅ Resolved 161 API compatibility issues
- ✅ Updated project version to 1.4.0

## Breaking Changes
- **System.CommandLine API Migration**: Replaced deprecated CommandHandler.Create pattern with SetHandler
- **Option Initialization**: Updated property-based initialization to .NET 10 patterns

## Testing Performed
- [x] Project builds successfully with zero errors
- [x] All CLI commands tested manually
- [ ] Integration tests with Azure SQL Database (pending test environment)

## Migration Details
- **Assessment Report**: `.github/upgrades/scenarios/new-dotnet-version_040bb5/assessment.md`
- **Migration Plan**: `.github/upgrades/scenarios/new-dotnet-version_040bb5/plan.md`

## Deployment Notes
- Requires .NET 10 SDK/Runtime in deployment environment
- No database schema changes
- No configuration changes required

## Rollback Plan
If issues arise, revert to `net10-update` branch (maintains .NET 8 compatibility).
```

---

### Branch Protection & Review

**Recommended Review Checklist:**
- [ ] All commits build successfully
- [ ] No compiler warnings for obsolete APIs
- [ ] Package versions align with .NET 10
- [ ] Version numbers updated appropriately
- [ ] Release notes accurately describe changes
- [ ] Critical CLI commands tested
- [ ] Documentation updated

**Reviewers:**
- Primary: [Team Lead / Senior Developer]
- Secondary: [CLI/Infrastructure Specialist]

---

### Merge Strategy

**Recommended:** Squash and Merge OR Regular Merge

**Squash Merge:**
```
Pros: Clean history with single upgrade commit
Cons: Loses granular change history
```

**Regular Merge:**
```
Pros: Preserves detailed commit history
Cons: More commits in main branch
```

**Recommendation:** Use **Regular Merge** to preserve incremental progress and make troubleshooting easier.

---

### Post-Merge Activities

**1. Tag Release**
```bash
git checkout main
git pull origin main
git tag -a v1.4.0 -m "Version 1.4.0 - .NET 10 Upgrade"
git push origin v1.4.0
```

**2. Update CI/CD Pipelines**
- Ensure build servers have .NET 10 SDK
- Update build scripts to target net10.0
- Verify automated tests pass

**3. Clean Up Branches**
```bash
# Keep upgrade branch temporarily for reference
# Delete after 30 days if no issues
git branch -d upgrade-to-NET10  # (after 30 days)
```

---

### Emergency Rollback Procedure

**If critical issues are discovered after merge:**

```bash
# Option 1: Revert the merge commit
git checkout main
git revert -m 1 <merge-commit-hash>
git push origin main

# Option 2: Branch from pre-upgrade state
git checkout -b hotfix-revert-net10 <commit-before-upgrade>
git push origin hotfix-revert-net10

# Deploy hotfix-revert-net10 to production
```

**Rollback Time:** < 5 minutes  
**Impact:** Reverts to .NET 8 version, all functionality restored

---

### Commit Hygiene

**Best Practices:**
- ✅ Commit frequently with clear messages
- ✅ Keep commits atomic (one logical change per commit)
- ✅ Build compiles before committing (except intentional WIP commits)
- ✅ Use conventional commit format (feat, fix, refactor, chore, docs, build)
- ✅ Reference issue numbers if applicable

**Commit Message Format:**
```
<type>: <short description>

<optional detailed description>

<optional footer>
```

**Examples:**
```
build: update target framework to net10.0
refactor: migrate CommandHandler.Create to SetHandler pattern
chore: update version to 1.4.0
docs: add .NET 10 migration notes to README
```

---

## Success Criteria

### Build Success Criteria

#### ✅ Compilation
- [ ] **Zero build errors** when targeting net10.0
- [ ] **Zero warnings** related to obsolete APIs
- [ ] Clean NuGet package restore
- [ ] All projects build in Release and Debug configurations

**Validation Command:**
```bash
dotnet clean
dotnet restore
dotnet build --configuration Release
dotnet build --configuration Debug
```

**Expected Output:**
```
Build succeeded.
    0 Warning(s)
    0 Error(s)
```

---

### Functional Success Criteria

#### ✅ Command-Line Interface
- [ ] All CLI commands execute without errors
- [ ] Help system works correctly (`--help`)
- [ ] Required parameters are enforced
- [ ] Optional parameters work as expected
- [ ] Error messages are clear and appropriate
- [ ] Command hierarchy (subcommands) functions correctly

**Critical Commands to Verify:**
- `dotnet run -- --help`
- `dotnet run -- --version`
- `dotnet run -- recovery-manager --help`
- `dotnet run -- recovery-manager attach-shard --tenants 1 2 3`
- All other recovery-manager subcommands
- All other top-level commands

---

#### ✅ Configuration Loading
- [ ] `appsettings.json` loads successfully
- [ ] Configuration values bind correctly
- [ ] Environment-specific configuration works (if applicable)
- [ ] Connection strings parse correctly

---

#### ✅ Dependency Injection
- [ ] IHost initialization succeeds
- [ ] Services resolve correctly
- [ ] Scoped lifetime management works
- [ ] Configuration injection works

---

### Compatibility Success Criteria

#### ✅ Package Compatibility
- [ ] All NuGet packages restore successfully
- [ ] No package version conflicts
- [ ] All packages compatible with net10.0
- [ ] Transitive dependencies resolve correctly

**Validation Command:**
```bash
dotnet list package
dotnet list package --vulnerable
dotnet list package --deprecated
```

**Expected:** No vulnerabilities, no deprecated packages (unless intentional)

---

#### ✅ API Compatibility
- [ ] No usage of obsolete/deprecated APIs
- [ ] No compiler warnings for API compatibility
- [ ] All System.CommandLine APIs updated to current patterns
- [ ] No runtime API exceptions

---

### Performance Success Criteria

#### ✅ Startup Performance
- [ ] Application starts without delay
- [ ] No significant performance regression vs .NET 8
- [ ] Command parsing performance acceptable

**Benchmark (Informal):**
```bash
# Time simple command execution
time dotnet run -- --help
```

**Expected:** < 2 seconds for simple commands

---

#### ✅ Memory Usage
- [ ] No memory leaks observed
- [ ] Memory usage comparable to .NET 8 version

---

### Quality Success Criteria

#### ✅ Code Quality
- [ ] No code analysis warnings
- [ ] Consistent code style maintained
- [ ] No "TODO" or "HACK" comments introduced
- [ ] Code follows existing patterns

---

#### ✅ Documentation
- [ ] Version numbers updated
- [ ] Release notes accurately describe changes
- [ ] README updated (if necessary)
- [ ] Migration plan documented
- [ ] Breaking changes cataloged

---

#### ✅ Version Control
- [ ] All changes committed with clear messages
- [ ] Upgrade branch ready for merge
- [ ] No uncommitted changes
- [ ] Clean commit history

---

### Testing Success Criteria

#### ✅ Manual Testing
- [ ] All CLI commands tested individually
- [ ] Error scenarios tested
- [ ] Edge cases validated
- [ ] Regression testing completed

**Test Coverage:**
- Minimum: All primary commands tested
- Ideal: All commands + error scenarios + edge cases

---

#### ✅ Automated Testing (if tests exist)
- [ ] All existing unit tests pass
- [ ] All integration tests pass
- [ ] Test coverage maintained or improved

**Validation Command:**
```bash
dotnet test --verbosity normal
```

**Expected:** 100% test pass rate

---

### Deployment Success Criteria

#### ✅ Deployment Readiness
- [ ] .NET 10 SDK/Runtime deployment plan defined
- [ ] CI/CD pipeline updated (if applicable)
- [ ] Deployment documentation updated
- [ ] Rollback procedure documented and tested

---

#### ✅ Environment Validation
- [ ] .NET 10 SDK available in target environment
- [ ] Dependencies available in deployment environment
- [ ] Configuration files deployed correctly

---

### Sign-Off Criteria

#### Must-Have (Blocking)
- ✅ **Clean build** with zero errors
- ✅ **All critical CLI commands functional**
- ✅ **No API compatibility warnings**
- ✅ **Package updates completed**

#### Should-Have (Important)
- ✅ All commands tested manually
- ✅ Documentation updated
- ✅ Code review completed
- ✅ Release notes finalized

#### Nice-to-Have (Optional)
- Integration tests with Azure SQL (if environment available)
- Performance benchmarking
- Automated test coverage

---

### Final Acceptance

**The upgrade is considered successful when:**

1. ✅ **Build Success:** Clean compilation with zero errors/warnings
2. ✅ **Functional Success:** All CLI commands work as expected
3. ✅ **Quality Success:** Code review approved, documentation complete
4. ✅ **Deployment Success:** Ready to deploy to production

**Sign-Off:**
- [ ] Developer: _______________ Date: ___________
- [ ] Code Reviewer: _______________ Date: ___________
- [ ] Tech Lead: _______________ Date: ___________

---

### Post-Migration Monitoring

**First 7 Days After Deployment:**
- Monitor application logs for errors
- Track any unexpected behavior
- Gather user feedback
- Document any issues for future migrations

**Success Metrics:**
- Zero critical bugs
- No rollback required
- Positive user feedback
- Application stability maintained

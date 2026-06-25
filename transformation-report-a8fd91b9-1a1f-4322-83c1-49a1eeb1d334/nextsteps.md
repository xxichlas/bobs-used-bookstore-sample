# Next Steps

## Issues resolved
- Transformed Bookstore.Domain.csproj to net8.0
- Transformed Bookstore.Data.csproj to net8.0
- Transformed Bookstore.Web.csproj to net8.0
- Transformed Bookstore.Cdk.csproj to net8.0
- Transformed Bookstore.Domain.Tests.csproj to net8.0

## Overview

The transformation appears to have completed successfully. No build errors were detected across any of the projects in the solution:

- `Bookstore.Data`
- `Bookstore.Domain.Tests`
- `Bookstore.Cdk`
- `Bookstore.Web`
- `Bookstore.Domain`

The following steps outline how to validate, test, and deploy the migrated solution.

---

## 1. Restore Dependencies

Run a full NuGet package restore to ensure all dependencies are resolved correctly in the new target framework:

```bash
dotnet restore
```

Review the output for any warnings about deprecated or incompatible packages and update them if necessary using:

```bash
dotnet list package --outdated
dotnet add <project> package <PackageName>
```

---

## 2. Build the Solution

Perform a full solution build to confirm there are no compilation issues:

```bash
dotnet build --configuration Release
```

Ensure the build completes with zero errors and review any warnings, particularly those related to nullable reference types, obsolete APIs, or platform compatibility.

---

## 3. Run Unit Tests

Execute the test project to verify that existing business logic behaves as expected after migration:

```bash
dotnet test app/Bookstore.Domain.Tests/Bookstore.Domain.Tests.csproj --configuration Release --verbosity normal
```

- Review test results for any failures or skipped tests.
- If tests were previously written against .NET Framework-specific behavior, inspect them for compatibility issues with the new runtime.

---

## 4. Validate the Web Application Locally

Run the `Bookstore.Web` project locally to confirm the application starts and functions correctly:

```bash
dotnet run --project app/Bookstore.Web/Bookstore.Web.csproj --configuration Release
```

- Navigate to the application in a browser and verify core functionality such as page rendering, data access, and any authentication flows.
- Check the console output and application logs for runtime exceptions or warnings.

---

## 5. Validate the Data Layer

Since `Bookstore.Data` handles data access, verify the following:

- **Database Migrations**: If Entity Framework Core is in use, confirm that migrations are up to date:

```bash
dotnet ef migrations list --project app/Bookstore.Data/Bookstore.Data.csproj --startup-project app/Bookstore.Web/Bookstore.Web.csproj
```

- **Connection Strings**: Confirm that connection strings in `appsettings.json` or environment-specific configuration files point to the correct database instance.
- **Schema Compatibility**: Run the application against a test database and verify that reads and writes function as expected.

---

## 6. Review the CDK Project

The `Bookstore.Cdk` project likely defines infrastructure. Review it to ensure:

- Any SDK or library versions referenced (e.g., AWS CDK) are compatible with the new .NET version.
- Configuration values such as environment names, regions, or resource identifiers are correct for your target environment.

Build and synthesize the CDK project independently to confirm it is functional:

```bash
dotnet build app/Bookstore.Cdk/Bookstore.Cdk.csproj --configuration Release
```

---

## 7. Check Target Framework Consistency

Verify that all projects in the solution are targeting the same .NET version. Open each `.csproj` file and confirm the `<TargetFramework>` element is consistent, for example:

```xml
<TargetFramework>net8.0</TargetFramework>
```

Mismatched target frameworks between projects can cause runtime issues even when the build succeeds.

---

## 8. Review Removed or Changed APIs

Cross-platform .NET removes certain APIs that were available in .NET Framework. Use the .NET Upgrade Assistant compatibility analyzer or the Platform Compatibility Analyzer to identify any remaining usage of unsupported APIs:

```bash
dotnet add package Microsoft.DotNet.PlatformAbstractions
```

Alternatively, review the [.NET Framework to .NET API differences documentation](https://learn.microsoft.com/en-us/dotnet/core/porting/net-framework-tech-unavailable) for any APIs relevant to this solution.

---

## 9. Publish the Application

Once validation is complete, publish the application to confirm the output is correct:

```bash
dotnet publish app/Bookstore.Web/Bookstore.Web.csproj --configuration Release --output ./publish
```

Review the contents of the `./publish` directory to ensure all required files, static assets, and configuration files are present.
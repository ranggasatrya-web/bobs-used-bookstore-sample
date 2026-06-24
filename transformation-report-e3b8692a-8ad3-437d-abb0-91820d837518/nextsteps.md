# Next Steps

## Issues resolved
- Transformed Bookstore.Domain.csproj to net8.0
- Transformed Bookstore.Data.csproj to net8.0
- Transformed Bookstore.Web.csproj to net8.0
- Transformed Bookstore.Cdk.csproj to net8.0
- Transformed Bookstore.Domain.Tests.csproj to net8.0

## Summary

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

Review the output for any warnings related to package compatibility or version conflicts.

---

## 2. Build the Solution

Perform a full solution build to confirm there are no compilation issues:

```bash
dotnet build --configuration Release
```

Address any warnings that appear, particularly those related to nullable reference types, deprecated APIs, or platform compatibility.

---

## 3. Run Unit Tests

Execute the test project to verify that existing business logic behaves as expected after migration:

```bash
dotnet test app/Bookstore.Domain.Tests/Bookstore.Domain.Tests.csproj --configuration Release --verbosity normal
```

Review the test output for any failures or skipped tests. If tests that previously passed are now failing, investigate whether the failures are due to behavioral differences in the new runtime or framework API changes.

---

## 4. Verify Runtime Behavior of the Web Project

Run the web application locally to confirm it starts and functions correctly:

```bash
dotnet run --project app/Bookstore.Web/Bookstore.Web.csproj
```

Check the following:
- The application starts without exceptions.
- Routing and middleware behave as expected.
- Any static files, views, or Razor pages render correctly.
- Authentication and authorization flows work if applicable.

---

## 5. Verify Data Layer

Confirm that the `Bookstore.Data` project functions correctly against your target database:

- If using Entity Framework Core, verify that your `DbContext` configuration is correct for the new framework version.
- Run any pending migrations or verify the schema is up to date:

```bash
dotnet ef database update --project app/Bookstore.Data/Bookstore.Data.csproj --startup-project app/Bookstore.Web/Bookstore.Web.csproj
```

- Test basic CRUD operations to confirm data access is working as expected.

---

## 6. Review the CDK Project

Inspect `Bookstore.Cdk` to ensure that any infrastructure definitions still align with the updated application structure. Verify that:

- Resource definitions (e.g., environment variables, ports, connection strings) match what the migrated application expects at runtime.
- Any references to deployment artifacts or build output paths are still valid.

---

## 7. Check for Removed or Changed APIs

Review the code for use of any APIs that were removed or significantly changed between the legacy .NET Framework and the current .NET version. Tools that can assist with this include:

- [.NET Upgrade Assistant](https://learn.microsoft.com/en-us/dotnet/core/porting/upgrade-assistant-overview)
- [Platform Compatibility Analyzer](https://learn.microsoft.com/en-us/dotnet/standard/analyzers/platform-compat-analyzer)

Pay particular attention to:
- `System.Web` usages, which are not available in cross-platform .NET.
- Windows-specific APIs that may not be available on Linux or macOS.
- Configuration system changes (`ConfigurationManager` vs `Microsoft.Extensions.Configuration`).

---

## 8. Test on Target Platform

If the goal of the migration is to run on a non-Windows platform (Linux or macOS), ensure you run and test the application on that target platform, not just on Windows. Some compatibility issues only surface at runtime on the intended target OS.
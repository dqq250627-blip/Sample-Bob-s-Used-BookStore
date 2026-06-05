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

Run a NuGet package restore to ensure all dependencies are resolved correctly before building:

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

Ensure the build completes with zero errors and review any warnings that may indicate compatibility concerns.

---

## 3. Run Unit Tests

Execute the test project to validate that existing business logic behaves as expected after migration:

```bash
dotnet test app/Bookstore.Domain.Tests/Bookstore.Domain.Tests.csproj --configuration Release --verbosity normal
```

Review the test output for:
- Any failing tests that may indicate behavioral regressions
- Any tests that were skipped or could not be discovered

If tests are failing, compare the test logic against the domain logic in `Bookstore.Domain` to identify any regressions introduced during transformation.

---

## 4. Validate the Data Layer

Since `Bookstore.Data` handles data access, verify the following:

- **Connection strings** in configuration files (e.g., `appsettings.json`) are correct and point to the intended database.
- If Entity Framework Core is used, confirm that migrations are up to date:

```bash
dotnet ef migrations list --project app/Bookstore.Data
dotnet ef database update --project app/Bookstore.Data
```

- If the project previously used Entity Framework 6 (EF6), confirm it has been migrated to EF Core and that all queries and relationships function as expected.

---

## 5. Validate the Web Application

Run the `Bookstore.Web` project locally and verify core functionality:

```bash
dotnet run --project app/Bookstore.Web --configuration Release
```

Check the following:
- The application starts without runtime errors.
- All routes and pages load correctly.
- Any middleware, authentication, or session configuration that existed in the legacy project is functioning as expected in the new ASP.NET Core pipeline.
- Static files, bundling, and any front-end assets are served correctly.

---

## 6. Review the CDK Project

If `Bookstore.Cdk` is used for infrastructure definitions, review the generated or migrated CDK code to ensure:

- All resource definitions (e.g., databases, storage, networking) reflect the intended infrastructure.
- Any environment-specific configuration values are externalized and not hardcoded.

---

## 7. Review Configuration Files

Cross-platform .NET no longer uses `Web.config` or `App.config` as the primary configuration mechanism. Confirm that:

- All settings have been moved to `appsettings.json` or environment variables.
- Any `Web.config` transforms or `App.config` sections that were present in the legacy project have been accounted for.
- Secrets are not stored in source-controlled configuration files. Use `dotnet user-secrets` for local development:

```bash
dotnet user-secrets init --project app/Bookstore.Web
dotnet user-secrets set "ConnectionStrings:Default" "your_connection_string"
```

---

## 8. Check Target Framework Compatibility

Verify that all projects are targeting the intended .NET version:

```bash
dotnet --version
```

Open each `.csproj` file and confirm the `<TargetFramework>` element reflects the correct version (e.g., `net8.0`). Ensure consistency across all projects in the solution.

---

## 9. Perform a Final Smoke Test

Before deploying, perform a manual walkthrough of the application's primary user flows, including:

- Browsing and searching for books
- Any authentication or account management flows
- Any data entry or transactional operations

This helps catch runtime issues that unit tests may not cover.
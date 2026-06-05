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

Review the output for any warnings related to package compatibility or version conflicts. Address any packages that may have been marked as deprecated or that target older frameworks.

---

## 2. Build the Solution

Perform a full solution build to confirm there are no compilation issues:

```bash
dotnet build --configuration Release
```

Ensure the build completes with zero errors and review any warnings, particularly those related to nullable reference types, obsolete APIs, or platform compatibility.

---

## 3. Run Unit Tests

Execute the test project to validate that existing business logic behaves as expected after migration:

```bash
dotnet test app/Bookstore.Domain.Tests/Bookstore.Domain.Tests.csproj --configuration Release --verbosity normal
```

Review the test results carefully. Any failing tests should be investigated to determine whether they represent regressions introduced during migration or pre-existing issues.

---

## 4. Validate the Web Application Locally

Run the web application locally to verify that it starts correctly and that core functionality is intact:

```bash
dotnet run --project app/Bookstore.Web/Bookstore.Web.csproj --configuration Release
```

Manually verify the following:

- Application starts without runtime exceptions
- Database connectivity is functional (check connection strings in `appsettings.json` for any environment-specific values that may need updating)
- Core pages and routes load correctly
- Any authentication or authorization flows work as expected

---

## 5. Validate the Data Layer

Confirm that the `Bookstore.Data` project is functioning correctly, particularly if it uses Entity Framework Core:

- Check that all migrations are present and up to date:

```bash
dotnet ef migrations list --project app/Bookstore.Data/Bookstore.Data.csproj --startup-project app/Bookstore.Web/Bookstore.Web.csproj
```

- If the database schema needs to be updated to reflect any migration changes:

```bash
dotnet ef database update --project app/Bookstore.Data/Bookstore.Data.csproj --startup-project app/Bookstore.Web/Bookstore.Web.csproj
```

---

## 6. Review the CDK Project

The `Bookstore.Cdk` project likely defines infrastructure. Review it to ensure any environment-specific configuration (such as runtime identifiers, region settings, or resource names) aligns with the target deployment environment. Confirm that the CDK project references the correct output paths from the updated .NET build.

---

## 7. Verify Target Framework Consistency

Confirm that all projects in the solution are targeting the same .NET version. Open each `.csproj` file and verify the `<TargetFramework>` element is consistent, for example:

```xml
<TargetFramework>net8.0</TargetFramework>
```

Mismatched target frameworks between projects can cause runtime issues that do not surface as build errors.

---

## 8. Check for Removed or Changed APIs

Review the code for any use of APIs that were available in .NET Framework but have changed behavior or been removed in cross-platform .NET. Common areas to check include:

- `System.Web` references (should have been replaced during transformation)
- `ConfigurationManager` usage (should be replaced with `Microsoft.Extensions.Configuration`)
- Windows-specific APIs that may compile but fail at runtime on non-Windows platforms

---

## 9. Deploy the Application

Once all validation steps pass, publish the application for deployment:

```bash
dotnet publish app/Bookstore.Web/Bookstore.Web.csproj --configuration Release --output ./publish
```

Verify the contents of the `./publish` directory and deploy to the target environment according to your existing deployment process.
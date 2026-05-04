# sales.solution
This repository contains the backend solution for the Sales sample application. It is organized into multiple projects that follow a layered architecture so each concern is isolated and easy to run locally.

Projects

- `Sales.Api` — ASP.NET Core Web API and app host. Exposes HTTP endpoints and configures services, EF Core, and third-party integrations (Stripe, Swagger).
- `Sales.Data` — Entity Framework Core data access layer and migrations.
- `Sales.Domain` — Pure domain model (entities, value objects, domain rules).
- `Sales.Services` — Application services and orchestration (use-cases).
- `Sales.Utility` — Shared helpers and utilities used across the solution.
- `Sales.Desktop.Manager` — WPF desktop client that consumes the API.

High-level integration

- `Sales.Api` depends on `Sales.Services`, `Sales.Domain`, and `Sales.Data` to serve HTTP requests.
- `Sales.Data` depends on `Sales.Domain` for entity types and contains EF Core migrations; `Sales.Api` is the typical startup project for design-time EF operations.
- `Sales.Desktop.Manager` is an independent WPF client that calls the API (default base URL `https://localhost:7282/api/` configured in `Sales.Desktop.Manager\App.config`).

Prerequisites

- .NET 10 SDK
- Visual Studio 2026 or `dotnet` CLI
- SQL Server or other EF Core provider if using a real database

Build and run (recommended — Visual Studio)

1. Open the solution in Visual Studio and restore NuGet packages.
2. Set `Sales.Api` as the startup project and run it (F5). The API typically exposes Swagger at `https://localhost:{port}/swagger`.
3. Set `Sales.Desktop.Manager` as the startup project and run the desktop app. Ensure `Sales.Api` is running and the `SalesApiUrl` in `Sales.Desktop.Manager\App.config` matches the API launch URL.

Build and run (command line)

- Build solution: `dotnet build`
- Run API: `dotnet run --project Sales.Api`
- Launch the desktop app from Visual Studio (WPF) or open the project in an IDE that supports WPF on .NET 10.

Database migrations

- Add migration: `dotnet ef migrations add Name --project Sales.Data --startup-project Sales.Api`
- Apply migrations: `dotnet ef database update --project Sales.Data --startup-project Sales.Api`

Tests

- Run all tests: `dotnet test`

Configuration

- API configuration is in `Sales.Api` (`appsettings.json` / environment variables / user secrets).
- Update `Sales.Desktop.Manager\App.config` if the API runs on a different URL/port.

Troubleshooting

- If the desktop client cannot reach the API, confirm the API is running, that the base URL is correct, and that there are no firewall/HTTPS issues.
- Check the application output and API logs for startup exceptions.

Contributing

- Open issues for bugs or feature requests.
- Keep PRs focused and include tests for behavior changes.

License

See repository root for license information.

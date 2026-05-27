# sales.solution
This repository contains the backend solution for the Sales sample application. It is organized into multiple projects that follow a layered architecture so each concern is isolated and easy to run locally.

Projects

- `Sales.Api` — ASP.NET Core Web API and app host. Exposes HTTP endpoints and configures services, EF Core, and third-party integrations (Stripe, Swagger).
- `Sales.Data` — Entity Framework Core data access layer and migrations.
- `Sales.Domain` — Pure domain model (entities, value objects, domain rules).
- `Sales.Services` — Application services and orchestration (use-cases).
- `Sales.Utility` — Shared helpers and utilities used across the solution.
- `Sales.Desktop.Manager` — WPF desktop client that consumes the API.
- `Sales.Ai` — AI/ML helpers and integration layer. Contains services that call external AI providers (or local models) for features such as recommendations, classification, or natural language processing. Designed to be called from application services.
- `Sales.MessageBroker` — Message broker integration and background workers. Hosts message contracts, producers, consumers, and wiring for a broker (RabbitMQ/Kafka/Service Bus). Used for async workflows and event-driven communication between services.
- `Sales.Fulfillment` — Fulfillment and order-processing background service. Implements long-running workflows (shipping, inventory updates, external fulfillment provider integration) and typically runs as a worker service.

Approximate total size

The entire backend solution is approximately 7,200 lines of code (LOC) across all projects. This is a rough estimate intended to give a sense of scale. Per-project estimates (approx.):

- Sales.Api: ~900 LOC
- Sales.Data: ~400 LOC
- Sales.Domain: ~800 LOC
- Sales.Services: ~700 LOC
- Sales.Utility: ~200 LOC
- Sales.Desktop.Manager: ~1,000 LOC
- Sales.Ai: ~300 LOC
- Sales.MessageBroker: ~350 LOC
- Sales.Fulfillment: ~1,200 LOC
- Sales.Calendar: ~600 LOC
- Sales.Archives: ~350 LOC
- Sales.Maintenance.Services: ~420 LOC

These numbers are approximations based on typical module responsibilities and may vary as the codebase changes.

High-level integration

- `Sales.Api` depends on `Sales.Services`, `Sales.Domain`, and `Sales.Data` to serve HTTP requests.
- `Sales.Data` depends on `Sales.Domain` for entity types and contains EF Core migrations; `Sales.Api` is the typical startup project for design-time EF operations.
- `Sales.Desktop.Manager` is an independent WPF client that calls the API (default base URL `https://localhost:7282/api/` configured in `Sales.Desktop.Manager\App.config`).
- `Sales.Ai` is invoked by application services (via `Sales.Services`) to provide AI-driven features such as recommendations or text processing. It is intentionally kept as an integration layer so the implementation can swap providers or models without changing core domain logic.
- `Sales.MessageBroker` defines message contracts and contains producers/consumers used by `Sales.Api`, `Sales.Fulfillment`, and other background services to perform asynchronous work and decouple components.
- `Sales.Fulfillment` subscribes to order/payment events (via the message broker or direct service calls) and handles fulfillment workflows, external provider calls, and inventory reconciliation. It can run as a hosted worker process or container.

Prerequisites

- .NET 10 SDK
- Visual Studio 2026 or `dotnet` CLI
- SQL Server or other EF Core provider if using a real database
- Message broker (if using asynchronous features): RabbitMQ, Kafka, or Azure Service Bus depending on your configuration. Ensure the broker is running and connection settings are configured in appsettings or environment variables.
- (Optional) AI provider credentials or local model runtime: configure API keys or model endpoints used by `Sales.Ai` via configuration or secrets.

Build and run (recommended — Visual Studio)

1. Open the solution in Visual Studio and restore NuGet packages.
2. Set `Sales.Api` as the startup project and run it (F5). The API typically exposes Swagger at `https://localhost:{port}/swagger`.
3. If you use asynchronous features, start your message broker (RabbitMQ/Kafka/Service Bus) and ensure `Sales.MessageBroker`/worker consumers can reach it via configured connection strings.
4. Start `Sales.Fulfillment` as a worker project (if configured to run locally) to process fulfillment jobs and consume messages.
5. If AI features are required, ensure `Sales.Ai` configuration is set (API keys or model endpoints) before exercising AI-driven endpoints.
6. Set `Sales.Desktop.Manager` as the startup project and run the desktop app. Ensure `Sales.Api` is running and the `SalesApiUrl` in `Sales.Desktop.Manager\App.config` matches the API launch URL.

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

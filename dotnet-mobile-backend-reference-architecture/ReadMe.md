# GPSRadius — Architecture & Design Patterns Reference

This repository is a reference implementation showing how I structure a real-world .NET solution across:
- A .NET MAUI mobile client (MVVM)
- An ASP.NET Core REST API backend
- A shared "Core" project for use-cases, validation, and interfaces
- Infrastructure integrations (AWS Cognito/S3/Secrets Manager, MongoDB)
- CI/CD and containerized deployment (GitHub Actions → ECR → AWS App Runner)

The goal is to demonstrate architecture and design patterns in a practical codebase (not a polished product).

---

## Tech Stack

- Client: .NET MAUI, XAML, MVVM
- Backend: ASP.NET Core Web API (REST)
- Data: MongoDB
- Cloud: AWS (Cognito, S3, Secrets Manager), AWS App Runner
- CI/CD: GitHub Actions, Docker

---

## Architecture Summary

- **Client–Server / 3-Tier**: Mobile client → REST API → database/cloud services
- **Layered / "Clean-ish" separation by projects**:
  - `GPSRadius.Core` = use-cases, validation, DTOs, interfaces (ports)
  - `Infrastructure` = implementations (AWS, avatar, etc.)
  - `GPSRadius.MAUI` = UI delivery layer (MVVM)
  - `GPSRadius.API` = backend delivery layer (controllers + middleware)
- **Ports & Adapters (Hexagonal)**: interfaces defined in Core; platform/AWS adapters in MAUI/Infrastructure
- **Cloud-native deployment**: API containerized + deployed via CI/CD to App Runner
- **Event-driven (in-process only)**: MAUI uses an in-process messenger for decoupled UI updates (not Kafka/RabbitMQ)

### Why this is not "microservices"
This solution has a **single deployable backend service** (`GPSRadius.API`) and primarily uses **synchronous REST**. There are no independently deployable domain services or a distributed event broker in this repo.

---

## Design Patterns Highlighted (GoF + Practical Patterns)

GoF patterns used in this codebase:
- **Adapter** (platform APIs behind interfaces)
- **Chain of Responsibility** (ASP.NET Core middleware; HttpClient handlers)
- **Command** (MVVM commands)
- **Decorator** (DelegatingHandler pipeline for cross-cutting HTTP behavior)
- **Facade** (AWS/auth flows wrapped behind service APIs)
- **Factory (factory-style construction)** (centralized HttpClient/client creation via helper + DI)
- **Observer** (INotifyPropertyChanged)

Non-GoF patterns and practices used:
- Use Case / Application Service (explicit workflow orchestration)
- Validation + mapping/hydration helpers
- In-process message bus (Event Aggregator style)
- Concurrency "single-flight" gates (SemaphoreSlim)
- Testing with fakes/spies
- Engineering practices: DI/IoC, DTO boundaries, idempotent endpoints, resilience (rate limiting/retry/bulkhead)

---

## Projects

- `GPSRadius.MAUI/`  
  MAUI UI + ViewModels (MVVM), commands, in-process messaging, client-side HTTP handlers.

- `GPSRadius.Core/`  
  Core app logic: use-cases, validation, DTOs, interfaces/ports, base view-model utilities.

- `Infrastructure/`  
  Adapters/implementations for AWS and other external integrations used by Core/UI.

- `GPSRadius.API/`  
  ASP.NET Core Web API: controllers, middleware (correlation IDs, throttling), auth, MongoDB integration.

- `.github/workflows/`  
  CI/CD pipelines (build/test/deploy).

---

## Full Architecture & Patterns Write-up

For the detailed write-up with evidence and code references, see:

- `docs/architecture-and-patterns.md`

---

## Notes

This repo is intentionally structured to be easy to review in an interview setting:
- clear project boundaries,
- explicit patterns,
- and practical production-minded concerns (auth, throttling, CI/CD).

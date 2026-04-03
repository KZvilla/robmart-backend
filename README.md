# RobMart-Backend - Production Engineering Snapshot

Backend API for a production home-care workflow. This repository is a public-facing summary of the work delivered: authentication, worker-patient assignments, shift lifecycle, clinical records, and operational reporting.

## What this backend solves
- Secure access for operational users with role-based authorization.
- Assignment and rate agreement management between workers and patients.
- Shift lifecycle handling (start, close, payment status).
- Clinical support tracking (vital signs and medication events).
- Operational visibility through reporting/dashboard endpoints.

## My engineering contribution
- Designed and implemented a layered API architecture (routes -> controllers -> services -> data).
- Established validation-first contracts with Zod across boundary inputs.
- Centralized domain error handling with explicit and predictable HTTP mapping.
- Implemented secure middleware baseline: Helmet, CORS policy, rate limits, cookie-based JWT.
- Delivered schema evolution through Prisma migrations and integration-focused tests.

## Key technical decisions
- Cookie-based JWT transport to reduce frontend token exposure.
- Business rules isolated in services to keep controllers thin and testable.
- Explicit domain errors (AppError pattern) to improve observability and debugging.
- Prisma as the ORM boundary to keep data access consistent and maintainable.

## Representative business complexity
- Prevent starting a new shift while another active shift exists.
- Restrict shift closing permissions by role.
- Compute compensation from assignment rate plus special conditions (for example, holiday multiplier).
- Keep shift, notes, and clinical records coherent across the workflow.

## Quality and production readiness
- TypeScript + Express 5 + PostgreSQL + Prisma ORM.
- Zod-based request validation and standardized API error shape.
- Integration testing with Vitest + Supertest.
- Versioned database changes via Prisma migrations.
- OpenAPI/Swagger documentation for endpoint contracts.

## Public scope note
This repository intentionally excludes secrets, infrastructure-sensitive details, and any real patient/client data.

## Architecture diagrams (representative)
The following diagrams are intentionally simplified to show engineering approach without exposing sensitive implementation details.

```mermaid
classDiagram
direction LR

class App {
	+start()
}

class AuthRoutes
class ShiftRoutes
class ClientRoutes
class PatientRoutes
class ReportRoutes

class AuthController {
	+handleAuth()
}
class ShiftController {
	+manageShifts()
}
class ClientController {
	+manageClients()
}
class PatientController {
	+managePatients()
}
class ReportController {
	+generateReports()
}

class AuthService {
	+authenticate()
	+buildSession()
}
class ShiftService {
	+applyShiftRules()
}
class ClientService {
	+applyClientRules()
}
class PatientService {
	+applyPatientRules()
}
class ReportService {
	+buildReportData()
}
class DocumentService {
	+renderDocument()
}

class AuthMiddleware {
	+protect()
}
class ValidateMiddleware {
	+validate()
}
class ErrorHandler {
	+handle()
}

class DataAccess {
	+readWrite()
}

App --> AuthRoutes
App --> ShiftRoutes
App --> ClientRoutes
App --> PatientRoutes
App --> ReportRoutes

AuthRoutes --> AuthController
ShiftRoutes --> ShiftController
ClientRoutes --> ClientController
PatientRoutes --> PatientController
ReportRoutes --> ReportController

AuthRoutes --> ValidateMiddleware
ShiftRoutes --> ValidateMiddleware
ClientRoutes --> ValidateMiddleware
PatientRoutes --> ValidateMiddleware
ReportRoutes --> ValidateMiddleware

ShiftRoutes --> AuthMiddleware
ClientRoutes --> AuthMiddleware
PatientRoutes --> AuthMiddleware
ReportRoutes --> AuthMiddleware

AuthController --> AuthService
ShiftController --> ShiftService
ClientController --> ClientService
PatientController --> PatientService
ReportController --> ReportService

ReportService --> DocumentService

AuthService --> DataAccess
ShiftService --> DataAccess
ClientService --> DataAccess
PatientService --> DataAccess
ReportService --> DataAccess

App --> ErrorHandler
```

```mermaid
sequenceDiagram
autonumber
actor U as Usuario (Enfermero/a)
participant API as Backend API
participant AC as AuthController
participant AS as AuthService
participant AM as AuthMiddleware
participant DC as DashboardController
participant DS as DashboardService
participant SC as ShiftController
participant SS as ShiftService
participant DB as DataAccess

U->>API: POST /auth/login (credenciales)
API->>AC: login(request)
AC->>AS: authenticate(credentials)
AS->>DB: buscar usuario y permisos
DB-->>AS: usuario valido
AS-->>AC: sesion creada (cookie segura)
AC-->>U: 200 OK (logueado)

U->>API: GET /dashboard
API->>AM: protect()
AM-->>API: sessionContext valido
API->>DC: getSummary(sessionContext)
DC->>DS: buildDashboard(sessionContext)
DS->>DB: consultar resumen operativo
DB-->>DS: metricas y estado actual
DS-->>DC: dashboardDTO
DC-->>U: 200 OK (dashboard)

U->>API: POST /shifts/start
API->>AM: protect()
AM-->>API: sessionContext valido
API->>SC: startShift(data minima)
SC->>SS: openShift(sessionContext, data)
SS->>DB: crear turno en estado ACTIVO
DB-->>SS: turno creado
SS-->>SC: shiftDTO
SC-->>U: 201 Created (turno iniciado)

U->>API: POST /shifts/{shiftId}/vitals
API->>AM: protect()
AM-->>API: sessionContext valido
API->>SC: recordVitals(vitalsDTO)
SC->>SS: saveVitals(shiftId, vitalsDTO, sessionContext)
SS->>DB: guardar signos vitales
DB-->>SS: registro confirmado
SS-->>SC: vitalsDTO
SC-->>U: 201 Created (signos guardados)

U->>API: POST /shifts/{shiftId}/finish
API->>AM: protect()
AM-->>API: sessionContext valido
API->>SC: finishShift(shiftId)
SC->>SS: closeShift(shiftId, sessionContext)
SS->>DB: cerrar turno (estado FINALIZADO + endTime)
DB-->>SS: turno actualizado
SS-->>SC: cierre confirmado
SC-->>U: 200 OK (turno finalizado)
```

Security note: diagrams omit infrastructure topology, secrets, internal controls, and any sensitive operational metadata by design.
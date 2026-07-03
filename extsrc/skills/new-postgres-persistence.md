---
name: new-postgres-persistence
description: Add EF Core PostgreSQL module with startup migrations.
---

1. Read `spec/extend/03-entity-framework-core.md`, `spec/extend/07-di-registration.md`.
2. Create `{Domain}.Persistence.PostgreSQL`: `Entities/`, `Configuration/`, `Extensions/`, `Migrations/`, `{Domain}DbContext`.
3. Options: implement `INpgsqlDbContextOptions` with `[Required]` on `ConnectionString` + `Schema`; bind from config section.
4. Register `AddNpgsqlDbContextFactory<TDbContext, TOptions>()` with standard migrations history table + `MigrationHostedService<TDbContext>`.
5. Implement `{IDomainStore}` via `IDbContextFactory<T>` (short-lived context per method).
6. Internal `IDesignTimeDbContextFactory<T>`; migrations via `dotnet ef migrations add` only.

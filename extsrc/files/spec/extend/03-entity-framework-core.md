# Entity Framework Core

### Layout
- One `{Domain}.Persistence.{Provider}` project per bounded context: DbContext, entities, migrations, DI extension.
- Folders: `Configuration/`, `Entities/`, `Extensions/`, `Migrations/`, `{Name}DbContext.cs`, internal design-time factory, domain store implementing `{Domain}.Abstractions`.

### DbContext
- One DbContext per context; inject `IOptions<TOptions>`; `HasDefaultSchema(schema)` in `OnModelCreating`.
- Options implement shared `INpgsqlDbContextOptions` (`ConnectionString` + `Schema`) with `[Required]` on mandatory fields.
- Fluent API for mapping (snake_case columns, `jsonb`, indexes); clean entity POCOs; `HasConversion` for JSON/enums.
- Standardize migrations history table name per solution (e.g. `__ef_migrations_history` in schema scope).

### Registration and usage
- Register `IDbContextFactory<T>` via shared helper (retry, schema-scoped history table); avoid scoped `AddDbContext` unless needed.
- Options: `ConnectionString` + `Schema`, validated with DataAnnotations + `ValidateOnStart()`.
- App code uses abstractions (`I{Domain}Repository`); never EF types.
- Short-lived context per operation: `await using var ctx = await factory.CreateDbContextAsync(ct)`.
- Reads: `AsNoTracking()`; bulk updates: `ExecuteUpdateAsync` / `ExecuteDeleteAsync`.

### Migrations
- Create only via `dotnet ef migrations add <Name> --project <Project>` from solution root.
- Internal `IDesignTimeDbContextFactory<T>` for CLI; startup via `MigrationHostedService<TDbContext>` — no lazy migrate on first call.

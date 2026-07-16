# C# / .NET — domain errors with localized user messages

### Domain Exception contract

- Define a base `DomainException` (or reuse existing) with:
  - `string UserMessageCode` — a key for i18n lookup on the frontend (e.g. `"subscription.seats.exceeded"`, `"document.locked"`).
  - `IReadOnlyDictionary<string, string> MessageParameters` — replacement tokens for the localized template (e.g. `{ "maxSeats": "10" }`, `{ "lockedBy": "Alice" }`).
- Throw `DomainException` for business-rule violations that carry a user-facing message code. Do not use it for technical failures (DB, network, config).

### HTTP transport

- Map `DomainException` to **422 Unprocessable Content** via a custom middleware or `IExceptionHandler`.
- Response body: standard ProblemDetails JSON extended with:
  ```json
  {
    "type": "https://httpwg.org/specs/rfc9110.html#status.422",
    "title": "Unprocessable Content",
    "status": 422,
    "userMessageCode": "subscription.seats.exceeded",
    "messageParameters": { "maxSeats": "10", "current": "15" }
  }
  ```
- Do not leak stack traces or internal details in the 422 response.
- Keep 5xx for unexpected errors; 400 for input validation (FluentValidation / DataAnnotations).

### Middleware placement

- Register the `DomainException` handler in `{EntryPoint}/Extensions/` alongside other exception mappings.
- Use `app.UseExceptionHandler(...)` or `AddExceptionHandler<DomainExceptionHandler>()` (minimal API friendly).
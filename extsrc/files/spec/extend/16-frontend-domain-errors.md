# Frontend — handling domain errors with localized messages

### HTTP error shape

- Backend returns **422 Unprocessable Content** for business-rule violations with this body:
  ```typescript
  interface DomainErrorBody {
    userMessageCode: string;
    messageParameters: Record<string, string>;
  }
  ```

### API layer parsing

- In `shared/api/errors.ts`, extend `toApiError()` to detect 422 responses:
  ```typescript
  interface ApiDomainError {
    type: "domain";
    userMessageCode: string;
    messageParameters: Record<string, string>;
  }
  ```
- A 422 without `userMessageCode` falls back to generic error handling.

### i18n integration

- Add translation keys matching backend `userMessageCode` values in `public/locales/{lang}/` JSON files.
- Translation values may contain replacement placeholders, e.g.:
  ```json
  {
    "subscription": {
      "seats": {
        "exceeded": "Превышен лимит мест: максимум {{maxSeats}}, текущее {{current}}"
      }
    }
  }
  ```
- Use `t(userMessageCode, messageParameters)` via `useTranslation()` to render the localized message.

### Client usage

- Mutations catch `ApiDomainError` and display the localized message (toast, inline error, or form field error).
- Form submissions: map `messageParameters` to form field errors when the code maps to a specific field.
- Non-form domain errors: show a toast with the localized message.
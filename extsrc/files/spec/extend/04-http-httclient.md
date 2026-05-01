# HTTP — `IHttpClientFactory` and `HttpClient`

- Never store `HttpClient` in a field; never mutate `BaseAddress` or `DefaultRequestHeaders` on the instance.
- Resolve from `IHttpClientFactory.CreateClient(name)` per-request; pass full URL and auth via `HttpRequestMessage`:

```csharp
var client = _httpClientFactory.CreateClient(/* optional name */);
using var request = new HttpRequestMessage(HttpMethod.Post, $"{config.BaseUrl.TrimEnd('/')}/endpoint");
request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", config.ApiKey);
request.Content = JsonContent.Create(body);
var response = await client.SendAsync(request, ct);
```

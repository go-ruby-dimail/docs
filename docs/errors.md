# Errors

Any non-2xx response raises an error. The Ruby error tree the rbgo binding
exposes mirrors the Go `APIError` from [go-dimail](https://github.com/go-dimail/dimail):

```ruby
Dimail::Error      < StandardError   # base
Dimail::APIError   < Dimail::Error   # any non-2xx response
```

`Dimail::APIError` carries:

| Member | Meaning |
| --- | --- |
| `#status` | the HTTP status code (Integer) |
| `#detail` | the parsed FastAPI `detail` (String / Array / Hash, or nil) |
| `#body` | the raw response body (String) |
| `#not_found?` | status is 404 |
| `#unauthorized?` | status is 401 |
| `#forbidden?` | status is 403 |
| `#conflict?` | status is 409 |

```ruby
begin
  client.post_domain({ "name" => "new.gouv.fr", "features" => ["mailbox"] })
rescue Dimail::APIError => e
  raise unless e.conflict?
  warn "domain already exists"
end
```

## In Go

`Session#Call` returns the underlying `*godimail.APIError` unchanged, so the same
information is available with `errors.As`:

```go
_, err := s.Call(ctx, "get_domain", "absent.example")
var apiErr *godimail.APIError
if errors.As(err, &apiErr) && apiErr.NotFound() {
	// handle 404
}
```

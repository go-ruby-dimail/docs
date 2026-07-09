# Usage

## Ruby (under rbgo)

Once rbgo ships the `require "dimail"` binding, the gem is used like this:

```ruby
require "dimail"

# Construction. Keyword options map to the Go client options:
#   base_url:   -> WithBaseURL      token:      -> WithToken
#   basic_auth: -> WithBasicAuth    user_agent: -> WithUserAgent  ([user, pass])
client = Dimail::Client.new(basic_auth: ["user", "pass"])

client.login                              # -> Hash (the token); stores the bearer

# Every API operation is a snake_case method named after its OpenAPI operationId.
# Dispatch is dynamic (method_missing -> Session#Call):
#   * path parameters  -> plain positional arguments
#   * query parameters -> trailing positional arguments (omit to leave unset)
#   * request body     -> a Hash
client.get_domain("example.gouv.fr")                       # -> Hash
client.get_domains                                         # -> Array<Hash>
client.post_mailbox_v2("d.fr", "user", { "features" => ["ox"] })
```

## Go

The same surface is available directly, without Ruby:

```go
package main

import (
	"context"
	"fmt"

	"github.com/go-ruby-dimail/dimail"
)

func main() {
	ctx := context.Background()
	s := dimail.NewSession(dimail.WithBasicAuth("user", "pass"))
	if _, err := s.Login(ctx); err != nil {
		panic(err)
	}

	dom, err := s.Call(ctx, "get_domain", "example.gouv.fr") // any (map[string]any)
	if err != nil {
		panic(err)
	}
	fmt.Println(dom.(map[string]any)["state"])
}
```

`Session.Client()` returns the underlying fully-typed `*godimail.Client`, and
`Session.Methods()` lists every snake_case operation `Call` accepts.

## Argument coercion

| Parameter kind | Pass as |
| --- | --- |
| Path parameter | a plain value (stringified if needed) |
| Query parameter | a value, or omit / `nil` to leave it unset |
| Request body | a Hash (JSON-round-tripped into the typed request struct) |

Results are JSON-normalised: a single object becomes a Hash, a collection an
Array of Hashes, and scalars stay scalars.

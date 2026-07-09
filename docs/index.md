# go-ruby-dimail

The pure-Go, Ruby-runtime-independent core of the Ruby **`dimail`** gem — a client
for the **Dimail API** of the French government's *La Suite numérique* platform,
shaped so that [go-embedded-ruby](https://github.com/go-embedded-ruby/ruby) (rbgo)
can bind it as `require "dimail"`.

It is a thin, reflective adapter over the typed client in
[go-dimail](https://github.com/go-dimail/dimail). A `Session` exposes **every one
of the client's 91 operations** through a single dynamic entry point, `Call`,
which:

1. maps a Ruby-style snake_case operation name (the API's `operationId`, e.g.
   `get_domain`, `post_mailbox_v2`) to the corresponding Go method;
2. coerces the arguments — Ruby Hashes become request structs, plain values become
   path and query parameters, omitted trailing arguments default to nil;
3. normalises the result into Ruby-shaped data: a **Hash** (`map[string]any`), an
   **Array** (`[]any`), or a scalar.

Nothing here depends on the Ruby runtime, so it is equally usable as a standalone
Go library — a sibling of `go-ruby-regexp` and the rest of the go-ruby-* family.

## Install

```sh
go get github.com/go-ruby-dimail/dimail
```

## At a glance

- **CGO-free** — builds and tests identically on `amd64`, `arm64`, `riscv64`,
  `loong64`, `ppc64le` and `s390x`.
- **100 % test coverage**, race-clean, enforced in CI.
- Backed by [go-dimail](https://github.com/go-dimail/dimail) (the typed,
  OpenAPI-generated client).

See [Usage](usage.md) for the API, [Examples](examples.md) for runnable Ruby, and
[Errors](errors.md) for the error surface.

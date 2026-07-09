# Contributing

go-ruby-dimail is BSD-3-Clause. The code lives at
[github.com/go-ruby-dimail/dimail](https://github.com/go-ruby-dimail/dimail).

## Ground rules

- **CGO-free.** No cgo, ever — the package must build and test on all six
  supported 64-bit targets (`amd64`, `arm64`, `riscv64`, `loong64`, `ppc64le`,
  `s390x`).
- **100 % coverage.** The CI gate enforces 100 % statement coverage, including
  error branches.
- **Backed by go-dimail.** The typed transport, models and errors live in
  [go-dimail](https://github.com/go-dimail/dimail); this repo is only the
  Ruby-idiomatic adapter. Changes to the API surface belong upstream (regenerate
  go-dimail from `openapi.json` with `go generate ./...`).

## Build & test

```sh
go build ./...
go vet ./...
go test -race -coverprofile=cover.out ./...
go tool cover -func=cover.out | tail -1   # must read 100.0%
```

## Examples

The Ruby examples under `examples/` are verified: their operation names are
checked against the real generated API by `TestRubyExamplesReferenceRealOperations`,
and (for gems rbgo binds) executed under the rbgo interpreter. Keep them runnable.

# Examples

The repository ships runnable Ruby examples under
[`examples/`](https://github.com/go-ruby-dimail/dimail/tree/main/examples). Each
runs under rbgo once the `require "dimail"` binding is installed; the operation
names they use are checked against the real generated API by a Go test, so they
cannot drift.

## Basic usage

```ruby
require "dimail"

client = Dimail::Client.new(basic_auth: ["apiuser", "apipass"])
client.login

# A single resource comes back as a Hash.
domain = client.get_domain("example.gouv.fr")
puts "#{domain["name"]}: #{domain["state"]}"

# A collection comes back as an Array of Hashes.
client.get_domains.each { |d| puts d["name"] }

# Self-service views for the authenticated user.
client.get_my_domains.each { |d| puts d["name"] }
```

## Mailboxes and forwards

```ruby
created = client.post_mailbox_v2("example.gouv.fr", "jean.dupont",
                                 { "features" => ["ox"] })
puts "created #{created["email"]}"

client.get_mailboxes_v2("example.gouv.fr").each { |mb| puts mb["email"] }

client.post_forward("example.gouv.fr", "contact",
                    { "nexthop" => "team@other.example" })
```

## Error handling

```ruby
begin
  client.get_domain("absent.example")
rescue Dimail::APIError => e
  warn "API error #{e.status}: #{e.detail}"
  warn "no such domain" if e.not_found?
end
```

See [Errors](errors.md) for the full error surface.

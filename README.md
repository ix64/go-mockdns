go-mockdns
============

[![Reference](https://godoc.org/github.com/foxcpp/go-mockdns?status.svg)](https://godoc.org/github.com/foxcpp/go-mockdns)

Boilerplate for testing of code involving DNS lookups, including ~~unholy~~
hacks to redirect `net.Lookup*` calls.

Example
---------

Trivial mock resolver, for cases where tested code supports custom resolvers:
```go
r := mockdns.Resolver{
    Zones: map[string]mockdns.Zone{
        "example.org.": {
            A: []string{"1.2.3.4"},
        },
    },
}

addrs, err := r.LookupHost(context.Background(), "example.org")
fmt.Println(addrs, err)

// Output:
// [1.2.3.4] <nil>
```

~~Unholy~~ hack for cases where it doesn't:
```go
srv, _ := mockdns.NewServer(map[string]mockdns.Zone{
    "example.org.": {
        A: []string{"1.2.3.4"},
    },
})
defer srv.Close()

srv.PatchNet(net.DefaultResolver)
defer mockdns.UnpatchNet(net.DefaultResolver)

addrs, err := net.LookupHost("example.org")
fmt.Println(addrs, err)

// Output:
// [1.2.3.4] <nil>
```

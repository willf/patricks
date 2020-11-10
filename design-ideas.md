# Design ideas

Regular expressions in Ruby are already a DSL. Can we create a DSL which:

1. Expresses everything (or most useful things) that Regular expressions do
2. Provide names for themselves
3. Allow a list of examples (and anti-examples) to be carried along with them.
4. Provide debugging info (to show where a regular expression fails).

Alternate: [Regularity](https://github.com/andrewberls/regularity)

```ruby
RX.new (
    name: "IPv3 address with optional slash",
    should_match: {
        "8.8.8.8" => "8.8.8.8",
        "127.0.0.1" => "127.0.0.1",
        "127.0.0.1/32" => "127.0.0.1/32",
        "8.8.8.8.8" => "8.8.8.8",
        },
    should_not_match: [
        "256.256.256.256",
        "8.8.8",
    ],
    subpatterns: {
        two_fifty: /25[0-5]/,
        two_hundred: /2[0-4]\d/.
        one_hundred: /1\d\d/,
        sub_hundred:  /0?\d\d/,
        octet: RX.alt(:two_fifty, :two_hundred, :one_hundred, :sub_hundred),
        address: RX.first(:octet)
                 .then('.')
                 .then(:octet)
                 .then('.')
                 .then(:octet),
        subnet: RX.first('/')
                .then(:octet)
                .maybe(
                     RX.first('.')
                    .then(:octet)
                    .maybe(
                        RX.first('.')
                        .then(:octet)))

    },
    ).first(:address)
     .maybe(:subnet)
  
```

\b(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.

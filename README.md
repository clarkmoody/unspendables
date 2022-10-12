# Provably Unspendable Bitcoin Outputs

Bitcoin Core nodes recognize four categories of unspendable coins:

- The Genesis Block outputs
- Transactions overridden by duplicates (no longer possible with [BIP30](https://github.com/bitcoin/bips/blob/master/bip-0030.mediawiki))
- Amounts sent to scripts that are unspendable (for example `OP_RETURN` outputs)
- Fee rewards that miners did not claim in their coinbase transaction

The [current check](https://github.com/bitcoin/bitcoin/blob/v23.0/src/script/script.h#L544-L547) within Core for whether a script is unspendable is limited to script size and `OP_RETURN` checks:

```C++
// script.h
bool IsUnspendable() const
{
    return (size() > 0 && *begin() == OP_RETURN) || (size() > MAX_SCRIPT_SIZE);
}
```

## The Registry

This project catalogs known unspendable outputs in order to gain a clearer understanding total Bitcoin supply lost. The registry of known scripts is a single JSON file, `unspendables.json`.

```json
{
    "scripts": [
        {
            "script_pubkey": "76a90088ac",
            "asm": "OP_DUP OP_HASH160 OP_0 OP_EQUALVERIFY OP_CHECKSIG",
            "explanation": "Script fails to push pubkey hash bytes and instead inserts OP_0",
            "certainty": "provable",
            "category": "non-standard"
        }
    ]
}
```

### Certainty

We know for certain that some script are provably unspendable. These are often non-standard scripts that contain a critical error. However, some scripts are only *probably* unspendable. That is to say, some scripts are highly unlikely to be spent. These include burner addresses, which usually have a human-readable form. In order to spend these scripts requires essentially guessing a private key.

As such, we assign a certainty to each script:

- `provable` - known with mathematical certainty to be unspendable
- `unlikely` - will probably never be spent

## Contributing

This is a Rust project, so make sure Rust is [installed](https://www.rust-lang.org/tools/install).

1. Add an entry to `unspendables.json`
2. (TODO) Run build script and checks
3. Open a pull request with changes

## Technical Roadmap

- [ ] Static script registry
- [ ] Automatic checks
- [ ] Generate CSV time histories
- [ ] Script pattern matching
- [ ] Taproot outputs with odd Y coordinate
- [ ] More advanced script evaluation with Miniscript?

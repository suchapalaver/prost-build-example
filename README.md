# prost-build-example

this contains the code from the `prost-build` [Example](https://docs.rs/prost-build/latest/prost_build/), the guide for which is copied and pasted below.

---
`prost-build` compiles `.proto` files into Rust.

`prost-build` is designed to be used for build-time code generation as part of a Cargo build-script.

## Example

Let's create a small crate, `snazzy`, that defines a collection of snazzy new items in a protobuf file.
```bash
$ cargo new snazzy && cd snazzy
```
First, add `prost-build`, `prost` and its public dependencies to `Cargo.toml`
(see [crates.io](https://crates.io/crates/prost) for the current versions):

```toml
[dependencies]
bytes = <bytes-version>
prost = <prost-version>

[build-dependencies]
prost-build = { version = <prost-version> }
```

Next, add `src/items.proto` to the project:

```proto
syntax = "proto3";

package snazzy.items;

// A snazzy new shirt!
message Shirt {
    enum Size {
        SMALL = 0;
        MEDIUM = 1;
        LARGE = 2;
    }

    string color = 1;
    Size size = 2;
}
```

To generate Rust code from `items.proto`, we use `prost-build` in the crate's
`build.rs` build-script:

```rust,no_run
use std::io::Result;
fn main() -> Result<()> {
    prost_build::compile_protos(&["src/items.proto"], &["src/"])?;
    Ok(())
}
```

And finally, in `lib.rs`, include the generated code:

```rust,ignore
// Include the `items` module, which is generated from items.proto.
pub mod items {
    include!(concat!(env!("OUT_DIR"), "/snazzy.items.rs"));
}

pub fn create_large_shirt(color: String) -> items::Shirt {
    let mut shirt = items::Shirt::default();
    shirt.color = color;
    shirt.set_size(items::shirt::Size::Large);
    shirt
}
```

That's it! Run `cargo doc` to see documentation for the generated code. The full
example project can be found on [GitHub](https://github.com/danburkert/snazzy).

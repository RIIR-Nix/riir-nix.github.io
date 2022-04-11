# RIIR-Nix

## Motivation for this organisation

[original issue](https://github.com/NixOS/nix/issues/5315)

> As it appears that there are now at least 5 tries to reimplement parts of Nix in Rust,
> it might be appropriate to isolate common functionality or patterns into crates that
> can be shared between all of them (as it might be likely that we run into edge cases
> which could be modeled with a big variety). It is also easy to see that the initial
> development targets of the associated projects vary to a somewhat greater degree.

(zseri)

## Goals

- define a set of protocols and Rust crates which implement them to ensure
  interoperability between both existing Nix tooling, and between different partial
  reimplementations of Nix in Rust.
- create low-level abstraction Rust crates which implement common functionality in a
  strongly modularised way, to avoid recreating a somewhat monolithic design again,
  as Nix (especially the language frontend) currently partially suffers from a
  too-high coupling between its internal components, which make e.g. swapping out the
  frontend language for [Nickel](https://github.com/tweag/nickel) or such difficult
  or almost impossible.

## Non-goals

- reimplementing the entirety of Nix within a single reimplementation in Rust.
  Currently, multiple slightly-different designs are pursued, which partially give
  up compatibility with Nix to some degree, and I don't want to curb creativity and
  potential benefits which might come from this,
  e.g. better support for content-addressed derivations+realisations, or different
  implementations of common interfaces to test their performance characteristics.

## Existing projects

- [PR nix-rust - various optimizations](https://github.com/NixOS/nix/pull/4697)
  &ndash; improvements to the old `nix-rust` code base
- [griff/Nix.rs](https://github.com/griff/Nix.rs), some parts of nix reimplemented in rust
- [YZITE/yzix](https://github.com/YZITE/yzix), based on mostly the same concepts as nix
  (but it's not really a reimplementation), but uses a different store layout,
  different interface, and tries to also integrate ninja, ccache and distcc,
  basically a much more researchy project, and less useful;
  but it might be possible to extract common abstractions out into some shared rust crates.
- [Tvix: We are rewriting Nix](https://www.reddit.com/r/NixOS/comments/r6ykln/tvix_we_are_rewriting_nix/hmxk04p/)
  TVL / tvix tries to reimplement the Nix evaluator in Rust.

## Related projects

- [YZITE/nix2js](https://github.com/YZITE/nix2js), half-working Nix-to-Javascript transpiler.
  Used as an experiment to find out which parts of the Nix language are difficult to implement.
  Known findings:
  - [mutual `with`-`import`s are difficult to implement](https://github.com/YZITE/nix2js/issues/2),
    [related RFC](https://github.com/NixOS/rfcs/pull/120)
  - It is difficult to find an equivalent to `builtins.match`, because each regex engine appears
    to use a different syntax.
    [`builtins.match` implementation in Nix ifself](https://github.com/NixOS/nix/blob/646af7325d93f98802b989f8a8e008a25f7a4788/src/libexpr/primops.cc#L3449),
    and it uses [`std::regex::extended`](https://github.com/NixOS/nix/blob/c74eac9fdeb172ea155b2d7ee9fe68d6487adc39/src/libexpr/primops.cc#L3440)
    as the format.

## Available components

- Lexer+Parser: [`rnix`](https://docs.rs/rnix), based upon `rowan`,
  but deviates a bit from the original Nix parser, and might thus parse some Nix code incorrectly.
- Lexer+Parser: [`NixEL`](https://github.com/kamadorueda/nixel), based upon `santiago`,
  tries to adhere much more closely to the original Nix syntax.
- [`store-ref-scanner`](https://docs.rs/store-ref-scanner),
  for scanning of byte arrays for references to a Nix-like store. Similar in goal to
  [`libstore/references.cc`](https://github.com/NixOS/nix/blob/646af7325d93f98802b989f8a8e008a25f7a4788/src/libstore/references.cc),
  but not a reimplementation.

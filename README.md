# RIIR-Nix

## Motivation for this organisation

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

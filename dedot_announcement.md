
# Introducing dedot: A delightful JavaScript client for Polkadot & Substrate-based blockchains

Introducing `Dedot`, the next generation of JavaScript client for Polkadot and Substrate blockchains. Designed to enhance dapps development experience to the next level, Dedot is rebuilt from the ground up to be lightweight and tree-shakable. Benefit from precise type and API suggestions for individual Substrate blockchains and ink! contracts, all while efficiently connecting to multiple chains to support a seamless multi-chain future.

## Overview

Dapps have always been a very important part of any blockchain ecosystem, it is where users can connect and interact with blockchain nodes. Given the complex nature of interacting with Substrate-based blockchains, in order for developers to focus on the business logic, a middle layer between dapps and blockchain nodes to facilitate the connections & interactions is always an essential part of the ecosystem.

`@polkadot/api` (`pjs`) has been around for a while and been integrated in most of the dapps in the ecosystem up until now. It has been doing a great job in helping applications connect to networks in an easy and effortless way by abstracting away all the complexities of connecting with a Substrate-based blockchain and scale-codec serialization process under the hood. 

Through our development experience, benchmarking, and profiling, we discovered that `@polkadot/api` has several limitations that may hinder developers from creating optimal dapps for the Polkadot ecosystem.

## Polkadot.js API's (`@polkadot/api` or `pjs`) limitations
###  Large bundle-size (`wasm` & `bn.js` & unused types def)
I believe any developers using `@polkadot/api` in build their dapps will recognize this issue first hand. Since `@polkadot/api` has very tight dependencies on `wasm` bob (crypto utilities) and `bn.js` for handling `BigInt` number. It also comes by default with a large amount if [type defs](https://github.com/polkadot-js/api/tree/master/packages/types/src/interfaces) even if dapps don't use most of those APIs/information. This make the whole bundle size of dapps growing pretty large thus creating a pretty bad UX as users have to wait longer before they can start interacting with dapps.

This is the bundle size of a really simple dapp built using `@polkadot/api` (no other dependencies) with 2 relatively trivial steps (1. initialize the api instance, 2. fetching account balance). As you can see the image below, the pre-compression size is close to 1MB, and even after gzip the size is still pretty big for a dead simple dapp.

<img width="660" alt="Pasted image 20240628111053" src="https://github.com/sinzii/w3-grant-draft/assets/6867026/fdf499d8-2fad-4396-98d5-48b55e937a85">

### High memory consumption
This is the issue that wallet providers or any dapps that need to connect to a large amount of networks at the same time might get into. For example, connecting to 100 RPC endpoints at the same time can potentially consume around 800MB of memory. Through benchmarking and profiling, we've pinpointed the root cause of this issue, long story short: its type system. We have a very detailed benchmarking and analysis in the [proposal](https://grants.web3.foundation/applications/delightfuldot) to Web 3 Foundation Grants Program to build `dedot` (formerly named DelightfulDOT) almost a year ago.

As we're heading toward a multi-chain future, we believe the ability to connecting to multiple blockchain networks at the same time efficiently and effectively is very important.

### Limitations in Types & APIs suggestions for individual chains
`@polkadot/api` only comes with a default types & api suggestions for only Polkadot & Kusama. This creates a very bad DX especially for new developers working on parachains or solochains with different business logic coming from different pallets & runtime apis.

I remembered my first time working with `@polkadot/api` to interact with my custom substrate blockchain. I was having a really hard time to define custom runtime-api typedefs and figure out which api to call in order to interact with my pallet (extrinsics & storage). I was also struggled on how to construct a struct, a tuple or especially an enum to pass those as parameters into the api. Give me a thump up if you are also in my situation the first time working with `@polkadot/api`. :) 

## Dedot comes to address those issues
### Small bundle-size and tree-shakable
Dedot was rebuilt from scratch to avoid the pitfall that `@polkadot/api` were into like type defs system (thanks god! we have metadata v14 & v15), no more dependencies with wasm bob and we're using native BigInt primitive instead of relying in `bn.js`.

As a result, the same dead simple dapp that has the size of nearly ~1MB built with `@polkadot/api` earlier, now the size is down to 127kB (gzip: 39kB) with dedot. It's ~7-8x smaller. Don't trust my number, [verify](https://github.com/dedotdev/dedot) it yourself!

<img width="686" alt="Pasted image 20240628111029" src="https://github.com/sinzii/w3-grant-draft/assets/6867026/6af835f4-c285-40dc-af9f-bf1906f41b35">

### Less memory consumption
`dedot`'s type system are relying completely on native TypeScript/JavaScript types, and with the help of [`subshape`](https://github.com/tjjfvi/subshape) for scale-codec encoding/decoding. This makes `dedot` to use memory more efficient in parsing and handling big raw metadata blob.

We're also seeing significant improvement in memory consumption when connecting `dedot` to multiple networks at the same time. Detailed benchmarking & comparison between `dedot` and `@polkadot/api` can be found [here](https://github.com/sinzii/delightfuldot-poc/tree/main?tab=readme-ov-file#memory-consumption-benchmark-result). You can also run the benchmarking script yourself to verify the result. TL.DR: ~4-5x less memory consumption compared to `@polkadot/api`.

In an attempt to verify how much impact `dedot` could make in term of memory consumption in a real-world application. I was trying to integrate `dedot` into SubWallet, a leading wallet in Polkadot ecosystem. When turning on connections to all of the Substrate-based networks SubWallet supported (+100 networks), SubWallet running `dedot` was consuming less than a half the total memory consumption when it's running with `@polkadot/api`. While the result's much less compared to the raw benchmarking (since there're a lot of other things can could impact memory consumption in a real application), this shows that we can build lightweight wallets or applications that connect to hundred of network connections at the same time efficiently.

<img width="1157" alt="Pasted image 20240628170009" src="https://github.com/sinzii/w3-grant-draft/assets/6867026/8846589a-dcf7-408f-ae18-b012b8a93f23">
	
### Types & APIs suggestion/auto-complete for individual Substrate-based chains.
With the latest changes in metadata v14 and v15. We can now have access to most of the available types & APIs that's exposed by the runtime. We were able to convert/generate those Types & APIs information encoded inside the metadata into plain TypeScript Types & APIs. So dapp developers can now being aware of all available Types & APIs for any particular Substrate-based blockchain that they're working on. E.g for Polkadot runtime: [types](https://github.com/dedotdev/chaintypes/blob/main/packages/chaintypes/src/polkadot/types.d.ts), [tx](https://github.com/dedotdev/chaintypes/blob/main/packages/chaintypes/src/polkadot/tx.d.ts), [runtime-apis](https://github.com/dedotdev/chaintypes/blob/main/packages/chaintypes/src/polkadot/runtime.d.ts), [storage queries](https://github.com/dedotdev/chaintypes/blob/main/packages/chaintypes/src/polkadot/query.d.ts), [constants](https://github.com/dedotdev/chaintypes/blob/main/packages/chaintypes/src/polkadot/consts.d.ts), ...

We're maintaining a package named [`@dedot/chaintypes`](https://github.com/dedotdev/chaintypes/tree/main/packages/chaintypes/src) with a goal to maintaining Types & APIs for all of Substrate-based blockchains in the ecosystem. So dapp developers can just install the package and pick which ever the ChainApi that they want to interact with.

Below is an example of how to interacting with different chain apis with `ChainApi`:

![chaintypes](https://github.com/sinzii/w3-grant-draft/assets/6867026/1237dc22-58d1-4dce-b57e-b33d167de94d)

Currently, there is a scheduled job running twice everyday to check if there's any runtime upgrades in the supported networks and regenerate the Types & APIs for those networks. So developers just need to upgrade this package everytime there is a runtime upgrade to be exposed to latest runtime changes. E.g: recently there is a runtime change in Kusama to remove the `Identity` & `IdentityMigrator` pallets, [this change](https://github.com/dedotdev/chaintypes/commit/c5692e15f441962fae4558278967bed7304d2033) is then get updated for Kusana network swiftly.

## Dedot also comes with more & more features
### Native TypeScript type system for scale-codec
Instead of using a wrapped codec for `u16`, `u64` in `@polkadot/api`, dedot directly use TypeScript type system to represent these type as `number` or `bigint`.

This make it easier for new dapp developers get started, and downstream libraries can easily inspect and utilize the types defined.

### Adopted latest changes in metadata V14 & V15
`dedot` creates types dynamically lazily on the fly with information from metadata and the types will be used for scale-codec encoding & decoding data.

An important change in metadata V15 is `Runtime APIs` information. `dedot` do leverage these information to expose the APIs (eg: [Polkadot](https://github.com/dedotdev/chaintypes/blob/main/packages/chaintypes/src/polkadot/runtime.d.ts)) & make the call.

`dedot` also supports metadata V14 for chains that haven't upgraded to V15 yet OR you simply want to query historical state where only V14 is supported. `dedot` also comes with explicit [Runtime API specs](https://github.com/dedotdev/dedot/tree/main/packages/runtime-specs/src) so you can [call Runtime APIs](https://github.com/dedotdev/dedot/tree/main?tab=readme-ov-file#runtime-apis) with metadata V14 as well.

### Build on top of new JSON-RPC specs (and the legacy as well but deprecated soon)
The new JSON-RPC specs is the new standard and is encourage to use. `dedot` is also using these new apis by default.

For the chain that haven't upgraded the the new specs yet, dapps could still be able to connect with those chains via a `LegacyClient`. But this will be deprecated soon in favor of the new specs.

### Support light clients (`smoldot`)
Since `smoldot` has a very good supports for new JSON-RPC specs for better performance. Developers can connect to the network via `smoldot` light client using the [`SmoldotProvider`](https://github.com/dedotdev/dedot/blob/main/packages/providers/src/smoldot/SmoldotProvider.ts). 

### Typed Contract APIs
Another limitations of `@polkadot/api` is lack of types & apis suggestions when working with ink!  Smart Contracts. With `dedot`, we're going to change this forever. Similar to how we expose Types & APIs from the runtime metadata, we're using the same trick for ink! smart contracts as well to enable Types & APIs suggestions for any ink! contracts that you're working on.

Below is quick gif to show you how does it look when you works with a PSP22 smart contract. Or if you want to see a working example, here's the [code](https://github.com/dedotdev/dedot/blob/main/zombienet-tests/src/0001-check-contract-api.ts).

![typink](https://github.com/sinzii/w3-grant-draft/assets/6867026/23f0fe2c-49be-4f38-a432-3d473181ebc6)

### Fully typed low level JSON-RPC client
This is useful for advanced users who wants to interact with the node directly via JSON-RPC call without having to go through the whole bootstrapping of downloading metadata or following the the chain head.

![typed-json-rpc-client](https://github.com/sinzii/w3-grant-draft/assets/6867026/417f5c36-790c-4cd9-9c5d-6225a04a3696)

### A builtin mechanism to cache metadata
Downloading a big metadata blob can take a large amount of time, depending on the JSON-RPC server that dapps are connecting to. For example, downloading Polkadot metadata (~500 kB) can take up to 500ms or ~1s or even longer depends on the network conditions.

This is a nice to have feature where dapp only have to download metadata on the first load, later metadata can be fetched directly from cache without having to download again.

### Compact Metadata (on the road-map)
Most of dapp does not use all of the types and api from the metadata, so why not extract only the information/types that dapps needs to function properly. So the goal is to produce a small and compact metadata that can be easily bundled inside dapps, so dapps no longer need to download metadata again from the network directly, saving a reasonable amount of loading time. ([ref](https://github.com/dedotdev/dedot/issues/45))

### XCM utilities (on the road-map
Crafting an XCM are a bit complicated, due to the heavy usage of nested enums. Developers can still [making an XCM](https://gist.github.com/sinzii/078a48976827e3a85f5cebda0930d1f9) transaction with `dedot` by following along with the types suggestions. But the syntax is very cumbersome and inefficient. We plan to add some extra tool on top to help the process of crafting XCM message easier.

### React Native supports (on the road-map)
Having support for React Native can help broaden the types of applications that can build on Polkadot ecosystem.

### And a lot more toolings around dedot to come

## Migration from `@polkadot/api` to `dedot`

`dedot` is not designed to be drop-in replacement for `@polkadot/api`, but we make some intentional decisions to help migration process from `@polkadot/api` to `dedot` a lot easier and faster. You should already see some familiarities in api styling between `dedot` & `@polkadot/api`. More information about migration can be found [here](https://github.com/dedotdev/dedot?tab=readme-ov-file#migration-from-polkadotapi-to-dedot).

## What's next
Dedot is currently in alpha testing phase, so it's ready for your experiments and explorations. We're expecting some more breaking changes before stabilization. Here a few places that we want to continue to optimize and adding further improvements:
- Optimize JSON-RPC v2 integration
- Improve Contracts APIs
- Improve `@dedot/chaintypes` package (js docs, supports more chains, error handling when generating chaintypes)
- Improve `smoldot` integration (Add known chain specs, worker helper)
- Documentations & example dapps
- And a lot more on the road map to help building a fully-fledge client.

## Who we are?
We are small team that falls in love with Polkadot technology and believe in the vision of a decentralization future. 
- We've started contributing from 2022 and since then we have secured 2 W3F Grants to building open source for Polkadot:
  - The [1st grant](https://grants.web3.foundation/applications/coong_wallet) is to build [`Coong Wallet`](https://github.com/CoongCrafts/coong-wallet), a website-based wallet that's compatible with `@polkadot/extension` APIs and works seamlessly on both desktop and mobile. (Demo 1, Demo 2)
  - The [2nd grant](https://grants.web3.foundation/applications/delightfuldot) is to fund the initial phase of `dedot` (formerly named DelightfulDOT)
- We're also the first prize winner of Polkadot Hackathon Vietnam 2023 with [`InSpace`](https://inspace.ink), an on-chain community launcher via ink! smart contracts.
- Thang (@sinzii), our lead developer, is PBA 5 graduate in Singapore.
  
## We need your feedback and supports
- We can't build this alone without the community feedback and supports. We are deeply appreciated if you could give `dedot` a try and let us know how you like or not like it.
- Aside from the initial funding from W3F Grants Program, we've been self-funded `dedot` to working on some of the very important integrations like the new JSON-RPC specs or Typed Contracts APIs. We're now seeking for community feedback and asking for funding from the treasury to continue the development of `dedot` to bring the dapps development DX of Polkadot ecosystem to another level. We hope to have your all supports. Thank you!
- Please let us know if you have any feedback by respond to this thread or post a discussion, raise an issue in the [dedot](https://github.com/dedotdev/dedot) repository
- We'd love to connect everyone as well
  - Twitter / X: @realsinzii
  - Telegram: @realsinzii
  - Discord: @sinzii







# SubProfile

- **Team Name:** SubProfile
- **Payment Address:** TBP (e.g. 0x8920... (DAI))
- **[Level](https://github.com/w3f/Grants-Program/tree/master#level_slider-levels):** 2

## Project Overview :page_facing_up:

### Overview

Wallet is a key factor to blockchain technology & cryptocurrencies, it should be secure, easy to use and for mass adoption it should have a frictionless users onboarding experience.

Polkadot & Kusama ecosystem has seen a few wallet solutions out there with great UI/UX (SubWallet, Nova, Talisman, Enkrypt). On desktop, most of the solutions are browser extension-based wallet with which users need to install an extension in order to interact with dapps and networks. On mobile, most of the browsers do not support extensions, so users would need to install wallet mobile apps and then interact with dapp via Dapp Browser build inside the apps (SubWallet, Nova). We believe this creates an inconsistent experience for users on desktop & mobile since most of the dapps are website-based thus posing a barrier in onboarding new users to the ecosystem, especially for those who are new to or less-educated about crypto.

As users, we love the website-based wallet experience that the NEAR wallet bring to the NEAR ecosystem where users can connect to dapps using their favorite browsers and access their wallet smoothly inside the same browser on both desktop & mobile.

With that inspiration, we propose to build SubProfile, a website-based multi-chain wallet, to bring the similar experience to Polkadot & Kusama ecosystem with which we believe it will bring a huge benefits to both users & the ecosystem.

### Project Details

#### Design Goals
- Compatible with `@polkadot/extension` API
    - Most the wallet in the ecosystem are now following `@poladot/extension` API which is widely used now in the ecosystem. So being compatible `@polkadot/extension` API will help dapps can easily integrate with SubProfile within a few steps.
    - The `@polkadot/extension` API allows dapps to call into the wallet to access granted information (connected accounts) as well as asking for permission/approval (request to access accounts, sign transaction, …), dapps can also subscribe to changes happened inside the wallet. Those ability seems to be impossible with the redirection-based approach that the Near wallet is using.
    - The approach that SubProfile would take is similar to how dapps interact with extension-based wallets which is via `window.postMessage` API.
        - To access granted information or subscribe to changes from the wallet, dapps will send/receive messages via an iframe loading SubProfile wallet, the iframe will be injected inside dapps via SubProfile SDK
        - To ask for users’ permission/approval, dapps would open a child tab of SubProfile wallet using `window.open` API, the `window.open` will return a window object of the child tab allowing wallet & dapps to send messages back and forth via `window.postMessage`
        - We have created a PoC to demonstrate how dapps can interact with a website-based wallet to ask for accounts access & sign dummy data. [Live demo here](https://subprofile-dapp.netlify.app/)
- Security first
    - We believe a wallet not only should be easy to use but also can secure users’ information. SubProfile is a non-custodial wallet, users’ private keys & seed phrase will be encrypted and stored in `localStorage` of the browsers, and can only be decrypted by users’ wallet password.

#### Account Creation
SubProfile is a hierarchical deterministic (HD) wallet following the idea of [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki), which only requires users to back up only one seed phrase upon setting up the wallet, new accounts will be created by deriving from the setup seed and an account index number as the derivation path (`{seed_phrase}//{index}`), `index` number will be started from `0` and increased one by one as new accounts are created. The first account will be created without derivation path, this is to be compatible with the Polkadot{.js} wallet.

SubProfile also supports import accounts by private keys, but those accounts cannot be recovered by the setup seed phrase, so they will be labeled as `Imported Account`.

#### Integration Process into Dapps
- Developers need to install SubProfile SDK (`@subprofile/sdk`) into the dapps and run SubProfile wallet initialization upon loading dapps to [inject the SubProfile API](https://github.com/polkadot-js/extension#injection-information).
- After the SubProfile API is injected, dapps can interact with SubProfile just like they interact with other extension-based wallets. E.g: calling `window.injectedWeb3['subprofile'].enable()` to request access to the wallet & users’ accounts.
- `@subprofile/sdk` will be published on npm registry so developers can easily download & integrate to dapps.

#### Vision
We set a vision for SubProfile to be an important part of Polkadot/Kusama ecosystem with fully-fledge features like view/send balances, EVM accounts, NFTs, staking, crowdloan, transaction history… We will split the development of SubProfile into several phases. This application is asking for grant to support the first development phase.

The first development phase will be focus on the core features of the wallet and the SDK with `@polkadot/extension` compatibility to help dapps easily integrate with SubProfile.

#### Mockups
- Welcome screen
![image](https://user-images.githubusercontent.com/6867026/207111441-80000a50-61ec-41ba-a7c5-6861fe7b1475.png)
- Set up new wallet
![image](https://user-images.githubusercontent.com/6867026/207111541-9b4f8dac-45d9-4a0c-a531-1cfc1afaffe1.png)
- Unlock Wallet
![image](https://user-images.githubusercontent.com/6867026/207111594-c1e1870b-a1cd-4874-b878-fb3bd69bcce7.png)
- List accounts
![image](https://user-images.githubusercontent.com/6867026/207111645-a0140b3f-b719-4881-ae06-5c0f23d7b32d.png)
- Create account
- Account controls (Forget, Copy address, Show QR Code, Export, Rename, Dapps Access)
- Sign Transaction
- Sign Message
- Request Wallet Access
- Other additional features:
    - Export wallet
    - Import account
    - Manage Dapps Access
    - Settings

#### Technology Stack
- React/Redux/Material UI
- Polkadot{.js} Extension/Api

### Ecosystem Fit

SubProfile is born with the intention to help mitigate the inconsistent wallet experience on desktop & mobile and bring a seamless onboarding new users experience to the Polkadot & Kusama ecosystem.

As discussed above, there’re a few wallet solutions out there in the ecosystem that have great UX/UI but most are extension-based wallet, SubProfile take a different approach as to be a website-based wallet.

[Choko wallet](https://github.com/w3f/Grants-Program/blob/master/applications/choko_wallet.md) is also an other website-based wallet but there’re a few differences:

- Choko wallet took the redirection-based approach like the near wallet did while SubProfile uses the `window.postMessage` API for cross-tab & cross-origin communication like how dapps interact with extension-based wallets.
- SubProfile is compatible with `@polkadot/extension` API which is widely adopted in the ecosystem, so dapps can integrate with SubProfile just like how they integrate with extension-based wallets.

## Team :busts_in_silhouette:

### Team members

- Thang X. Vu - Leader / Lead Developer
- Tung Vu - Frontend Developer

### Contact

- **Contact Name:** Thang X. Vu
- **Contact Email:** zthangxv@gmail.com

### Legal Structure

None

### Team's experience

We have more than 7 years of experience in software development for startups & enterprises. Seeing the potential of blockchain technologies, we have spent more than 1 year exposing to blockchain and Polakdot & Kusama ecosystem. We closely worked with SubWallet team in helping to review the source code to [improve performance & stability of the wallet](https://github.com/Koniverse/SubWallet-Extension/issues/232). Thang is a participant of the first Polkadot DevCamp in May 2022. We as users also experience the UX problems in Polkadot & Kusama ecosystem. With that, we know where and how to solve these paint points to help bring the ecosystem closer to end-users.

### Team Code Repos

Projects repos

- https://github.com/subprofile/wallet
- https://github.com/subprofile/sdk

Team members

- Thang X. Vu - https://github.com/sinzii
- Tung Vu - https://github.com/1cedrus

## Development Status :open_book:

- We’ve been researched the `@polkadot/extension` source code to have a sense of how the wallet is setup & work, also to better understand the interaction between dapps & extension. SubProfile will be compatible with `@polkadot/extension` API, so knowing its source code to a certain extend would greatly help the development of SubProfile.
- TODO: Should we start from a fork of `@polkadot/extension`?

- We’ve also been working on a PoC to demonstrate the interaction between dapp & wallet.
    - [Live demo](https://subprofile-dapp.netlify.app/)
    - Source code: [demo-dapp](https://github.com/sinzii/demo-wallet-interaction/tree/main/dapp), [demo-wallet](https://github.com/sinzii/demo-wallet-interaction/tree/main/wallet)

## Development Roadmap :nut_and_bolt:

### Overview

- **Total Estimated Duration:** 5 months
- **Full-Time Equivalent (FTE):**  1.5 FTE
- **Total Costs:** 30,000 USD

> :exclamation: **The default deliverables 0a-0d below are mandatory for all milestones**, and deliverable 0e at least for the last one. If you do not intend to deliver one of these, please state a reason in its specification (e.g. Milestone X is research oriented and as such there is no code to test).


### Milestone 1 — Core features & SDK

- **Estimated duration:** 2.5 month
- **FTE:**  1,5
- **Costs:** 15,000 USD

| Number | Deliverable | Specification |
| -----: | ----------- | ------------- |
| **0a.** | License | Apache 2.0 |
| **0b.** | Documentation | We will provide both **inline documentation** of the code, a live demo of the wallet and instruction on how to integrate SubProfile Wallet into dapps using SubProfile SDK. |
| **0c.** | Testing and Testing Guide | Core functions will be fully covered by comprehensive unit tests to ensure functionality and robustness. In the guide, we will describe how to run these tests. |
| **0d.** | Docker | We will provide a Dockerfile that can be used to test all the functionality delivered with this milestone. |
| 0e. | Article | We will publish an article that explains SubProfile Wallet concept & what has been done in this milestone |
| 1. | Wallet App / Core features | We'll implement the following features for the wallet app:<br>- Welcome screen: Shows a small introduction about SubProfile & instructw users to set up the wallet by creating a new one or import from an existing seed phrase.<br>- Unlock wallet: Requires users to enter password to access the wallet<br>- Set up new wallet: Guides users through a screen flow to help setting up their wallet from pick up a wallet password, to back up secret recovery phrase (12 words).<br>- Create account: Creates a new account<br>- List accounts: Lists all of the accounts users have created<br>- Request wallet access: Allows users to approve dapps access to the wallet accounts<br>- Approve transaction: Allows users to sign/approve a transaction  |
| 2. | SubProfile SDK | We'll implement the SDK to helps [integrate SubProfile into Dapps](#integration-process-into-dapps) & publish the package to npm registry. |


### Milestone 2 — Additional features, demo

- **Estimated duration:** 2.5 month
- **FTE:**  1,5
- **Costs:** 15,000 USD

| Number | Deliverable | Specification |
| -----: | ----------- | ------------- |
| **0a.** | License | Apache 2.0 |
| **0b.** | Documentation | We will provide both **inline documentation** of the code and a live demo which will show how the new functionality works. |
| **0c.** | Testing and Testing Guide | Core functions will be fully covered by comprehensive unit tests to ensure functionality and robustness. In the guide, we will describe how to run these tests. |
| **0d.** | Docker | We will provide a Dockerfile that can be used to test all the functionality delivered with this milestone. |
| 0e. | Article | We will publish an article that explains SubProfile Wallet (what was done/achieved as part of the grant) |
| 1. | Wallet App / Additional features | We'll implement the following features for the wallet app:<br>- Sign message: Allow users to sign a raw message<br/>- Import existing wallet: Set up wallet by using an existing recovery phrase (seed phrase) or scan QR code from the export wallet feature<br>- Forget wallet password / Reset wallet: Allow users to reset the wallet if they forget the password.<br/>- Account Controls (Forget, Copy address, Show QR Code, Export, Rename, Dapps Access)<br/>- Export wallet: Allow users to easily transfer seed phrase & created accounts to other devices via QR code<br/>- Import account (From: QR Code, Private Key, JSON file)<br/>- Manage Dapps Access: Manage & update access to wallet accounts of dapps<br/>- Settings: Dark/light theme mode, Language, Auto-lock timer, Reveal recovery phrase, Change wallet password |
| 2. | Demo Dapp | We'll create a demo dapp that is integrated with SubProfile wallet to demonstrate dapp-wallet interactions, similar to [connect.subwallet.app](https://connect.subwallet.app/). |


## Future Plans

Please include here

- how you intend to use, enhance, promote and support your project in the short term, and
- the team's long-term plans and intentions in relation to it.

> TOTO: Update this

## Additional Information :heavy_plus_sign:

**How did you hear about the Grants Program?** 
Web3 Foundation Website & Medium
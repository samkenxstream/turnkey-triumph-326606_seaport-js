# Seaport.js

[Seaport](https://github.com/ProjectOpenSea/seaport) is a new marketplace protocol for safely and efficiently buying and selling NFTs. This is a JavaScript library intended to make interfacing with the contract reasonable and easy.

- [Synopsis](#synopsis)
- [Installation](#installation)
- [Getting Started](#getting-started)
  - [Use Cases](#use-cases)
- [Contributing](#contributing)

## Synopsis

This is a JavaScript library to help interface with Seaport. It includes various helper methods and constants that makes interfacing with Seaport easier. These include creating orders, fulfilling orders, doing the necessary balance and approval checks, and more. We recommend taking a look at the [Seaport](https://github.com/ProjectOpenSea/seaport) docs to get a better understanding of how the Seaport marketplace works.

## Installation

We recommend using [nvm](https://github.com/nvm-sh/nvm) to manage Node.js versions. Execute `nvm use`, if you have `nvm` installed.

Then, in your project, run:

```
npm install --save @opensea/seaport-js
```

## Getting Started

Instantiate your instance of seaport using your ethers provider:

```JavaScript
import { Seaport } from "@opensea/seaport-js";

const provider = ethers.getDefaultProvider();

const seaport = new Seaport(provider);
```

Look at the relevant definitions in `seaport.ts` in order to see the different functionality this library offers.

### Use Cases

Many of the main core flows return _use cases_. What this means is that if you were to create an order (a la `createOrder`), the library helps perform the necessary balance and approval checks based on the `offer` of the order being created. If the `offerer` requires approvals on one asset contract, the `actions` field of the use case would contain an approval action that the user should execute first in order for the trade to succeed in the future.

### Examples

#### Listing an ERC-721 for 10 ETH and fulfilling it

```
const offerer = "0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266";
const fulfiller = "0x70997970c51812dc3a010c7d01b50e0d17dc79c8";
const { executeAllActions } = await seaport.createOrder({
  offer: [
    {
      itemType: ItemType.ERC721,
      token: "0x8a90cab2b38dba80c64b7734e58ee1db38b8992e",
      identifier: "1",
    }
  ],
  consideration: [
    {
      amount: parseEther("10").toString(),
      recipient: offerer,
    }
  ],
  accountAddress: offerer
});

const order = await executeAllActions();

const { executeAllActions: executeAllFulfillActions } = await seaport.fulfillOrder({
  order,
  accountAddress: fulfiller,
});

const transaction = executeAllFulfillActions()
```

#### Making an offer for an ERC-721 for 10 WETH and fulfilling it

```
const offerer = "0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266";
const fulfiller = "0x70997970c51812dc3a010c7d01b50e0d17dc79c8";
const { executeAllActions } = await seaport.createOrder({
  offer: [
    {
      amount: parseEther("10").toString(),
      // WETH
      token: "0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2"
    },
  ],
  consideration: [
    {
      itemType: ItemType.ERC721,
      token: "0x8a90cab2b38dba80c64b7734e58ee1db38b8992e",
      identifier: "1",
      recipient: offerer
    },
  ],
  accountAddress: offerer
});

const order = await executeAllActions();

const { executeAllActions: executeAllFulfillActions } = await seaport.fulfillOrder({
  order,
  accountAddress: fulfiller.address,
});

const transaction = executeAllFulfillActions()
```

## Contributing

See [the contributing guide](CONTRIBUTING.md) for detailed instructions on how to get started with this project.

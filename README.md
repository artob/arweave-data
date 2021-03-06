# Arweave-Data (ANS-102)

This library contains routines to create, read, and verify Arweave bundled data.

See [ANS-102](https://github.com/ArweaveTeam/arweave-standards/blob/master/ans/ANS-102.md) for more details.

## Initializing the library

This is a self-contained library, so we need to initialize the API with a couple of dependencies:

```javascript
import Arweave from 'arweave/node'
import deepHash from 'arweave/node/lib/deepHash'
import ArweaveData from 'arweave-data'

const deps = {
  utils: Arweave.utils,
  crypto: Arweave.crypto,
  deepHash: deepHash,
}

const ArData = ArweaveData(deps);
```

## Unbundling from a Transaction containing DataItems

```javascript

const txData = myTx.get('data', { decode: true, string: true });

// Will extract and verify items, returning an array of valid 
// DataItems.
const items = await ArweaveData.unbundleData(txData);

```

## Reading data and tags from a DataItem

```javascript

const item = items[1]; // get a single item out of the array returned in the previous step.

// get data as Uint8Array.
const data = await ArData.decodeData(item, { string: false });
// get data as utf8 string.
const data = await ArData.decodeData(item, { string: true });

// get id, owner, target, signature, nonce
const id = item.id
const owner = item.owner
const target = item.taget
const signature = item.signature
const nonce = item.none


for (let i = 0; i < item.tags.length; i++) {
  const tag = await ArData.decodeTag(item.tag)
  // tag.name
  // tag.value
}


```

## Creating a DataItem

```javascript

const myTags = [
  { name: 'App-Name', value: 'myApp' },
  { name: 'App-Version', value: '1.0.0' }
]

let item = await ArData.createData({ to: 'awalleet', data: 'somemessage', tags: myTags }, wallet);

// Add some more tags after creation.
ArData.addTag(item, 'MyTag', 'value1');
ArData.addTag(item, 'MyTag', 'value2');

// Sign the data, ready to be added to a bundle
data = await ArData.sign(item, wallet);

```

## Bundling up DataItems and writing a transaction

```javascript

const items = await makeManyDataItems();

// Will ensure all items are valid and have been signed,
// throwing if any are not
const myBundle = await ArData.bundleData(items);

const myTx = await arweave.createTransaction({ data: myBundle }, wallet);

myTx.addTag('Bundle-Format', 'json');
myTx.addTag('Bundle-Version', '1.0.0');
myTx.addTag('Content-Type', 'application/json');

await arweave.transactions.sign(tx, wallet);
await arweave.transactions.post(tx);

```


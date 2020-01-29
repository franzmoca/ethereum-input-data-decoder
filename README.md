<h3 align="center">
  <br />
  <img src="https://user-images.githubusercontent.com/168240/39537094-61c30484-4ded-11e8-8c93-410ba0510cf4.png" alt="logo" width="750" />
  <br />
  <br />
  <br />
</h3>

# ethereum-input-data-decoder

> Ethereum smart contract transaction input data decoder
This fork removes the ethereumjs-abi, relying uniquely on ethers for ABI decoding.
In this way this platform doesn't relying on NodeJS exclusive modules and can be more easily used in environments like react-native.
There are some inconsistencies in comparison to the original version, tests have been updated to reflect them.
Unnecessary components like CLI were removed, refer to the original repository by miguelmota for them.

## Install

```bash
npm install "github:tsuru-/ethereum-input-data-decoder"
```

## Getting started

fs module dependency was removed, you can now only pass ABI array object to constructor:

```javascript
const abi = [{ ... }]
const decoder = new InputDataDecoder(abi);
```

[example abi](./test/abi.json)

Then you can decode input data:

```javascript
const data = `0x67043cae0000000000000000000000005a9dac9315fdd1c3d13ef8af7fdfeb522db08f020000000000000000000000000000000000000000000000000000000058a20230000000000000000000000000000000000000000000000000000000000040293400000000000000000000000000000000000000000000000000000000000000a0f3df64775a2dfb6bc9e09dced96d0816ff5055bf95da13ce5b6c3f53b97071c800000000000000000000000000000000000000000000000000000000000000034254430000000000000000000000000000000000000000000000000000000000`;

const result = decoder.decodeData(data);

console.log(result);
```

```text
{
  "method": "registerOffChainDonation",
  "types": [
    "address",
    "uint256",
    "uint256",
    "string",
    "bytes32"
    ],
    "inputs": [
      <BN: 5a9dac9315fdd1c3d13ef8af7fdfeb522db08f02>,
      <BN: 58a20230>,
      <BN: 402934>,
      "BTC",
      <Buffer f3 df ... 71 c8>
    ],
    "names": [
      "addr",
      "timestamp",
      "chfCents",
      "currency",
      "memo"
    ]
}
```

Example using input response from [web3.getTransaction](https://github.com/ethereum/wiki/wiki/JavaScript-API#web3ethgettransaction):

```javascript
web3.eth.getTransaction(txHash, (error, txResult) => {
  const result = decoder.decodeData(txResult.input);
  console.log(result);
});
```

### Decoding tuple and tuple[] types

Where `OrderData` is

```solidity
struct OrderData {
  uint256 amount;
  address buyer;
}
```

Decoding input to a method `someMethod(address,OrderData,OrderData[])` returns data in format

```js
{
  method: 'someMethod',
  types: ['address', '(uint256,address)', '(uint256,address)[]'],
  inputs: [
    '0x81c55017F7Ce6E72451cEd49FF7bAB1e3DF64d0C',
    [100, '0xA37dE6790861B5541b0dAa7d0C0e651F44c6f4D9']
    [[274, '0xea674fdde714fd979de3edf0f56aa9716b898ec8']]
  ],
  names: ['sender', ['order', ['amount', 'buyer']], ['allOrders', ['amount', 'buyer']]]
}
```

- In the `types` field, tuples are represented as a string containing types contained in the tuple
- In the `inputs` field, tuples are represented as an array containing values contained in the tuple
- In the `names` field, tuples are represented as an array with 2 items. Item 1 is the name of the tuple, item 2 is an array containing the names of the values contained in the tuple.


## Test

```bash
npm test
```

## License

[MIT](LICENSE)

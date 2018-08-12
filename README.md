bcored-rpc.js
===============

[![NPM Package](https://img.shields.io/npm/v/bitcoind-rpc.svg?style=flat-square)](https://www.npmjs.org/package/bitcoind-rpc)
[![Build Status](https://travis-ci.com/bitcoin-cored/bitcoind-rpc.svg?branch=master)](https://travis-ci.com/bitcoin-cored/bitcoind-rpc)
[![Coverage Status](https://img.shields.io/coveralls/bitpay/bitcoind-rpc.svg?style=flat-square)](https://coveralls.io/r/bitpay/bitcoind-rpc?branch=master)

A client library to connect to Bitcoin Core RPC in JavaScript.

## Get Started

bcored-rpc.js runs on [node](http://nodejs.org/), and can be installed via [npm](https://npmjs.org/):

```bash
npm install bcored-rpc
```

## Examples

```javascript
var run = function() {
  var bitcore = require('bcore');
  var RpcClient = require('bcored-rpc');

  var config = {
    protocol: 'http',
    user: 'user',
    pass: 'pass',
    host: '127.0.0.1',
    port: '18332',
  };

  var rpc = new RpcClient(config);

  var txids = [];

  function showNewTransactions() {
    rpc.getRawMemPool(function (err, ret) {
      if (err) {
        console.error(err);
        return setTimeout(showNewTransactions, 10000);
      }

      function batchCall() {
        ret.result.forEach(function (txid) {
          if (txids.indexOf(txid) === -1) {
            rpc.getRawTransaction(txid);
          }
        });
      }

      rpc.batch(batchCall, function(err, rawtxs) {
        if (err) {
          console.error(err);
          return setTimeout(showNewTransactions, 10000);
        }

        rawtxs.map(function (rawtx) {
          var tx = new bcore.Transaction(rawtx.result);
          console.log('\n\n\n' + tx.id + ':', tx.toObject());
        });

        txids = ret.result;
        setTimeout(showNewTransactions, 2500);
      });
    });
  }

  showNewTransactions();
};
```

## License

**Code released under [the MIT license](https://github.com/bitpay/bitcore/blob/master/LICENSE).**

Copyright 2013-2018 BitPay, Inc.

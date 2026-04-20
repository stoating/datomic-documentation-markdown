# Transactions

This section documents Datomic transactions. Start with the [transaction model](01-transaction-model/transaction-model.md) document, which explains the semantics of Datomic transactions. After that, you can move to specific detail topics as needed:

- [Transaction model](01-transaction-model/transaction-model.md): semantics of Datomic transactions
- [Transaction data](02-transaction-data/transaction-data.md): the transaction data format
- [Processing transactions](03-processing-transactions/processing-transactions.md): what happens when you call [d/transact](../../04-apis/01-peer-api-clojuredoc/peer-api-clojuredoc.md#transact)
- [Transaction functions](04-transaction-functions/transaction-functions.md): extending Datomic transactions with your code
- [ACID](05-acid/acid.md): how Datomic transactions deliver the ACID properties
- [Client synchronization](06-client-synchronization/client-synchronization.md): coordinating access to a particular point in time from multiple processes

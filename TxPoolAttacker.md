

Tx pool attacker point: 
https://github.com/ethereum/go-ethereum/blob/master/miner/worker.go#L1065

From txs := types.NewTransactionsByPriceAndNonce(env.signer, localTxs, env.header.BaseFee)  
To  txs := [Tx1, Txs, Tx2, Tx3, Tx4] //select the transactions that the adversary likes 

```go
// fillTransactions retrieves the pending transactions from the txpool and fills them
// into the given sealing block. The transaction selection and ordering strategy can
// be customized with the plugin in the future.
func (w *worker) fillTransactions(interrupt *int32, env *environment) {
	// Split the pending transactions into locals and remotes
	// Fill the block with all available pending transactions.
	pending := w.eth.TxPool().Pending(true)
	localTxs, remoteTxs := make(map[common.Address]types.Transactions), pending
	for _, account := range w.eth.TxPool().Locals() {
		if txs := remoteTxs[account]; len(txs) > 0 {
			delete(remoteTxs, account)
			localTxs[account] = txs
		}
	}
	if len(localTxs) > 0 {
		//txs := types.NewTransactionsByPriceAndNonce(env.signer, localTxs, env.header.BaseFee)
      txs := [Tx1, Txs, Tx2, Tx3, Tx4] // select the transactions that the adversary likes 
		if w.commitTransactions(env, txs, interrupt) {
			return
		}
	}
	if len(remoteTxs) > 0 {
		//txs := types.NewTransactionsByPriceAndNonce(env.signer, remoteTxs, env.header.BaseFee)
		if w.commitTransactions(env, txs, interrupt) {
			return
		}
	}
}
```

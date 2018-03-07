ローカルで関数を開発するときは、ターミナルまたはコマンド プロンプトから Azure Functions Core Tools を使って、必要な拡張機能をインストールできます。 次の `func extensions install` コマンドは、Azure Cosmos DB バインド拡張機能をインストールします。

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version 3.0.0-beta6 
```

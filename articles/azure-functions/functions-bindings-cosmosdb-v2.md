---
title: Functions 2.x 以降での Azure Cosmos DB バインド
description: Azure Functions で Azure Cosmos DB のトリガーとバインドを使用する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 09/01/2021
ms.author: cshoe
ms.openlocfilehash: 5d3513ea7c7e44afde70765bed668c93cf4096bb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582603"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-and-higher-overview"></a>Azure Functions 2.x 以降での Azure Cosmos DB のトリガーとバインドの概要

> [!div class="op_single_selector" title1="使用している Azure Functions ランタイムのバージョンを選択します。 "]
> * [Version 1](functions-bindings-cosmosdb.md)
> * [バージョン 2 以降](functions-bindings-cosmosdb-v2.md)

この一連の記事では、Azure Functions 2.x 以降で [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) のバインドを操作する方法について説明します。 Azure Functions は、Azure Cosmos DB のトリガー、入力、出力のバインドをサポートしています。

| アクション | Type |
|---------|---------|
| Azure Cosmos DB ドキュメントが作成または変更されたときに関数を実行する | [トリガー](./functions-bindings-cosmosdb-v2-trigger.md) |
| Azure Cosmos DB ドキュメントを読み込む | [入力バインド](./functions-bindings-cosmosdb-v2-input.md) |
| Azure Cosmos DB ドキュメントに変更を保存する  |[出力バインド](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> このリファレンスは、[Azure Functions のバージョン 2.x 以降](functions-versions.md)を対象としています。  Functions 1.x でこれらのバインドを使用する方法については、[Azure Functions 1.x の Azure Cosmos DB のバインド](functions-bindings-cosmosdb.md)に関するページを参照してください。
>
> このバインドの元の名称は DocumentDB でした。 Functions バージョン 2.x 以降では、トリガー、バインド、およびパッケージの名称はすべて、Cosmos DB になっています。

## <a name="supported-apis"></a>サポート対象 API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Functions アプリに追加する

### <a name="functions-2x-and-higher"></a>Functions 2.x 以降

トリガーとバインドを使用するには、適切なパッケージを参照する必要があります。 NuGet パッケージは .NET クラス ライブラリに使用されますが、他のすべてのアプリケーションの種類には拡張バンドルが使用されます。

| 言語                                        | 追加手段                                   | 解説 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet パッケージ] バージョン 3.x をインストールする | |
| C# スクリプト、Java、JavaScript、Python、PowerShell | [拡張機能バンドル]を登録する          | Visual Studio Code で使用するには [Azure Tools 拡張機能]をお勧めします。 |
| C# スクリプト (Azure portal ではオンラインのみ)         | バインディングを追加する                            | 関数アプリを再発行せずにポータルで既存のバインディング拡張機能を更新するには、[拡張機能の更新]に関する記事を参照してください。 |

[NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[拡張機能バンドル]: ./functions-bindings-register.md#extension-bundles
[拡張機能の更新]: ./functions-bindings-register.md
[Azure Tools 拡張機能]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="cosmos-db-extension-4x-and-higher"></a>Cosmos DB 拡張機能 4.x 以降

Cosmos DB のバインド拡張機能の新しいバージョンは、[プレビュー NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB/4.0.0-preview1)として入手できます。 このプレビューでは、[シークレットではなく ID を使用して接続する](./functions-reference.md#configure-an-identity-based-connection)機能が導入されています。 .NET アプリケーションの場合は、バインドできる型も変更され、v2 SDK `Microsoft.Azure.DocumentDB` からの型が v3 SDK [Microsoft.Azure.Cosmos](../cosmos-db/sql/sql-api-sdk-dotnet-standard.md) からの新しい型に置き換えられます。 これらの新しい型の相違点と、それらへの移行方法の詳細については、[SDK 移行ガイド](../cosmos-db/sql/migrate-dotnet-v3.md)、[トリガー](./functions-bindings-cosmosdb-v2-trigger.md)、[入力バインディング](./functions-bindings-cosmosdb-v2-input.md)、および[出力バインディング](./functions-bindings-cosmosdb-v2-output.md)の例を参照してください。

> [!NOTE]
> 現在、Elastic Premium プランでは、4.x プレビュー拡張機能を使用したシークレットは使用できず、ID による認証が使用できるだけです。 

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x アプリでは、[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet パッケージ バージョン 2.x が自動的に参照されます。

## <a name="exceptions-and-return-codes"></a>例外とリターン コード

| バインド | リファレンス |
|---|---|
| Cosmos DB | [CosmosDB のエラー コード](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>host.json 設定

このセクションでは、Azure Functions バージョン 2.x でこのバインディングに使用可能なグローバル構成設定について説明します。 Azure Functions バージョン 2.x でのグローバル構成設定の詳細については、[Azure Functions バージョン 2.x の host.json のリファレンス](functions-host-json.md)を参照してください。

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|プロパティ  |Default |説明 |
|----------|--------|------------|
|GatewayMode|Gateway|Azure Cosmos DB サービスに接続する際に関数で使用される接続モード。 オプションは `Direct` と `Gateway` です|
|Protocol|Https|Azure Cosmos DB サービスに接続する際に関数で使用される接続プロトコル。 両方のモードの説明については[こちら](../cosmos-db/performance-tips.md#networking)を参照してください <br><br> この設定は、[拡張機能バージョン 4.x](#cosmos-db-extension-4x-and-higher) では使用できません。 |
|leasePrefix|該当なし|アプリ内のすべての関数で使用するプレフィックスをリースします。 <br><br> この設定は、[拡張機能バージョン 4.x](#cosmos-db-extension-4x-and-higher) では使用できません。|

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB ドキュメントが作成または変更されたときに関数を実行する (トリガー)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Azure Cosmos DB ドキュメントを読み込む (入力バインド)](./functions-bindings-cosmosdb-v2-input.md)
- [Azure Cosmos DB ドキュメントに変更を保存する (出力バインド)](./functions-bindings-cosmosdb-v2-output.md)
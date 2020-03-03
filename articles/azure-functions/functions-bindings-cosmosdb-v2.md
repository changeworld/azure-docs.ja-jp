---
title: Functions 2.x の Azure Cosmos DB バインド
description: Azure Functions で Azure Cosmos DB のトリガーとバインドを使用する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: f258a7aff52796a53540706bc8413575d63c9e7d
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605764"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-overview"></a>Azure Functions 2.x での Azure Cosmos DB のトリガーとバインドの概要

> [!div class="op_single_selector" title1="使用している Azure Functions ランタイムのバージョンを選択します。 "]
> * [Version 1](functions-bindings-cosmosdb.md)
> * [Version 2](functions-bindings-cosmosdb-v2.md)

この一連の記事では、Azure Functions 2.x で [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) のバインドを操作する方法について説明します。 Azure Functions は、Azure Cosmos DB のトリガー、入力、出力のバインドをサポートしています。

| アクション | Type |
|---------|---------|
| Azure Cosmos DB ドキュメントが作成または変更されたときに関数を実行する | [トリガー](./functions-bindings-cosmosdb-v2-trigger.md) |
| Azure Cosmos DB ドキュメントを読み込む | [入力バインド](./functions-bindings-cosmosdb-v2-input.md) |
| Azure Cosmos DB ドキュメントに変更を保存する  |[出力バインド](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> このリファレンスでは、[Azure Functions のバージョン 2.x](functions-versions.md) を対象としています。  Functions 1.x でこれらのバインドを使用する方法については、[Azure Functions 1.x の Azure Cosmos DB のバインド](functions-bindings-cosmosdb.md)に関するページを参照してください。
>
> このバインドの元の名称は DocumentDB でした。 Functions バージョン 2.x では、トリガー、バインド、およびパッケージの名称はすべて、Cosmos DB になっています。

## <a name="supported-apis"></a>サポート対象 API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Functions アプリに追加する

### <a name="functions-2x-and-higher"></a>Functions 2.x 以降

トリガーとバインドを使用するには、適切なパッケージを参照する必要があります。 NuGet パッケージは .NET クラス ライブラリに使用されますが、他のすべてのアプリケーションの種類には拡張バンドルが使用されます。

| Language                                        | 追加手段                                   | 解説 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet パッケージ] バージョン 3.x をインストールする | |
| C# スクリプト、Java、JavaScript、Python、PowerShell | [拡張機能バンドル]を登録する          | Visual Studio Code で使用するには [Azure Tools 拡張機能]をお勧めします。 |
| C# スクリプト (Azure portal ではオンラインのみ)         | バインディングを追加する                            | 関数アプリを再発行せずにポータルで既存のバインディング拡張機能を更新するには、[拡張機能の更新]に関する記事を参照してください。 |

[NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[拡張機能バンドル]: ./functions-bindings-register.md#extension-bundles
[拡張機能の更新]: ./install-update-binding-extensions-manual.md
[Azure Tools 拡張機能]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x アプリでは、[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet パッケージ バージョン 2.x が自動的に参照されます。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB ドキュメントが作成または変更されたときに関数を実行する (トリガー)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Azure Cosmos DB ドキュメントを読み込む (入力バインド)](./functions-bindings-cosmosdb-v2-input.md)
- [Azure Cosmos DB ドキュメントに変更を保存する (出力バインド)](./functions-bindings-cosmosdb-v2-output.md)

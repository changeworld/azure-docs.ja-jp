---
title: Azure Functions における Azure Blob Storage のトリガーとバインド
description: Azure Functions で Azure Blob Storage のトリガーとバインドを使用する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 5d93ebd083ccd887b8267c1bc9b82c6ba7b34c05
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432379"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Azure Functions における Azure Blob Storage のバインドの概要

Azure Functions は[トリガーとバインド](./functions-triggers-bindings.md)を使用して [Azure Storage](../storage/index.yml) と統合されます。 Blob Storage との統合により、BLOB データの変更に対応し、値の読み取りと書き込も行う関数を作成することができます。

| アクション | 種類 |
|---------|---------|
| Blob Storage データが変更されたときに関数を実行する | [トリガー](./functions-bindings-storage-blob-trigger.md) |
| 関数で Blob Storage データを読み取る | [入力バインド](./functions-bindings-storage-blob-input.md) |
| 関数で Blob Storage データを書き込めるようにする |[出力バインド](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Functions アプリに追加する

### <a name="functions-2x-and-higher"></a>Functions 2.x 以降

トリガーとバインドを使用するには、適切なパッケージを参照する必要があります。 NuGet パッケージは .NET クラス ライブラリに使用されますが、他のすべてのアプリケーションの種類には拡張バンドルが使用されます。

| Language                                        | 追加手段                                   | 解説 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet パッケージ] バージョン 3.x をインストールする | |
| C# スクリプト、Java、JavaScript、Python、PowerShell | [拡張機能バンドル]を登録する          | Visual Studio Code で使用するには [Azure Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)をお勧めします。 |
| C# スクリプト (Azure portal ではオンラインのみ)         | バインディングを追加する                            | 関数アプリを再発行せずにポータルで既存のバインディング拡張機能を更新するには、[拡張機能の更新]に関する記事を参照してください。 |

#### <a name="storage-extension-5x-and-higher"></a>ストレージ拡張機能 5.x 以降

ストレージのバインド拡張機能の新しいバージョンが利用可能になりました。 [シークレットではなく ID を使用して接続する](./functions-reference.md#configure-an-identity-based-connection)機能が導入されています。 マネージド ID を使用して関数アプリを構成するチュートリアルについては、[ID ベースの接続を使用した関数アプリの作成](./functions-identity-based-connections-tutorial.md)に関するチュートリアルを参照してください。 .NET アプリケーションの場合は、新しい拡張機能バージョンによって、バインドできる型も変更されます。これにより、`WindowsAzure.Storage` および `Microsoft.Azure.Storage` の型が [Azure.Storage.Blobs](/dotnet/api/azure.storage.blobs) の新しい型に置き換えられます。 これらの異なる新しい型と、それらを移行する方法については、[Azure.Storage.Blobs の移行ガイド](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/storage/Azure.Storage.Blobs/AzureStorageNetMigrationV12.md)に関するページを参照してください。

この拡張機能のバージョンは、[NuGet パッケージ] バージョン 5.x をインストールすることによって利用できます。または、`host.json` ファイルに次のものを追加して、拡張機能バンドル v3 から追加することもできます。

```json
{
  "version": "2.0",
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[3.3.0, 4.0.0)"
  }
}
```

詳細については、[ユーザーの更新]に関するページを参照してください。

[core tools]: ./functions-run-local.md
[拡張機能バンドル]: ./functions-bindings-register.md#extension-bundles
[NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[拡張機能の更新]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x アプリでは、[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet パッケージ バージョン 2.x が自動的に参照されます。

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="hostjson-settings"></a>host.json 設定

このセクションでは、このバインディングの関数で使用できる関数アプリの構成設定について説明します。 これらの設定は、[拡張機能バージョン 5.0.0 以上](#storage-extension-5x-and-higher)を使用している場合にのみ適用されます。 次の host.json ファイルの例には、このバインドのバージョン 2.x 以降の設定のみが含まれています。 バージョン 2.x 以降のバージョンでの関数アプリ構成設定の詳細については、「[Azure Functions の host.json のリファレンス](functions-host-json.md)」を参照してください。

> [!NOTE]
> このセクションは、5.0.0 より前の拡張機能のバージョンには適用されません。 それらの以前のバージョンでは、BLOB に対する関数アプリ規模の構成設定はありません。

```json
{
    "version": "2.0",
    "extensions": {
        "blobs": {
            "maxDegreeOfParallelism": "4"
        }
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------|
|maxDegreeOfParallelism|8 * (使用可能なコアの数)|BLOB によってトリガーされる関数ごとに許可される、同時呼び出しの整数の値。 許容される最小値は 1 です。|

## <a name="next-steps"></a>次のステップ

- [Blob Storage データの変更時に関数を実行する](./functions-bindings-storage-blob-trigger.md)
- [関数が実行されたときに Blob Storage データを読み取る](./functions-bindings-storage-blob-input.md)
- [関数から Blob Storage データを書き込む](./functions-bindings-storage-blob-output.md)

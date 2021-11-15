---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 70544909ae419cdf215bdad88c3ac19968f6e4f0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131521111"
---
## <a name="add-to-your-functions-app"></a>Functions アプリに追加する

### <a name="functions-2x-and-higher"></a>Functions 2.x 以降

トリガーとバインドを使用するには、適切なパッケージを参照する必要があります。 NuGet パッケージは .NET クラス ライブラリに使用されますが、他のすべてのアプリケーションの種類には拡張バンドルが使用されます。

| Language                                        | 追加手段                                   | 解説 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet パッケージ] バージョン 3.x をインストールする | |
| C# スクリプト、Java、JavaScript、Python、PowerShell | [拡張機能バンドル]を登録する          | Visual Studio Code で使用するには [Azure Tools 拡張機能]をお勧めします。 |
| C# スクリプト (Azure portal ではオンラインのみ)         | バインディングを追加する                            | 関数アプリを再発行せずにポータルで既存のバインディング拡張機能を更新するには、[拡張機能の更新]に関する記事を参照してください。 |

[NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[拡張機能バンドル]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[拡張機能の更新]: ../articles/azure-functions/functions-bindings-register.md
[Azure Tools 拡張機能]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="event-hubs-extension-5x-and-higher"></a>Event Hubs 拡張機能 5.x 以降

Event Hubs のバインド拡張機能の新しいバージョンをご利用いただけるようになりました。 [シークレットではなく ID を使用して接続する](../articles/azure-functions/functions-reference.md#configure-an-identity-based-connection)機能が導入されています。 マネージド ID を使用して関数アプリを構成するチュートリアルについては、[ID ベースの接続を使用した関数アプリの作成に関するチュートリアル](../articles/azure-functions/functions-identity-based-connections-tutorial.md)を参照してください。 .NET アプリケーションの場合は、バインドできる型も変更されます。これにより、`Microsoft.Azure.EventHubs` の型が [Azure.Messaging.EventHubs](/dotnet/api/azure.messaging.eventhubs) の新しい型に置き換えられます。

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
[NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/
[拡張機能の更新]: ./functions-bindings-register.md
[Azure Tools 拡張機能]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x アプリでは、[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet パッケージ バージョン 2.x が自動的に参照されます。

## <a name="hostjson-settings"></a>host.json 設定
<a name="host-json"></a>

[host.json](../articles/azure-functions/functions-host-json.md#eventhub) ファイルには、Event Hubs トリガーの動作を制御する設定が含まれています。 構成は、Azure Functions のバージョンによって異なります。

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]
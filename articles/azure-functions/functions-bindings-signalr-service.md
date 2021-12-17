---
title: Azure Functions における SignalR Service のバインド
description: Azure Functions で SignalR Service のバインドを使用する方法を説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 34460fb0076c4cb666242d996add913cc2b74d22
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108641"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Azure Functions における SignalR サービスのバインド

こちらの一連の記事では、Azure Functions で SignalR Service のバインドを使用して、[Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/) に接続されたクライアントに対して認証を行い、リアルタイム メッセージを送信する方法を説明しています。 Azure Functions は、SignalR Service の入力および出力バインドをサポートしています。

| アクション | Type |
|---------|---------|
| SignalR Service からのメッセージの処理 | [トリガー バインド](./functions-bindings-signalr-service-trigger.md) |
| サービス エンドポイント URL とアクセス トークンを返す | [入力バインド](./functions-bindings-signalr-service-input.md) |
| SignalR Service メッセージを送信する |[出力バインド](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Functions アプリに追加する

### <a name="functions-2x-and-higher"></a>Functions 2.x 以降

トリガーとバインドを使用するには、適切なパッケージを参照する必要があります。 NuGet パッケージは .NET クラス ライブラリに使用されますが、他のすべてのアプリケーションの種類には拡張バンドルが使用されます。

| 言語                                        | 追加手段                                   | 解説 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet パッケージ] バージョン 3.x をインストールする | |
| C# スクリプト、Java、JavaScript、Python、PowerShell | [拡張機能バンドル]を登録する          | Visual Studio Code で使用するには [Azure Tools 拡張機能]をお勧めします。 |
| C# スクリプト (Azure portal ではオンラインのみ)         | バインディングを追加する                            | 関数アプリを再発行せずにポータルで既存のバインディング拡張機能を更新するには、[拡張機能の更新]に関する記事を参照してください。 |

[NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[拡張機能バンドル]: ./functions-bindings-register.md#extension-bundles
[拡張機能の更新]: ./functions-bindings-register.md
[Azure Tools 拡張機能]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

SignalR Service と Azure Functions を構成して一緒に使用する方法の詳細については、「[Azure Functions development and configuration with Azure SignalR Service (Azure SignalR Service を使用した Azure Functions の開発と構成)](../azure-signalr/signalr-concept-serverless-development-config.md)」を参照してください。

### <a name="annotations-library-java-only"></a>注釈ライブラリ (Java のみ)

SignalR Service の注釈を Java 関数で使用するには、*azure-functions-java-library-signalr* アーティファクト (バージョン 1.0 以降) への依存関係を *pom.xml* ファイルに追加する必要があります。

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="connection-string-settings"></a>接続文字列の設定

接続文字列を含んだアプリケーション設定を指す `AzureSignalRConnectionString` キーを _host.json_ ファイルに追加します。 ローカル開発の場合、この値は _local.settings.json_ ファイルに存在します。

## <a name="next-steps"></a>次のステップ

- [SignalR Service からのメッセージの処理 (トリガー バインド)](./functions-bindings-signalr-service-trigger.md)
- [サービス エンドポイント URL とアクセス トークンを返す (入力バインド)](./functions-bindings-signalr-service-input.md)
- [SignalR Service メッセージを送信する (出力バインド)](./functions-bindings-signalr-service-output.md)
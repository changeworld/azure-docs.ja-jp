---
title: Azure Functions における SignalR Service のバインド
description: Azure Functions で SignalR Service のバインドを使用する方法を説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 1446808b77e5eea78a9912db4c7a8e2dd783f33a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104378"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Azure Functions における SignalR サービスのバインド

こちらの一連の記事では、Azure Functions で SignalR Service のバインドを使用して、[Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/) に接続されたクライアントに対して認証を行い、リアルタイム メッセージを送信する方法を説明しています。 Azure Functions は、SignalR Service の入力および出力バインドをサポートしています。

| アクション | Type |
|---------|---------|
| サービス エンドポイント URL とアクセス トークンを返す | [入力バインド](./functions-bindings-signalr-service-input.md) |
| SignalR Service メッセージを送信する |[出力バインド](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Functions アプリに追加する

### <a name="functions-2x-and-higher"></a>Functions 2.x 以降

トリガーとバインドを使用するには、適切なパッケージを参照する必要があります。 NuGet パッケージは .NET クラス ライブラリに使用されますが、他のすべてのアプリケーションの種類には拡張バンドルが使用されます。

| Language                                        | 追加手段                                   | 解説 
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

## <a name="next-steps"></a>次のステップ

- [サービス エンドポイント URL とアクセス トークンを返す (入力バインド)](./functions-bindings-signalr-service-input.md)
- [SignalR Service メッセージを送信する (出力バインド)](./functions-bindings-signalr-service-output.md)
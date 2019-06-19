---
title: Azure Functions バインド拡張機能を登録する
description: 環境に基づく Azure Functions バインド拡張機能の登録について説明します。
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 53eb5fc9389d913ecacec3729a06e47a1c2bf56b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65864546"
---
# <a name="register-azure-functions-binding-extensions"></a>Azure Functions バインド拡張機能を登録する

Azure Functions バージョン 2.x では、[バインド](./functions-triggers-bindings.md)を関数ランタイムとは別のパッケージとして利用できます。 .NET 関数は NuGet パッケージを介してバインドにアクセスしますが、拡張機能バンドルを使用すると、他の関数から構成設定を介してすべてのバインドにアクセスできるようになります。

バインド拡張機能に関連する以下の項目を考慮してください。

- バインド拡張機能は、[Visual Studio 2019 を使用して C# クラス ライブラリを作成する](#local-csharp)場合を除き、Functions 1.x では明示的に登録されません。

- HTTP とタイマーのトリガーは既定でサポートされており、拡張機能は必要ありません。

次の表に、バインディングを登録するタイミングと方法を示します。

| 開発環境 |登録<br/> (Functions 1.x)  |登録<br/> (Functions 2.x)  |
|-------------------------|------------------------------------|------------------------------------|
|Azure ポータル|自動|自動|
|非.NET 言語またはローカルの Azure Core Tools の開発|自動|[Azure Functions Core Tools と拡張機能バンドルを使用する](#local-development-with-azure-functions-core-tools-and-extension-bundles)|
|Visual Studio 2019 を使用する C# クラス ライブラリ|[NuGet ツールを使用](#c-class-library-with-visual-studio-2019)|[NuGet ツールを使用](#c-class-library-with-visual-studio-2019)|
|Visual Studio Code を使用する C# クラス ライブラリ|該当なし|[.NET Core CLI を使用](#c-class-library-with-visual-studio-code)|

## <a name="local-development-with-azure-functions-core-tools-and-extension-bundles"></a>Azure Functions Core Tools と拡張機能バンドルを使用するローカルの開発

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2019"></a>Visual Studio 2019 を使用する C# クラス ライブラリ

**Visual Studio 2019** では、次の例に示すように [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) コマンドを使用して、Package Manager Console からパッケージをインストールできます。

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

特定のバインディングに使用するパッケージ名は、該当のバインディングのリファレンス記事に示されています。 たとえば、[Service Bus バインディングのリファレンス記事にある「パッケージ」セクション](functions-bindings-service-bus.md#packages---functions-1x)を参照してください。

例の中の `<TARGET_VERSION>` を `3.0.0-beta5` などの特定のバージョンのパッケージに置き換えます。 有効なバージョンは、[NuGet.org](https://nuget.org) の個々のパッケージ ページに記載されています。Functions ランタイム 1.x または 2.x に対応する主要なバージョンは、バインデイングのリファレンス記事に示されています。

## <a name="c-class-library-with-visual-studio-code"></a>Visual Studio Code を使用する C# クラス ライブラリ

**Visual Studio Code** では、次の例のように、.NET Core CLI の [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) コマンドを使用してコマンド プロンプトからパッケージをインストールできます。

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

.NET Core CLI は、Azure Functions 2.x 開発のみに使用できます。

特定のバインディングに使用するパッケージ名は、該当のバインディングのリファレンス記事に示されています。 たとえば、[Service Bus バインディングのリファレンス記事にある「パッケージ」セクション](functions-bindings-service-bus.md#packages---functions-1x)を参照してください。

例の中の `<TARGET_VERSION>` を `3.0.0-beta5` などの特定のバージョンのパッケージに置き換えます。 有効なバージョンは、[NuGet.org](https://nuget.org) の個々のパッケージ ページに記載されています。Functions ランタイム 1.x または 2.x に対応する主要なバージョンは、バインデイングのリファレンス記事に示されています。

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバンドの例](./functions-bindings-example.md)


---
title: Azure Functions バインド拡張機能を登録する
description: 環境に基づく Azure Functions バインド拡張機能の登録について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 09/14/2020
ms.author: cshoe
ms.openlocfilehash: b847243543eb8667a38d253e7eaad215af29e7b2
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852341"
---
# <a name="register-azure-functions-binding-extensions"></a>Azure Functions バインド拡張機能を登録する

Azure Functions バージョン 2.x 以降では、Functions ランタイムには既定で HTTP とタイマー トリガーのみが含まれています。 その他の[トリガーとバインド](./functions-triggers-bindings.md)は、個別のパッケージとして提供されます。

.NET クラス ライブラリ関数アプリでは、プロジェクトに NuGet パッケージとしてインストールされているバインドが使用されます。 拡張機能バンドルを使用すると、.NET 関数アプリ以外で、.NET インフラストラクチャを扱うことなく同じバインドを使用できるようになります。

次の表に、バインディングを登録するタイミングと方法を示します。

| 開発環境 |登録<br/> (Functions 1.x)  |登録<br/> (Functions 2.x 以降)  |
|-------------------------|------------------------------------|------------------------------------|
|Azure portal|自動|自動<sup>*</sup>|
|.NET 以外の言語|自動|[拡張機能バンドル](#extension-bundles) を使用する(推奨) または[明示的に拡張機能をインストールする](#explicitly-install-extensions)|
|Visual Studio を使用する C# クラス ライブラリ|[NuGet ツールを使用](#vs)|[NuGet ツールを使用](#vs)|
|Visual Studio Code を使用する C# クラス ライブラリ|該当なし|[.NET Core CLI を使用](#vs-code)|

<sup>*</sup> ポータルでは拡張機能バンドルが使用されます。

## <a name="access-extensions-in-non-net-languages"></a>.NET 以外の言語で拡張機能にアクセスする

Java、JavaScript、PowerShell、Python、およびカスタム ハンドラー関数アプリでは、バインドへのアクセスに拡張機能バンドルを使用することをお勧めします。 拡張機能バンドルを使用できない場合は、バインド拡張機能を明示的にインストールできます。

### <a name="extension-bundles"></a><a name="extension-bundles"></a>拡張機能バンドル

拡張機能バンドルを使用すると、互換性のある一連のバインド拡張機能を関数アプリに追加できます。 拡張機能バンドルは、アプリの *host.json* ファイルで有効にします。

バージョン 2.x およびそれ以降のバージョンの Functions ランタイムで拡張機能バンドルを使用できます。

拡張機能バンドルはバージョン管理されています。 各バージョンには、連携して機能することが確認された特定のバインド拡張機能のセットが含まれています。 バンドルのバージョンは、アプリで必要な拡張機能に基づいて選択します。

関数アプリに拡張機能バンドルを追加するには、`extensionBundle` セクションを *host.json* に追加します。 多くの場合、これは Visual Studio Code と Azure Functions Core Tools によって自動的に追加されます。

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

次の表に、現在使用可能な既定の *Microsoft.Azure.Functions.ExtensionBundle* バンドルのバージョン、およびそれらに含まれる拡張機能へのリンクを示します。

| バンドルのバージョン | host.json でのバージョン | 含まれる拡張機能 |
| --- | --- | --- |
| 1.x | `[1.*, 2.0.0)` | バンドルを生成するために使用される [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/v1.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) を参照してください |
| 2.x | `[2.*, 3.0.0)` | バンドルを生成するために使用される [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/v2.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) を参照してください |
| 3.x | `[3.3.0, 4.0.0)` | バンドルを生成するために使用される [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/v3.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) を参照してください |

> [!NOTE]
> host.json でカスタムのバージョン範囲を指定することもできますが、この表にあるバージョンの値を使用することをお勧めします。

### <a name="explicitly-install-extensions"></a>拡張機能を明示的にインストールする

拡張機能バンドルを使用できない場合、Azure Functions Core Tools をローカルで使用し、プロジェクトで必要とされる特定の拡張機能パッケージをインストールできます。

> [!IMPORTANT]
> 拡張機能バンドルを使用している関数アプリには、拡張機能を明示的にインストールできません。 拡張機能を明示的にインストールする前に、*host.json* の `extensionBundle` セクションを削除します。

次の項目では、場合によっては拡張機能の手動インストールが必要になるいくつかの理由を説明します。

* バンドルで利用できない特定のバージョンの拡張機能にアクセスする必要があります。
* バンドルで利用できないカスタム拡張機能にアクセスする必要があります。
* 1 つのバンドルで利用できない特定の組み合わせの拡張機能にアクセスする必要があります。

> [!NOTE]
> Core Tools を使用して拡張機能を手動でインストールするには、[.NET Core 3.1 SDK](https://dotnet.microsoft.com/download) がインストールされている必要があります。 .NET Core SDK は NuGet から拡張機能をインストールする目的で Azure Functions Core Tools によって使用されます。 Azure Functions 拡張機能を使用するために .NET の知識は必要ありません。

拡張機能を明示的にインストールすると、extensions.csproj という名前の .NET プロジェクト ファイルがプロジェクトのルートに追加されます。 このファイルによって、関数に必要とされる NuGet パッケージのセットが定義されます。 このファイルで [NuGet パッケージ参照](/nuget/consume-packages/package-references-in-project-files)を使用することができますが、Core Tools では、ファイルを手動編集しなくても拡張機能をインストールできます。

Core Tools を使用し、ローカル プロジェクトで必要な拡張機能をインストールする方法がいくつかあります。 

#### <a name="install-all-extensions"></a>すべての拡張機能をインストールする 

次のコマンドを使用し、ローカル プロジェクトのバインドで使用されるすべての拡張機能パッケージを自動追加します。

```command
func extensions install
```

このコマンドでは、*function.json* ファイルを読み取って必要なパッケージを確認し、パッケージをインストールして、拡張プロジェクト (extensions.csproj) を再構築します。 現在のバージョンで新しいバインドが追加されますが、既存のバインドは更新されません。 新しいバージョンをインストールするときに、`--force` オプションを使用して既存のバインドを最新バージョンに更新します。 詳細については、[`func extensions install` コマンド](functions-core-tools-reference.md#func-extensions-install)を参照してください。

Core Tools で認識されないバインドが関数アプリで使用されている場合、特定の拡張機能を手動インストールする必要があります。

#### <a name="install-a-specific-extension"></a>特定の拡張機能をインストールする

次のコマンドを使用し、特定のバージョンの特定の拡張機能パッケージをインストールします。今回は、Storage 拡張機能です。

```command
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 5.0.0
```

詳細については、[`func extensions install` コマンド](functions-core-tools-reference.md#func-extensions-install)を参照してください。

## <a name="install-extensions-from-nuget-in-net-languages"></a><a name="local-csharp"></a>.NET 言語で NuGet から拡張機能をインストールする

C# クラス ライブラリ ベースの関数プロジェクトについては、拡張機能を直接インストールする必要があります。 拡張機能バンドルは、C# クラス ライブラリ ベースではないプロジェクト専用に設計されています。

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a> Visual Studio を使用する C\# クラス ライブラリ

**Visual Studio** では、次の例に示すように [Install-Package](/nuget/tools/ps-ref-install-package) コマンドを使用して、Package Manager Console からパッケージをインストールできます。

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

特定のバインディングに使用するパッケージ名は、該当のバインディングのリファレンス記事に示されています。 たとえば、[Service Bus バインディングのリファレンス記事にある「パッケージ」セクション](functions-bindings-service-bus.md#functions-1x)を参照してください。

例の中の `<TARGET_VERSION>` を `3.0.0-beta5` などの特定のバージョンのパッケージに置き換えます。 有効なバージョンは、[NuGet.org](https://nuget.org) の個々のパッケージ ページに記載されています。Functions ランタイム 1.x または 2.x に対応する主要なバージョンは、バインデイングのリファレンス記事に示されています。

`Install-Package` を使用してバインディングの参照を行う場合、[拡張機能バンドル](#extension-bundles)を使用する必要はありません。 この方法は、Visual Studio でビルドされたクラス ライブラリに固有のものです。

### <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a>Visual Studio Code を使用する C# クラス ライブラリ

**Visual Studio Code** では、コマンド プロンプトから .NET Core CLI の [dotnet add package](/dotnet/core/tools/dotnet-add-package) コマンドを使用して C# クラス ライブラリ プロジェクトのパッケージをインストールします。 次の例は、バインディングの追加方法を示しています。

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI は、Azure Functions 2.x 開発のみに使用できます。

`<BINDING_TYPE_NAME>` は、必要なバインドが含まれているパッケージの名前に置き換えます。 対象のバインディングのリファレンス記事は、[サポートされるバインディングの一覧](./functions-triggers-bindings.md#supported-bindings)で見つけることができます。

例の中の `<TARGET_VERSION>` を `3.0.0-beta5` などの特定のバージョンのパッケージに置き換えます。 有効なバージョンは、[NuGet.org](https://nuget.org) の個々のパッケージ ページに記載されています。Functions ランタイム 1.x または 2.x に対応する主要なバージョンは、バインデイングのリファレンス記事に示されています。

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバンドの例](./functions-bindings-example.md)

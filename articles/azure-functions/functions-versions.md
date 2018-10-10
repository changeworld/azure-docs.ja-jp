---
title: Azure Functions ランタイム バージョンの概要
description: Azure Functions では、複数のバージョンのランタイムがサポートされます。 バージョン間の違いと、適切なバージョンを選択する方法について説明します。
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: glenga
ms.openlocfilehash: a601ea42549abad84d6cab5c429cf94147776436
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978626"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions ランタイム バージョンの概要

 Azure Functions Runtime には、2 つのメジャー バージョン 1.x と 2.x があります。 新機能が動作し、機能強化が行われている現在のバージョンは 2.x ですが、どちらも運用シナリオに対してサポートされています。  以下では、2 つのバージョンの違い、各バージョンを作成する方法、および 1.x から 2.x にアップグレードする方法について詳しく説明します。

> [!NOTE] 
> この記事は、クラウド サービスの Azure Functions に関するものです。 オンプレミスで Azure Functions を実行できるプレビュー製品については、「[Azure Functions Runtime の概要 (プレビュー)](functions-runtime-overview.md)」をご覧ください。

## <a name="creating-1x-apps"></a>1.x アプリの作成

Azure Portal で作成される新しいアプリは、既定で 2.x に設定されます。これが最新バージョンであり、新しい機能への投資が行われています。  ただし、次のようにすればまだ v1.x アプリを作成できます。

1. Azure Portal から Azure 関数を作成します
1. 作成されたアプリを開き、ブランクの間に **[関数の設定]** を開きます
1. バージョンを ~2 から ~1 に変更します。  *アプリ内に関数がある場合、このトグルは無効になります*。
1. [保存] をクリックして、アプリを再起動します。  すべてのテンプレートが 1.x で作成されて実行されるようになります。

## <a name="cross-platform-development"></a>クロスプラットフォーム開発

ランタイム 1.x では、ポータルまたは Windows のみで開発とホスティングがサポートされます。 ランタイム 2.x は .NET Core 2 で実行されます。つまり、ランタイム 2.x は、macOS や Linux など、.NET Core でサポートされるすべてのプラットフォームで実行できます。 これにより、クロスプラットフォーム開発とホスティング シナリオが可能になります。

## <a name="languages"></a>Languages

ランタイム 2.x では、新しい言語拡張モデルが使用されます。 さらに、ツールとパフォーマンスを向上させるため、2.x では、各アプリは単一言語の関数のみに制限されています。 現在、2.x では C#、F#、JavaScript、Java の各言語がサポートされています。 Azure Functions 1.x の試験段階の言語は、新しいモデルを使用するよう更新されていないため、2.x ではサポートされません。 次の表は、各ランタイム バージョンでどのプログラミング言語がサポートされているかを示しています。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

詳細については、[サポートされている言語](supported-languages.md)に関するページを参照してください。

## <a name="migrating-from-1x-to-2x"></a>1.x から 2.x への移行

1.x で記述された既存のアプリを 2.x に移行したいことがあります。  バージョン間の移行において必要な考慮事項のほとんどは、上に挙げた言語ランタイムの変更に関連します (たとえば、C# は .NET Framework 4.7 から .NET Core 2 に移行しています)。  コードとライブラリが使用されている言語ランタイムと互換性があることを確認する必要があります。  また、以下で示すトリガー、バインド、および機能での変更にも注意してください。

### <a name="changes-in-triggers-and-bindings"></a>トリガーとバインドでの変更

トリガーとバインドのプロパティと構成のほとんどは、バージョン間で同じままですが、2.x では、すべてのトリガーまたはバインドをアプリにインストールする必要があります。 これに対する唯一の例外は、HTTP トリガーとタイマー トリガーです。  [バインディング拡張機能の登録とインストール](./functions-triggers-bindings.md#register-binding-extensions)に関するページをご覧ください。  バージョン間では、関数の `function.json` または属性も変わっている場合があることに注意してください (たとえば、CosmosDB の `connection` プロパティは、現在は `ConnectionStringSetting` です)。  各バインドのドキュメントへのリンクについては、[既存のバインド テーブル](#bindings)をご覧ください。

### <a name="changes-in-features-available"></a>使用可能な機能の変更点

言語とバインドの変更に加えて、バージョン間で削除、更新、または置換された機能がいくつかあります。  1.x を使用した後で 2.x を使用するときの主な考慮事項の一部を次に示します。  v2.x では以下の変更が行われました。

* 関数を呼び出すためのキーは、暗号化された BLOB ストレージに常に格納されます。 1.x の既定ではファイル ストレージに格納され、スロットなどの機能を有効にすると BLOB に移動できます。  1.x アプリを 2.x にアップグレードすると、現在ファイル ストレージに格納されているシークレットはリセットされます。
* パフォーマンスを向上させるため、"Webhook" 型トリガーは削除されて、"HTTP" トリガーに置き換えられます。
* ホストの構成 (`host.json`) は、空であるか、またはプロパティの 1 つとして `version` に `2.0` が含まれている必要があります。
* 監視と可観測性を向上させるため、WebJobs ダッシュ ボード (`AzureWebJobsDashboard`) は [Azure Application Insights](functions-monitoring.md) (`APPINSIGHTS_INSTRUMENTATIONKEY`) に置き換えられています
* アプリケーションの設定 (`local.settings.json`) のプロパティ `FUNCTIONS_WORKER_RUNTIME` の値は、アプリの言語 `dotnet | node | java | python` に対応している必要があります。
    * フットプリントと起動時間の向上のため、アプリは 1 つの言語に制限されます。 複数のアプリを発行して、同じソリューションで異なる言語の関数を使用できます。
* App Service プランでの関数に対する既定のタイムアウトは 30 分です。  手動で無制限に設定できます。
* [.NET Core の制限](https://github.com/Azure/azure-functions-host/issues/3414)のため、F# 関数に対する `.fsx` スクリプトは削除されました。 コンパイル済みの F# 関数は引き続きサポートされます。
* Webhook ベースのトリガー (Event Grid など) の形式が、`https://{app}/runtime/webhooks/{triggerName}` に変更されました。

### <a name="upgrading-a-locally-developed-application"></a>ローカルで開発されたアプリケーションのアップグレード

v1.x アプリをローカルで開発した場合、アプリまたはプロジェクトを変更して v2 と互換性があるようにできます。  新しいアプリを作成し、コードを新しいアプリにポートすることをお勧めします。  インプレース アップグレードを実行するために既存アプリに対して行うことができる変更がありますが、レガシ コードでは利用していないと思われる v1 と v2 の間の機能強化が他にもいくつかあります (たとえば、C# での `TraceWriter` から `ILogger` への変更)。  

推奨されるパスは、v2 テンプレートの 1 つから始めて、新しいプロジェクトまたはアプリにコードを移動します。

#### <a name="visual-studio-runtime-versions"></a>Visual Studio のランタイム バージョン

Visual Studio では、プロジェクトを作成するときにランタイムのバージョンを選択します。  Visual Studio には両方のメジャー バージョンに対するビットがあり、適切なものをプロジェクトで動的に利用できます。  これらの設定は、`.csproj` ファイルから派生されます。  1.x アプリのプロジェクトには次のプロパティがあります

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

v2 のプロジェクトのプロパティは次のとおりです

```xml
<TargetFramework>netstandard2.0</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

デバッグまたは発行をクリックすると、プロジェクトの設定に対して適切なバージョンが正しく設定されます。

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code と Azure Functions Core Tools

他のローカル ツールは、Azure Functions Core Tools に依存します。  これらのツールはコンピューターにインストールされ、一般に、開発用コンピューターにインストールされるバージョンは一度に 1 つだけです。  [Core Tools ツールの特定のバージョンをインストールする方法](./functions-run-local.md)に関するページをご覧ください。

VS Code に場合は、インストールされているツールのバージョンと一致するように `azureFunctions.projectRuntime` のユーザー設定を更新することが必要な場合があります。  これにより、新しいアプリの作成時に表示されるテンプレートと言語も更新されます。

### <a name="changing-version-of-apps-in-azure"></a>Azure でのアプリのバージョンの変更

発行されたアプリのバージョンは、アプリケーションの設定 `FUNCTIONS_RUNTIME_VERSION` によって設定されます。  これは、v2 アプリの場合は `~2` に設定され、v1 アプリの場合は `~1` に設定されます。  発行済みの既存の関数があるアプリのランタイム バージョンを、これらの関数のコードを変更することなく変更しないよう、強くお勧めします。  推奨されるパスは、新しい関数アプリを作成して、適切なバージョンに設定し、変更をテストしてから、以前のアプリを無効化または削除することです。

## <a name="bindings"></a>バインド 

ランタイム 2.x で使用される新しい[バインド拡張モデル](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)の利点は、次のとおりです。

* サード パーティのバインド拡張のサポート。
* ランタイムとバインドの分離。 これにより、バインド拡張を個別にバージョン管理したり、解放したりできます。 たとえば、基になる SDK の新しいバージョンに依存する拡張のバージョンにアップグレードするよう選択できます。
* より軽量な実行環境。ここでは、使用中のバインドのみがランタイムによって識別され、読み込まれます。

タイマー トリガーと HTTP トリガーを除いて、すべての組み込み Azure Functions バインドでこのモデルが採用され、既定では含まれなくなりました。 Visual Studio などのツールまたはポータルを通じて関数を作成するときに、これらの拡張が自動的にインストールされます。

各ランタイム バージョンでサポートされるバインドを次の表に示します。

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>次の手順

詳細については、次のリソースを参照してください。

* [Azure Functions をローカルでコーディングしてテストする](functions-run-local.md)
* [Azure Functions Runtime バージョンをターゲットにする方法](set-runtime-version.md)
* [リリース ノート](https://github.com/Azure/azure-functions-host/releases)

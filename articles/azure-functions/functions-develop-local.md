---
title: Azure Functions をローカルで開発して実行する
description: Azure 関数を Azure Functions で実行する前に、ローカル コンピューターでコーディングしてテストする方法について説明します。
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 4568cd1befc31cbec30758a3117c30762e7de406
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830857"
---
# <a name="code-and-test-azure-functions-locally"></a>Azure Functions をローカルでコーディングしてテストする

Azure Functions の開発やテストは、[Azure Portal] で行うことができますが、多くの開発者は、ローカル開発エクスペリエンスを好みます。 Functions では、ローカル コンピューター上でお気に入りのコード エディターや開発ツールを使用して、関数を作成し、テストすることができます。 ローカル関数はライブ Azure サービスに接続できるため、完全な Functions ランタイムを使用してローカル コンピューター上で関数をデバッグすることができます。

この記事には、ご希望の言語に対応した特定の開発環境へのリンクがあります。 また、ローカル開発に関する共有ガイダンスもいくつかあります。たとえば、[local.settings.json ファイル](#local-settings-file)に対する操作などです。 

## <a name="local-development-environments"></a>ローカル開発環境

ローカル コンピューター上で関数を開発する方法は、[言語](supported-languages.md)とツールの設定によって異なります。 ローカル開発をサポートする環境を次の表に示します。

|環境                              |Languages         |説明|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C# (クラス ライブラリ)](functions-dotnet-class-library.md)<br/>[C# 分離プロセス (.NET 5.0)](dotnet-isolated-process-guide.md)<br/>[JavaScript](functions-reference-node.md)<br/>[PowerShell](./create-first-function-vs-code-powershell.md)<br/>[Python](functions-reference-python.md) | [VS Code 用の Azure Functions 拡張](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)は、VS Code に対して Functions サポートを追加します。 Core Tools が必要です。 Core Tools のバージョン 2.x を使用した場合は、Linux、macOS、および Windows 上での開発がサポートされます。 詳細については、「[Create your first function using Visual Studio Code](./create-first-function-vs-code-csharp.md)」 (Visual Studio Code を使用して最初の関数を作成する) を参照してください。 |
| [コマンド プロンプトまたはターミナル](functions-run-local.md) | [C# (クラス ライブラリ)](functions-dotnet-class-library.md)<br/>[C# 分離プロセス (.NET 5.0)](dotnet-isolated-process-guide.md)<br/>[JavaScript](functions-reference-node.md)<br/>[PowerShell](functions-reference-powershell.md)<br/>[Python](functions-reference-python.md) | [Azure Functions Core Tools] は、関数を作成するためのコア ランタイムとテンプレートを提供しており、これにより、ローカル開発が可能です。 バージョン 2.x では、Linux、macOS、および Windows 上での開発がサポートされています。 すべての環境は、ローカル Functions ランタイムとして、Core Tools を利用します。 |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (クラス ライブラリ)](functions-dotnet-class-library.md)<br/>[C# 分離プロセス (.NET 5.0)](dotnet-isolated-process-guide.md) | Azure Functions ツールは、[Visual Studio 2019](https://www.visualstudio.com/vs/) 以降のバージョンの **Azure 開発** ワークロードに含まれています。 クラス ライブラリの関数をコンパイルして .dll を Azure に発行できます。 ローカル テスト用の Core Tools が含まれています。 詳細については、「[Develop Azure Functions using Visual Studio](functions-develop-vs.md)」(Visual Studio を使用して Azure Functions を開発する) を参照してください。 |
| [Maven](./create-first-function-cli-java.md) (各種) | [Java](functions-reference-java.md) | Maven アーキタイプは、Java 関数の開発を可能にする Core Tools をサポートしています。 バージョン 2.x では、Linux、macOS、および Windows 上での開発がサポートされています。 詳細については、「[Create your first function with Java and Maven](./create-first-function-cli-java.md)」(Java および Maven を使用して、最初の関数を作成する) を参照してください。 [Eclipse](functions-create-maven-eclipse.md) や [IntelliJ IDEA](functions-create-maven-intellij.md) を使った開発もサポートされます。 |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

これらの各ローカル開発環境では、関数アプリ プロジェクトを作成し、事前定義の Functions テンプレートを使用して新しい関数を作成できます。 各環境では、Core Tools が使用されています。そのため、マシン上の実際の Functions ランタイムに対して、その他のアプリの場合と同様に関数をテストしたり、デバッグしたりできます。 また、これらのどの環境からでも、関数アプリ プロジェクトを Azure に発行できます。

## <a name="local-settings-file"></a>ローカル設定ファイル

local.settings.json ファイルには、アプリの設定、およびローカルの開発ツールによって使用される設定が格納されます。 local.settings.json ファイル内の設定は、プロジェクトをローカルで実行している場合にのみ使用されます。 

> [!IMPORTANT]  
> local.settings.json には接続文字列などのシークレットが含まれている場合があるため、リモート リポジトリには絶対に格納しないようにしてください。 Functions をサポートするツールを使用すると、local.settings.json ファイル内の設定を、プロジェクトをデプロイしている関数アプリの[アプリ設定](functions-how-to-use-azure-function-app-settings.md#settings)と同期できます。

ローカル設定ファイルの構造は次のとおりです。

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

これらの設定は、プロジェクトをローカルで実行する場合にサポートされます。

| 設定      | 説明                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | この設定を `true` にすると、すべての値がローカル コンピューターのキーを使用して暗号化されます。 `func settings` コマンドと共に使用されます。 既定値は `false` です。 サービス接続文字列など、シークレットが local.settings.json に含まれている場合、それをローカル コンピューター上で暗号化することができます。 実行時には、ホストが設定の暗号化を自動的に解除します。 ローカルで暗号化された設定の読み取りを試す前に、`func settings decrypt` コマンドを使用してください。 |
| **`Values`** | プロジェクトをローカルで実行するときに使用されるアプリケーション設定のコレクションです。 これらのキーと値 (文字列と文字列) のペアは、[`AzureWebJobsStorage`] など、Azure 内の関数アプリでのアプリケーション設定に対応します。 多くのトリガーおよびバインドには、[BLOB ストレージ トリガー](functions-bindings-storage-blob-trigger.md#configuration)の `Connection` など、接続文字列アプリ設定を参照するプロパティがあります。 これらのプロパティでは、`Values` 配列にアプリケーション設定を定義する必要があります。 よく使用される設定の一覧については、後ろの表を参照してください。 <br/>値は、JSON オブジェクトまたは配列ではなく文字列である必要があります。 設定名には、コロン (`:`)、2 つの連続する下線 (`__`) を含めることはできません。 二重下線文字はランタイムによって予約されています。また、コロンは[依存関係の挿入](functions-dotnet-dependency-injection.md#working-with-options-and-settings)をサポートするために予約されています。 |
| **`Host`** | このセクションの設定により、ローカルでプロジェクトを実行するときの Functions ホスト プロセスをカスタマイズできます。 これらの設定は、Azure でプロジェクトを実行するときにも適用される、host.json 設定とは別のものです。 |
| **`LocalHttpPort`** | ローカルの Functions ホストの実行時に使用される既定のポートを設定します (`func host start`と`func run`)。 `--port` コマンド ライン オプションは、この設定より優先されます。 たとえば、Visual Studio IDE で実行する場合、[プロジェクトのプロパティ] の [デバッグ] ウィンドウに移動し、[Application Arguments]\(アプリケーション引数\) フィールドから入力できる `host start --port <your-port-number>` コマンドに、ポート番号を明示的に指定することで、ポート番号を変更できます。 |
| **`CORS`** | [クロス オリジン リソース共有 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) で許可されるオリジンを定義します。 スペースなしのコンマ区切りのリストでオリジンを指定します。 ワイルドカード値 (\*) がサポートされており、これによって任意のオリジンからの要求を許可できます。 |
| **`CORSCredentials`** |  `true` に設定すると、`withCredentials` 要求が許可されます。 |
| **`ConnectionStrings`** | コレクション。 関数のバインディングで使用される接続文字列にこのコレクションを使用しないでください。 このコレクションは、[Entity Framework](/ef/ef6/) など、構成ファイルの `ConnectionStrings` セクションから接続文字列を取得するのが一般的なフレームワークでのみ使用されます。 このオブジェクト内の接続文字列は、[System.Data.SqlClient](/dotnet/api/system.data.sqlclient) のプロバイダーの種類と共に、環境に追加されます。 他のアプリ設定では、このコレクション内の項目は Azure に発行されません。 ご自分の関数アプリの設定の `Connection strings` コレクションに、これらの値を明示的に追加する必要があります。 関数コードで [`SqlConnection`](/dotnet/api/system.data.sqlclient.sqlconnection) を作成する場合は、接続文字列の値を他の接続と共に、ポータル内の **アプリケーション設定** に格納する必要があります。 |

次のアプリケーション設定は、ローカルでの実行時に **`Values`** 配列に含めることができます。

| 設定 | 値 | 説明 |
|-----|-----|-----|
|**`AzureWebJobsStorage`**| ストレージ アカウント接続文字列、または<br/>`UseDevelopmentStorage=true`| Azure ストレージ アカウントの接続文字列を含みます。 HTTP 以外のトリガーを使用する場合には必須です。 詳しくは、[`AzureWebJobsStorage`] のリファレンスを参照してください。<br/>[Azure ストレージ エミュレーター](../storage/common/storage-use-emulator.md)がローカルにインストールされ、[`AzureWebJobsStorage`] を `UseDevelopmentStorage=true` に設定すると、Core Tools でエミュレーターが使用されます。 エミュレーターは開発中には便利ですが、デプロイする前に実際のストレージに接続してテストする必要があります。| 
|**`AzureWebJobs.<FUNCTION_NAME>.Disabled`**| `true`\|`false` | ローカルで実行しているときに関数を無効にするには、`"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"` をコレクションに追加します。`<FUNCTION_NAME>` は関数の名前です。 詳細については、[Azure Functions で関数を無効にする方法](disable-function.md#localsettingsjson)に関する記事を参照してください。 |
|**`FUNCTIONS_WORKER_RUNTIME`** | `dotnet`<br/>`node`<br/>`java`<br/>`powershell`<br/>`python`| Functions ランタイムのターゲット言語を示します。 バージョン 2.x 以上の Functions ランタイムで必須です。 この設定は、お客様のプロジェクト用に Core Tools によって生成されます。 詳細については、[`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) のリファレンスを参照してください。|
| **`FUNCTIONS_WORKER_RUNTIME_VERSION`** | `~7` |ローカルでの実行時に使用される PowerShell 7 を示します。 設定されていない場合は、PowerShell Core 6 が使用されます。 この設定は、ローカルでの実行時にのみ使用されます。 Azure で実行する場合、PowerShell ランタイムのバージョンは、`powerShellVersion` サイト構成設定によって決まります。これは、[ポータルで設定](functions-reference-powershell.md#changing-the-powershell-version)できます。 | 

## <a name="next-steps"></a>次のステップ

+ Visual Studio 2019 を使用して、ローカルでコンパイル済み C# 関数を開発する方法の詳細については、「[Visual Studio を使用する Azure Functions の開発](functions-develop-vs.md)」を参照してください。
+ Mac、Linux、または Windows コンピューター上で VS Code を使用して、ローカルで関数を開発する方法の詳細については、ご希望の言語に対応する Visual Studio Code の概要に関する記事をご覧ください。
    + [C# クラス ライブラリ](create-first-function-vs-code-csharp.md)
    + [C# 分離プロセス (.NET 5.0)](create-first-function-vs-code-csharp.md?tabs=isolated-process)
    + [Java](create-first-function-vs-code-java.md)
    + [JavaScript](create-first-function-vs-code-node.md)
    + [PowerShell](create-first-function-vs-code-powershell.md)
    + [Python](create-first-function-vs-code-python.md)
    + [TypeScript](create-first-function-vs-code-typescript.md)
+ コマンド プロンプトまたはターミナルから関数を開発する方法の詳細については、「[Work with Azure Functions Core Tools](functions-run-local.md)」(Azure Functions Core Tools を使用して作業する) を参照してください。

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`AzureWebJobsStorage`]: functions-app-settings.md#azurewebjobsstorage

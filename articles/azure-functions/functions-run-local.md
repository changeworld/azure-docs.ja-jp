---
title: Azure Functions Core Tools の操作
description: Azure 関数を Azure Functions で実行する前に、ローカル コンピューターのコマンド プロンプトまたはターミナルでコーディングしてテストする方法について説明します。
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 10/05/2021
ms.custom: devx-track-csharp, 80e4ff38-5174-43
ms.openlocfilehash: fa804ee911d8b810de391916f7ae92ff46efe0b1
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129613284"
---
# <a name="work-with-azure-functions-core-tools"></a>Azure Functions Core Tools の操作

Azure Functions Core Tools を使用すると、ローカル コンピューター上のコマンド プロンプトまたはターミナルから関数を開発およびテストできます。 ローカル関数はライブ Azure サービスに接続できるため、完全な Functions ランタイムを使用してローカル コンピューター上で関数をデバッグすることができます。 Azure サブスクリプションに関数アプリをデプロイすることもできます。

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Core Tools を使用して、ローカル コンピューターで関数を開発し、Azure に発行する基本的な手順は次の通りです。

> [!div class="checklist"]
> * [Core Tools と依存関係をインストールします。](#v2)
> * [言語固有のテンプレートから関数アプリ プロジェクトを作成します。](#create-a-local-functions-project)
> * [トリガーとバインド拡張を登録します。](#register-extensions)
> * [ストレージとその他の接続を定義します。](#local-settings)
> * [トリガーおよび言語固有のテンプレートから関数を作成します。](#create-func)
> * [関数をローカルで実行します。](#start)
> * [Azure にプロジェクトを発行します。](#publish)

## <a name="prerequisites"></a>前提条件

現在、Azure Functions Core Tools では、[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/install-az-ps) に依存して Azure アカウントでの認証を行っています。 つまり、Azure Functions Core Tools から [Azure に発行](#publish)できるようにするには、このいずれかのツールをインストールする必要があります。 

## <a name="core-tools-versions"></a><a name="v2"></a>Core Tools のバージョン

Azure Functions Core Tools には、4 つのバージョンがあります。 使用するバージョンは、ローカル開発環境、[選択した言語](supported-languages.md)、必要なサポートのレベルによって異なります。

各特定のバージョンと詳細なインストール手順の詳細については、以下のバージョン タブを選択します。

# <a name="version-3x"></a>[バージョン 3.x](#tab/v3)

Azure Functions ランタイムの[バージョン 3.x](functions-versions.md) をサポートします。 このバージョンでは Windows、macOS、Linux がサポートされ、インストールにはプラットフォーム固有のパッケージ マネージャーまたは npm が使用されます。 これは、Functions ランタイムと Core Tools で推奨されるバージョンです。

# <a name="version-4x-preview"></a>[バージョン 4.x (プレビュー)](#tab/v4)

プレビュー段階にある Functions ランタイムの[バージョン 4.x](functions-versions.md) をサポートします。 このバージョンでは Windows、macOS、Linux がサポートされ、インストールにはプラットフォーム固有のパッケージ マネージャーまたは npm が使用されます。 .NET 6.0 で C# 関数を実行する必要がない限り、Functions ランタイムと Core Tools のバージョン 3.x の使用を検討してください。

# <a name="version-2x"></a>[バージョン 2.x](#tab/v2)

Azure Functions ランタイムの[バージョン 2.x](functions-versions.md) をサポートします。 このバージョンでは Windows、macOS、Linux がサポートされ、インストールにはプラットフォーム固有のパッケージ マネージャーまたは npm が使用されます。 

# <a name="version-1x"></a>[バージョン 1.x](#tab/v1) 

Azure Functions ランタイムのバージョン 1.x をサポートします。 ツールのこのバージョンは Windows コンピューター上でのみサポートされ、[npm パッケージ](https://www.npmjs.com/package/azure-functions-core-tools)からインストールされます。

---

特定のコンピューターには、1 つのバージョンの Core Tools のみをインストールできます。  特に記載がない限り、この記事の例ではバージョン 3.x を対象にしています。

## <a name="install-the-azure-functions-core-tools"></a>Azure Functions Core Tools のインストール

[Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools) には、Azure Functions ランタイムを実行するのと同じランタイムのバージョンが含まれており、ローカルの開発コンピューターで実行できます。 また、関数の作成、Azure への接続、関数プロジェクトのデプロイを行うコマンドも用意されています。

Core Tools はバージョン 2.x より、[Windows](?tabs=windows#v2)、[macOS](?tabs=macos#v2)、[Linux](?tabs=linux#v2) 上で実行されます。

# <a name="windows"></a>[Windows](#tab/windows/v4)

次の手順では、Windows インストーラー (MSI) を使用して Core Tools v4.x をインストールします。 その他のパッケージベースのインストーラーの詳細については、[Core Tools の Readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows) をご覧ください。

Windows のバージョンに応じて、 以下の Core Tools インストーラーをダウンロードして実行します。

- [v4.x - Windows 64 ビット](https://go.microsoft.com/fwlink/?linkid=2174087) (推奨。 [Visual Studio Code のデバッグ](functions-develop-vs-code.md#debugging-functions-locally) には 64 ビットが必要です)
- [v4.x - Windows 32 ビット](https://go.microsoft.com/fwlink/?linkid=2174159)

# <a name="windows"></a>[Windows](#tab/windows/v3)

次の手順では、Windows インストーラー (MSI) を使用して Core Tools v3.x をインストールします。 その他のパッケージベースのインストーラーの詳細については、[Core Tools の Readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows) をご覧ください。

Windows のバージョンに応じて、 以下の Core Tools インストーラーをダウンロードして実行します。

- [v3.x - Windows 64 ビット](https://go.microsoft.com/fwlink/?linkid=2135274) (推奨。 [Visual Studio Code のデバッグ](functions-develop-vs-code.md#debugging-functions-locally) には 64 ビットが必要です)
- [v3.x - Windows 32 ビット](https://go.microsoft.com/fwlink/?linkid=2135275)

# <a name="windows"></a>[Windows](#tab/windows/v2)

Core Tools のバージョン 2.x をインストールするには npm が必要です。 [Chocolatey を使用してパッケージをインストールする](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#azure-functions-core-tools)こともできます。

1. まだインストールしていない場合は、[npm が付属する Node.js をインストールします](https://nodejs.org/en/download/)。 

1. 次の npm コマンドを実行して、Core Tools パッケージをインストールします。

    ```
    npm install -g azure-functions-core-tools@2 --unsafe-perm true
    ```

# <a name="windows"></a>[Windows](#tab/windows/v1)

Core Tools のバージョン 1.x をインストールする必要がある場合は、[GitHub リポジトリ](https://github.com/Azure/azure-functions-core-tools/blob/v1.x/README.md#installing)で詳細を確認してください。

# <a name="macos"></a>[macOS](#tab/macos/v4)

次の手順では、Homebrew を使用して macOS 上に Core Tools をインストールします。

1. まだインストールしていない場合は、[Homebrew](https://brew.sh/) をインストールします。

1. 次のコマンドを使って、Core Tools のパッケージをインストールします。

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@4
    # if upgrading on a machine that has 2.x or 3.x installed:
    brew link --overwrite azure-functions-core-tools@4
    ```

# <a name="macos"></a>[macOS](#tab/macos/v3)

次の手順では、Homebrew を使用して macOS 上に Core Tools をインストールします。

1. まだインストールしていない場合は、[Homebrew](https://brew.sh/) をインストールします。

1. 次のコマンドを使って、Core Tools のパッケージをインストールします。

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed:
    brew link --overwrite azure-functions-core-tools@3
    ```

# <a name="macos"></a>[macOS](#tab/macos/v2)

次の手順では、Homebrew を使用して macOS 上に Core Tools をインストールします。

1. まだインストールしていない場合は、[Homebrew](https://brew.sh/) をインストールします。

1. 次のコマンドを使って、Core Tools のパッケージをインストールします。

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@2
    ```

# <a name="macos"></a>[macOS](#tab/macos/v1)

Core Tools のバージョン 1.x は、macOS ではサポートされていません。 macOS では、バージョン 2.x 以降のバージョンを使用します。

# <a name="linux"></a>[Linux](#tab/linux/v4)

[!INCLUDE [functions-core-tools-linux-install](../../includes/functions-core-tools-linux-install.md)]

5. 次のコマンドを使って、Core Tools のパッケージをインストールします。

    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-4
    ```

# <a name="linux"></a>[Linux](#tab/linux/v3)

[!INCLUDE [functions-core-tools-linux-install](../../includes/functions-core-tools-linux-install.md)]

5. 次のコマンドを使って、Core Tools のパッケージをインストールします。

    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-3
    ```

# <a name="linux"></a>[Linux](#tab/linux/v2)

[!INCLUDE [functions-core-tools-linux-install](../../includes/functions-core-tools-linux-install.md)]

5. 次のコマンドを使って、Core Tools のパッケージをインストールします。

    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-2
    ```

# <a name="linux"></a>[Linux](#tab/linux/v1)

Core Tools のバージョン 1.x は、Linux ではサポートされていません。 Linux ではバージョン 2.x 以降のバージョンを使用します。

---

## <a name="changing-core-tools-versions"></a>Core Tools のバージョンの変更

別のバージョンの Core Tools に変更する場合は、元のインストールと同じパッケージ マネージャーを使用して、別のパッケージ バージョンに移行する必要があります。 たとえば、npm を使用して Core Tools バージョン 2.x をインストールした場合は、次のコマンドを使用してバージョン 3.x にアップグレードする必要があります。

```bash
npm install -g azure-functions-core-tools@3 --unsafe-perm true
```

Windows で Windows インストーラー (MSI) を使用して Core Tools をインストールした場合は、別のバージョンをインストールする前に、[プログラムの追加と削除] から古いバージョンをアンインストールする必要があります。

## <a name="create-a-local-functions-project"></a>ローカル関数プロジェクトを作成する

Functions プロジェクト ディレクトリには、言語に関係なく、次のファイルとフォルダーが含まれます。 

| ファイル名 | 説明 |
| --- | --- |
| host.json | 詳細については、「[host.json のリファレンス](functions-host-json.md)」を参照してください。 |
| local.settings.json | アプリ設定など、ローカルで実行する場合に Core Tools によって使用される設定。 詳細については、[ローカル設定](#local-settings)に関するページを参照してください。 |
| .gitignore | local.settings.json ファイルが誤って Git リポジトリに発行されないようにします。 詳細については、[ローカル設定](#local-settings)に関するページを参照してください。|
| .vscode\extensions.json | Visual Studio Code でプロジェクト フォルダーを開く際に使用される設定ファイル。  |

Functions プロジェクト フォルダーの詳細については、「[Azure Functions の開発者向けガイド](functions-reference.md#folder-structure)」を参照してください。

ターミナル ウィンドウまたはコマンド プロンプトで、次のコマンドを実行してプロジェクトおよびローカルの Git リポジトリを作成します。

```
func init MyFunctionProj
```

この例では、新しい `MyFunctionProj` フォルダーに Functions プロジェクトが作成されます。 プロジェクトの既定の言語を選択するように求めるダイアログが表示されます。 

プロジェクトの初期化には、次の考慮事項が適用されます。

+ コマンドで `--worker-runtime` オプションを指定しない場合は、言語を選択するように求めるダイアログが表示されます。 詳しくは、「[func init リファレンス](functions-core-tools-reference.md#func-init)」を参照してください。

+ プロジェクト名を指定しないと、現在のフォルダーが初期化されます。 

+ プロジェクトをカスタム Linux コンテナーに発行する予定の場合は、`--dockerfile` オプションを使用して、プロジェクトに対して Dockerfile が生成されるようにします。 詳細については、「[カスタム イメージを使用して Linux で関数を作成する](functions-create-function-linux-custom-image.md)」をご覧ください。 

特定の言語には、追加の考慮事項がある場合があります。

# <a name="c"></a>[C\#](#tab/csharp)

+ 既定では、Core Tools のバージョン 2.x 以降のバージョンでは、.NET ランタイムの関数アプリ プロジェクトは [C# クラス プロジェクト](functions-dotnet-class-library.md) (.csproj) として作成されます。 バージョン 3.x では、[分離プロセスで .NET 5.0 で実行](dotnet-isolated-process-guide.md)される関数の作成もサポートされています。 Visual Studio または Visual Studio Code で使用できるこれらの C# プロジェクトは、デバッグ中および Azure への発行時にコンパイルされます。 

+ C# スクリプト (csx) ファイルを使用してローカルで作業する場合は、`--csx` パラメーターを使用します。 これらのファイルは、Azure portal で関数を作成するとき、およびバージョン 1.x の Core Tools を使用するときに取得するものと同じです。 詳細については、「[func init リファレンス](functions-core-tools-reference.md#func-init)」を参照してください。

# <a name="java"></a>[Java](#tab/java)

+ Java では、HTTP でトリガーされる最初の関数と共に、Maven アーキタイプを使用してローカル関数プロジェクトを作成します。 `func init` と `func new` を使用する代わりに、[コマンド ライン クイックスタート](./create-first-function-cli-java.md)の手順に従ってください。  

# <a name="javascript"></a>[JavaScript](#tab/node)

+ `node` の `--worker-runtime` 値を使用するには、`--language` を `javascript` として指定します。 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell に関する追加の考慮事項はありません。

# <a name="python"></a>[Python](#tab/python)

+ `func init` を含むすべてのコマンドを仮想環境内から実行する必要があります。 詳細については、「[仮想環境を作成してアクティブにする](create-first-function-cli-python.md#create-venv)」を参照してください。

# <a name="typescript"></a>[TypeScript](#tab/ts)

+ `node` の `--worker-runtime` 値を使用するには、`--language` を `javascript` として指定します。

+ TypeScript 特有の `func init` 動作については、[JavaScript 開発者リファレンスの TypeScript セクション](functions-reference-node.md#typescript)を参照してください。 

--- 

## <a name="register-extensions"></a>拡張機能を登録する

ランタイム バージョン 2.x 以降では、Functions のトリガーとバインディングは .NET 拡張機能 (NuGet) パッケージとして実装されます。 コンパイル済みの C# プロジェクトの場合は、使用している特定のトリガーとバインディングの NuGet 拡張機能パッケージを参照するだけです。 HTTP バインドとタイマー トリガーには、拡張機能は必要ありません。 

C# 以外のプロジェクトの開発エクスペリエンスを向上させるために、Functions では、host.json プロジェクト ファイルでバージョン付きの拡張バンドルを参照できます。 [拡張機能バンドル](functions-bindings-register.md#extension-bundles)を使用すると、すべての拡張機能をアプリで使用できるようになり、拡張機能間でパッケージの互換性の問題が発生する可能性を排除できます。 拡張機能バンドルでは、.NET Core 3.1 SDK をインストールする必要も、extensions.csproj ファイルを処理する必要もなくなります。

拡張機能バンドルは、C# でコンパイルされたプロジェクト以外の関数プロジェクトで推奨される方法です。 これらのプロジェクトでは、拡張機能バンドル設定が、初期化中に _host.json_ ファイルで生成されます。 これで問題がない場合は、このセクション全体をスキップできます。  

### <a name="use-extension-bundles"></a>拡張機能バンドルを使用する

[!INCLUDE [Register extensions](../../includes/functions-extension-bundles.md)]

 お使いの言語でサポートされている場合は、`func init` を呼び出した後、拡張機能バンドルは既に有効になっています。 function.json ファイルへのバインドを追加する前に、host.json に拡張機能のバンドルを追加する必要があります。 詳細については、「[Azure Functions バインド拡張機能を登録する](functions-bindings-register.md#extension-bundles)」を参照してください。 

### <a name="explicitly-install-extensions"></a>拡張機能を明示的にインストールする

.NET 以外のプロジェクトでは、バンドルに含まれていない拡張機能の特定のバージョンをターゲットにする必要がある場合など、拡張機能バンドルを使用できない場合があります。 このようなまれなケースでは、Core Tools を使用して、プロジェクトに必要な特定の拡張機能パッケージをローカルにインストールすることができます。 詳細については、「[拡張機能を明示的にインストールする](functions-bindings-register.md#explicitly-install-extensions)」を参照してください。

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

既定では、プロジェクトが Azure に発行されても、これらの設定は自動的に移行されません。 発行する際に [`--publish-local-settings` オプション][func azure functionapp publish] を使用し、これらの設定が Azure 内の関数アプリに追加されるようにしてください。 `ConnectionStrings` セクション内の値は発行されません。

関数アプリの設定値は、コードの中で環境変数として読み込むこともできます。 詳細については、以下の言語固有のリファレンス トピックの「環境変数」のセクションを参照してください。

* [C# プリコンパイル済み](functions-dotnet-class-library.md#environment-variables)
* [C# スクリプト (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

有効なストレージ接続文字列が [`AzureWebJobsStorage`] に設定されていなく、エミュレーターが使用されていない場合は、次のエラー メッセージが表示されます。

> local.settings.json に AzureWebJobsStorage の値がありません。 これは HTTP 以外のすべてのトリガーに必要です。 "func azure functionapp fetch-app-settings \<functionAppName\>" を実行するか、local.settings.json で接続文字列を指定することができます。

### <a name="get-your-storage-connection-strings"></a>ストレージ接続文字列の取得

開発のために Microsoft Azure ストレージ エミュレーターを使用している場合であっても、実際のストレージに接続してローカルで実行したい場合があります。 [ストレージ アカウントを作成済み](../storage/common/storage-account-create.md)である場合は、次のいずれかの方法で、有効なストレージ接続文字列を取得できます。

# <a name="portal"></a>[ポータル](#tab/portal)

1. [Azure portal] から、 **[ストレージ アカウント]** を検索して選択します。 

    ![Azure portal で [ストレージ アカウント] を選択する](./media/functions-run-local/select-storage-accounts.png)
  
1.  使用するストレージ アカウントを選択し、 **[設定]** の **[アクセス キー]** を選択してから、 **[接続文字列]** の値のいずれかをコピーします。

    ![Azure Portal から接続文字列をコピーする](./media/functions-run-local/copy-storage-connection-portal.png)

# <a name="core-tools"></a>[Core Tools](#tab/azurecli)

プロジェクトのルートから、次のいずれかのコマンドで Azure から接続文字列をダウンロードします。

  + 既存の関数アプリからすべての設定をダウンロードします。

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```

  + 特定のストレージ アカウントの接続文字列を取得します。

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Azure にまだサインインしていない場合は、それを求めるメッセージが表示されます。 これらのコマンドは、local.settings.json ファイル内のすべての既存の設定を上書きします。 詳細については、[`func azure functionapp fetch-app-settings`](functions-core-tools-reference.md#func-azure-functionapp-fetch-app-settings) および [`func azure storage fetch-connection-string`](functions-core-tools-reference.md#func-azure-storage-fetch-connection-string) コマンドを参照してください。

# <a name="storage-explorer"></a>[Storage Explorer](#tab/storageexplorer)

1. [Azure Storage Explorer](https://storageexplorer.com/) を実行します。 

1. **Explorer** で、お使いのサブスクリプション、**ストレージ アカウント** の順に展開します。

1. ストレージ アカウントを選択して、プライマリまたはセカンダリの接続文字列をコピーします。

    ![Storage Explorer から接続文字列をコピーする](./media/functions-run-local/storage-explorer.png)

---

## <a name="create-a-function"></a><a name="create-func"></a>関数を作成する

既存のプロジェクトで関数を作成するには、次のコマンドを実行します。

```
func new
```

バージョン 3.x または 2.x では、`func new` を実行すると、関数アプリの既定の言語でテンプレートを選択するように求められます。 次に、関数の名前を選択するように求められます。 バージョン 1.x では、さらに言語を選択するように求められます。 

`func new` コマンドで関数名とテンプレートを指定することもできます。 次の例では、`--template` オプションを使用して、`MyHttpTrigger` という名前の HTTP トリガーを作成します。

```
func new --template "Http Trigger" --name MyHttpTrigger
```

この例では、`MyQueueTrigger` という名前の Queue Storage トリガーを作成します。

```
func new --template "Queue Trigger" --name MyQueueTrigger
```

詳細については、[`func new` コマンド](functions-core-tools-reference.md#func-new)に関するセクションを参照してください。

## <a name="run-functions-locally"></a><a name="start"></a>関数をローカルで実行する

Functions プロジェクトを実行するには、プロジェクトのルート ディレクトリから Functions ホストを実行します。 ホストによって、プロジェクトのすべての関数に対するトリガーが有効になります。 [`start` コマンド](functions-core-tools-reference.md#func-start)は、使用するプロジェクトの言語によって異なります。

# <a name="c"></a>[C\#](#tab/csharp)

```
func start
```

# <a name="java"></a>[Java](#tab/java)

```
mvn clean package 
mvn azure-functions:run
```

# <a name="javascript"></a>[JavaScript](#tab/node)

```
func start
```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
このコマンドは[仮想環境で実行する](./create-first-function-cli-python.md)必要があります。

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> Functions ランタイムのバージョン 1.x では、代わりに `func host start` を使用する必要があります。 詳細については、「[Azure Functions Core Tools リファレンス](functions-core-tools-reference.md?tabs=v1#func-start)」を参照してください。 

Functions ホストの起動時に、次の例のような、HTTP によってトリガーされる関数の URL が出力されます。

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>ローカルで実行される場合、HTTP エンドポイントに対して承認は適用されません。 つまり、ローカルの HTTP 要求はすべて、`authLevel = "anonymous"` として処理されます。 詳細については、[HTTP バインディング](functions-bindings-http-webhook-trigger.md#authorization-keys)に関する記事を参照してください。

### <a name="passing-test-data-to-a-function"></a>関数へのテスト データの受け渡し

関数をローカルでテストするには、[Functions ホストを起動](#start)し、HTTP 要求を使用してローカル サーバーでエンドポイントを呼び出します。 呼び出すエンドポイントは、関数の種類によって異なります。

>[!NOTE]
> このトピックの例では、cURL ツールを使用してターミナルまたはコマンド プロンプトから HTTP 要求を送信します。 お好みのツールを使用して HTTP 要求をローカル サーバーに送信できます。 Linux ベースのシステムと Windows 10 ビルド 17063 以降では既定で cURL ツールを使用できます。 以前の Windows では、最初に [cURL ツール](https://curl.haxx.se/)をダウンロードし、インストールする必要があります。

関数のテストの全般的な情報については、「[Azure Functions のコードをテストするための戦略](functions-test-a-function.md)」を参照してください。

#### <a name="http-and-webhook-triggered-functions"></a>HTTP と webhook でトリガーされる関数

次のエンドポイントを呼び出して、HTTP と webhook でトリガーされる関数をローカルで実行できます。

```
http://localhost:{port}/api/{function_name}
```

Functions ホストがリッスンしているのと同じサーバー名とポートを使用していることを確認してください。 これは、Functions ホストの起動時に生成される出力で確認できます。 トリガーでサポートされている任意の HTTP メソッドを使用して、この URL を呼び出すことができます。

次の cURL コマンドは、`MyHttpTrigger` クイックスタート関数を、クエリ文字列で渡された _name_ パラメーターを使用して、GET 要求からトリガーします。

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

次の例は、要求本文で _name_ を渡す POST 要求から呼び出される同じ関数です。

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

ブラウザーから GET 要求を行ってクエリ文字列でデータを渡すことが可能です。 その他すべての HTTP メソッドについては、cURL、Fiddler、Postman、または POST 要求をサポートする類似の HTTP テスト ツールを使用する必要があります。

#### <a name="non-http-triggered-functions"></a>HTTP でトリガーされない関数

HTTP トリガーと Event Grid トリガーを除く、あらゆる関数で、"_管理エンドポイント_" と呼ばれる特別なエンドポイントを呼び出すことによって、REST を使用して関数をローカルでテストできます。 HTTP POST 要求を使ってローカル サーバーでこのエンドポイントを呼び出すと、関数がトリガーされます。 

Event Grid でトリガーされた関数をローカルでテストする方法については、「[ビューアー Web アプリでのローカル テスト](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app)」を参照してください。

必要に応じて、POST 要求の本体でテスト データを実行に渡すことができます。 この機能は、Azure Portal の **[テスト]** タブに似ています。

次の管理者エンドポイントを呼び出して、非 HTTP 関数をトリガーします。

```
http://localhost:{port}/admin/functions/{function_name}
```

テスト データを関数の管理者エンドポイントに渡すには、そのデータを POST 要求メッセージの本文で提供する必要があります。 メッセージ本文は、次の JSON 形式にする必要があります。

```JSON
{
    "input": "<trigger_input>"
}
```

`<trigger_input>` 値には、関数が必要とする形式でデータが含まれています。 次の cURL の例は、`QueueTriggerJS` 関数に対する POST です。 この場合、入力は、キューにあることが期待されるメッセージに相当する文字列です。

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

Azure の関数アプリで管理者エンドポイントを呼び出す場合は、アクセス キーを指定する必要があります。 詳細については、「[関数のアクセス キー](functions-bindings-http-webhook-trigger.md#authorization-keys)」を参照してください。

## <a name="publish-to-azure"></a><a name="publish"></a>Azure に発行する

Azure Functions Core Tools は、次の 3 種類のデプロイをサポートしています。

| 展開の種類 | command | 説明 |
| ----- | ----- | ----- |
| プロジェクト ファイル | [`func azure functionapp publish`](functions-core-tools-reference.md#func-azure-functionapp-publish) | [zip デプロイ](functions-deployment-technologies.md#zip-deploy)を使用し、関数プロジェクト ファイルを関数アプリに直接デプロイします。 |
| カスタム コンテナー | `func deploy` | プロジェクトをカスタム Docker コンテナーとして Linux 関数アプリにデプロイします。  |
| Kubernetes クラスター | `func kubernetes deploy` | Linux 関数アプリを、カスタマー Docker コンテナーとして Kubernetes クラスターにデプロイします。 | 

### <a name="before-you-publish"></a>発行前 

>[!IMPORTANT]
>Core Tools から Azure に発行できるようにするには、[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/install-az-ps) がローカルにインストールされている必要があります。  

プロジェクト フォルダーには、発行するべきではない言語固有のファイルやディレクトリが含まれている場合があります。 除外された項目は、ルート プロジェクト フォルダーの .funcignore ファイルにリストされます。  

コードをデプロイする [Azure サブスクリプションで関数アプリを作成しておく](functions-cli-samples.md#create)必要があります。 コンパイルを必要とするプロジェクトは、バイナリをデプロイできるように、ビルドする必要があります。

Azure CLI または Azure PowerShell を使用してコマンド プロンプトまたはターミナル ウィンドウから関数アプリを作成する方法については、「[サーバーレス実行用の Function App を作成する](./scripts/functions-cli-create-serverless.md)」を参照してください。 

>[!IMPORTANT]
> Azure portal で関数アプリを作成すると、既定でバージョン 3.x の Function ランタイムが使用されます。 関数アプリにバージョン 1.x のランタイムを使用させるには、[バージョン 1.x での実行](functions-versions.md#creating-1x-apps)に関するページの説明に従ってください。
> 既存の関数がある関数アプリのランタイム バージョンを変更することはできません。


### <a name="deploy-project-files"></a><a name="project-file-deployment"></a>プロジェクト ファイルのデプロイ

Azure で ローカル コードを関数アプリに発行するには、`publish` コマンドを使用します。

```
func azure functionapp publish <FunctionAppName>
```

このデプロイには、次の考慮事項が適用されます。

+ 発行すると、関数アプリ内の既存のファイルが上書きされます。

+ [`--publish-local-settings` オプション][func azure functionapp publish]を使用して、local.settings.json ファイルの値に基づいて、関数アプリにアプリ設定を自動的に作成します。  

+ コンパイルされたプロジェクトで[リモート ビルド](functions-deployment-technologies.md#remote-build)が実行されます。 これは、[`--no-build` オプション][func azure functionapp publish]を使用して制御できます。  

+ プロジェクトがデプロイされ、[デプロイ パッケージから実行されます](run-functions-from-deployment-package.md)。 この推奨されるデプロイ モードを無効にするには、[`--nozip` オプション][func azure functionapp publish]を使用します。

+ Java では、Maven を使用して、ローカル プロジェクトが Azure に発行されます。 代わりに、コマンド `mvn azure-functions:deploy` を実行して、Azure に発行します。 Azure リソースは、初期デプロイ中に作成されます。

+ サブスクリプションに存在しない `<FunctionAppName>` に発行しようとすると、エラーが表示されます。 

### <a name="kubernetes-cluster"></a>Kubernetes クラスター

Functions では、Docker コンテナーで実行するように Functions プロジェクトを定義することもできます。 `func init` の [`--docker` オプション][func init]を使用して、特定の言語の Dockerfile を生成します。 このファイルはその後、デプロイするコンテナーを作成するときに使用されます。 

Core Tools を使用すると、プロジェクトをカスタム コンテナー イメージとして Kubernetes クラスターにデプロイできます。 使用するコマンドは、クラスターで使用されるスケーラーの種類によって異なります。  

次のコマンドは、Dockerfile を使用してコンテナーを生成し、それを Kubernetes クラスターにデプロイします。 

# <a name="keda"></a>[KEDA](#tab/keda)

```command
func kubernetes deploy --name <DEPLOYMENT_NAME> --registry <REGISTRY_USERNAME> 
```

詳細については、「[Kubernetes への関数アプリのデプロイ](functions-kubernetes-keda.md#deploying-a-function-app-to-kubernetes)」を参照してください。 

# <a name="defaultknative"></a>[デフォルト/KNative](#tab/default)

```command
func deploy --name <FUNCTION_APP> --platform kubernetes --registry <REGISTRY_USERNAME> 
```

上記の例で、`<FUNCTION_APP>` を Azure の関数アプリの名前に置き換え、`<REGISTRY_USERNAME>` を Docker ユーザー名などのレジストリ アカウント名に置き換えます。 コンテナーはローカルでビルドされ、`<FUNCTION_APP>` に基づくイメージ名を使用して Docker レジストリ アカウントにプッシュされます。 Docker コマンド ライン ツールがインストールされている必要があります。

詳細については、[`func deploy` コマンド](functions-core-tools-reference.md#func-deploy)に関するセクションを参照してください。

---

Kubernetes を使用せずにカスタム コンテナーを Azure に発行する方法については、「[カスタム コンテナーを使用して Linux で関数を作成する](functions-create-function-linux-custom-image.md)」を参照してください。

## <a name="monitoring-functions"></a>関数の監視

関数の実行を監視するための推奨される方法は、Azure Application Insights との統合です。 また、ローカル コンピューターに実行ログをストリーミングすることもできます。 詳細については、「[Azure Functions を監視する](functions-monitoring.md)」を参照してください。

### <a name="application-insights-integration"></a>Application Insights の統合

Azure で関数アプリを作成するときに Application Insights の統合を有効にする必要があります。 何らかの理由で関数アプリが Application Insights インスタンスに接続されていない場合は、Azure portal でこの統合を簡単に行うことができます。 詳細については、「[Application Insights との統合を有効にする](configure-monitoring.md#enable-application-insights-integration)」を参照してください。

### <a name="enable-streaming-logs"></a>ストリーミング ログを有効にする

関数によって生成されているログ ファイルのストリームは、ローカル コンピューター上のコマンド ライン セッションで表示できます。 

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

この種類のストリーミング ログを使用するには、関数アプリの Application Insights との統合を有効にする必要があります。   


## <a name="next-steps"></a>次のステップ

Azure Functions Core Tools [Microsoft 学習モジュール](/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) を使用して Azure Functions を開発、テスト、および発行する方法について説明します。Azure Functions Core Tools は[オープンソースであり、GitHub ](https://github.com/azure/azure-functions-cli)でホストされています。  
バグまたは機能要求を提出するには、[GitHub の問題をオープン](https://github.com/azure/azure-functions-cli/issues)してください。

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[`AzureWebJobsStorage`]: functions-app-settings.md#azurewebjobsstorage
[extension bundles]: functions-bindings-register.md#extension-bundles
[func azure functionapp publish]: functions-core-tools-reference.md?tabs=v2#func-azure-functionapp-publish
[func init]: functions-core-tools-reference.md?tabs=v2#func-init

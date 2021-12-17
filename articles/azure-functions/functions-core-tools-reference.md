---
title: Azure Functions Core Tools リファレンス
description: Azure Functions Core Tools (func.exe) をサポートするリファレンス ドキュメント。
ms.topic: reference
ms.date: 07/13/2021
ms.openlocfilehash: 90d4a9575c2fe6bb2dc4fbd18132e3bc21e4a07c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781357"
---
# <a name="azure-functions-core-tools-reference"></a>Azure Functions Core Tools リファレンス

この記事は、Azure Functions Core Tools のリファレンス ドキュメントです。このドキュメントを使用して、お使いのローカル コンピューターから、Azure Functions プロジェクトを開発、管理、デプロイすることができます。 Core Tools の使用の詳細については、「[Azure Functions Core Tools の操作](functions-run-local.md)」を参照してください。 

Core Tools コマンドは次のコンテキストに分類され、それぞれが独自のアクション セットを提供します。

| コマンドのコンテキスト | 説明 |
| ----- | ----- |
| [`func`](#func-init) | お使いのローカル コンピューター上での関数の作成と実行に使用されるコマンド。 |
| [`func azure`](#func-azure-functionapp-fetch-app-settings) | 発行など、Azure リソースを操作するためのコマンド。 |
| [`func durable`](#func-durable-delete-task-hub)    | [Durable Functions](./durable/durable-functions-overview.md) を操作するためのコマンド。 |
| [`func extensions`](#func-extensions-install) | 拡張機能をインストールおよび管理するためのコマンド。 |
| [`func kubernetes`](#func-kubernetes-deploy) | Kubernetes と Azure Functions を操作するためのコマンド。 |
| [`func settings`](#func-settings-decrypt)   | ローカル関数ホストの環境設定を管理するためのコマンド。 |
| [`func templates`](#func-templates-list)  | 使用可能な関数テンプレートを一覧表示するためのコマンド。 |

この記事のコマンドを使用する前に、[Core Tools をインストール](functions-run-local.md#install-the-azure-functions-core-tools)する必要があります。 

## <a name="func-init"></a>func init 

特定の言語で新しい Functions プロジェクトを作成します。

```command
func init <PROJECT_FOLDER>
```

`<PROJECT_FOLDER>` を指定すると、プロジェクトは、この名前の新しいフォルダー内に作成されます。 それ以外の場合は、現在のフォルダーが使用されます。

`func init` では、次のオプションがサポートされています。特に注意書きがない限り、バージョン 3.x/2.x だけが対象です。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--csx`** | .NET 関数を C# スクリプトとして作成します。これはバージョン 1.x の動作です。 `--worker-runtime dotnet` でのみ有効です。 |
| **`--docker`** | 選択した `--worker-runtime` に基づく基本イメージを使用して、コンテナーの Dockerfile を作成します。 カスタム Linux コンテナーに発行する場合は、このオプションを使用します。 |
| **`--docker-only`** |  既存のプロジェクトに Dockerfile を追加します。 local.settings.json で設定されていないか指定されていない場合、worker ランタイムのプロンプトを表示します。 既存のプロジェクトをカスタム Linux コンテナーに発行する場合は、このオプションを使用します。 |
| **`--force`** | プロジェクトに既存のファイルがある場合でも、プロジェクトを初期化します。 この設定は、同じ名前の既存のファイルを上書きします。 プロジェクト フォルダー内の他のファイルには影響ありません。 |
| **`--language`** | 言語固有のプロジェクトを初期化します。 現時点では、`--worker-runtime` が `node` に設定されている場合にサポートされます。 オプションは `typescript` と `javascript` です。 `--worker-runtime javascript` または `--worker-runtime typescript` を使用することもできます。  |
| **`--managed-dependencies`**  | マネージドの依存関係をインストールします。 現時点では、この機能は PowerShell worker ランタイムのみでサポートされています。 |
| **`--source-control`** | Git リポジトリを作成するかどうかを制御します。 既定では、リポジトリは作成されません。 `true` を指定すると、リポジトリが作成されます。 |
| **`--worker-runtime`** | プロジェクトの言語ランタイムを設定します。 サポートされる値は、`csharp`、`dotnet`、`dotnet-isolated`、`javascript`、`node` (JavaScript)、`powershell`、`python`、`typescript` です。 Java の場合は、[Maven](functions-reference-java.md#create-java-functions) を使用します。 プロジェクト ファイルだけが含まれる言語に依存しないプロジェクトを生成するには、`custom` を使用します。 設定しないと、初期化中にランタイムの選択を求められます。 |
|

> [!NOTE]
> `--docker` または `--dockerfile` のいずれかのオプションを使用すると、Core Tools によって、C#、JavaScript、Python、PowerShell の各関数の Dockerfile が自動的に作成されます。 Java 関数の場合は、手動で Dockerfile を作成する必要があります。 Azure Functions の[イメージ リスト](https://github.com/Azure/azure-functions-docker)を使用して、Azure Functions が実行されているご自身のコンテナーに適した基本イメージを見つけてください。

## <a name="func-logs"></a>func logs

Kubernetes クラスター内で実行されている関数のログを取得します。

```
func logs --platform kubernetes --name <APP_NAME>
``` 

`func logs` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------------------------------------ | -------------------------------------- |
| **`--platform`** | 関数アプリのホスティング プラットフォーム。 サポートされているオプション: `kubernetes`。 |
| **`--name`** | Azure 内での関数アプリの名前。 |

詳細については、「[KEDA を使用した Kubernetes での Azure Functions](functions-kubernetes-keda.md)」を参照してください。

## <a name="func-new"></a>func new

テンプレートに基づいて、現在のプロジェクト内に新しい関数を作成します。

```
func new
``` 

`func new` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------------------------------------ | -------------------------------------- |
| **`--authLevel`** | HTTP トリガーの承認レベルを設定できます。 サポートされる値は `function`、`anonymous`、`admin` です。 ローカルで実行される場合、承認は適用されません。 詳細については、[HTTP バインディング](functions-bindings-http-webhook-trigger.md#authorization-keys)に関する記事を参照してください。 |
| **`--csx`** | (バージョン 2.x 以降のバージョン。)バージョン 1.x およびポータルで使用されるものと同じ C# スクリプト (.csx) テンプレートを生成します。 |
| **`--language`**, **`-l`**| テンプレート プログラミング言語。C#、F#、JavaScript など。 このオプションは、バージョン 1.x で必須です。 バージョン2.x 以降のバージョンでは、言語はワーカー ランタイムによって定義されているため、このオプションは使用しません。 |
| **`--name`**, **`-n`** | 関数名。 |
| **`--template`**, **`-t`** | サポートされている各言語で使用可能なテンプレートの完全な一覧を表示するには、`func templates list` コマンドを使います。   |

詳細については、「[関数を作成する](functions-run-local.md#create-func)」を参照してください。

## <a name="func-run"></a>func run

*バージョン 1.x のみ。*

関数を直接呼び出せるようにします。Azure portal 内の **[テスト]** タブを使用して関数を実行するのと似ています。 このアクションは、バージョン1.x でのみサポートされています。 これ以降のバージョンでは、`func start` を使用し、[関数エンドポイントを直接呼び出し](functions-run-local.md#passing-test-data-to-a-function)ます。

```command
func run
```

`func run` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--content`** | 関数に渡されるインライン コンテンツ。 |
| **`--debug`** | 関数を実行する前に、デバッガーを、ホスト プロセスにアタッチします。|
| **`--file`** | コンテンツとして使用するファイル名。|
| **`--no-interactive`** | 入力のためのダイアログを表示しません。自動化のシナリオに便利です。|
| **`--timeout`** | Functions ホストの準備が完了するまでの待機時間 (秒単位)。|

たとえば、HTTP によってトリガーされる関数を呼び出して、コンテンツ本文を渡すには、次のコマンドを実行します。

```
func run MyHttpTrigger --content '{\"name\": \"Azure\"}'
```

## <a name="func-start"></a>func start

ローカル ランタイム ホストを起動し、現在のフォルダーに関数プロジェクトを読み込みます。 

具体的なコマンドは、[ランタイム バージョン](functions-versions.md)によって異なります。   

# <a name="v2x"></a>[v2.x+](#tab/v2)

```command
func start
```

`func start` では、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--cert`** | 秘密キーが含まれる .pfx ファイルへのパス。 `--useHttps` でのみサポートされます。 |
| **`--cors`** | CORS オリジンのコンマ区切りのリスト (スペースなし)。 |
| **`--cors-credentials`** | Cookie と Authentication ヘッダーを使用して、クロスオリジン認証済み要求を許可します。 |
| **`--dotnet-isolated-debug`** | `true` に設定されている場合、デバッグ対象の .NET 分離プロジェクトからデバッガーがアタッチされるまで、.NET ワーカー プロセスを一時停止します。 |
| **`--enable-json-output`** | 可能な場合は、コンソール ログを JSON として出力します。 |
| **`--enableAuth`** | 完全な認証処理パイプラインを有効にします。 |
| **`--functions`** | 読み込まれる関数のスペース区切りリスト。 |
| **`--language-worker`** | 言語ワーカーを構成するための引数。 たとえば、[デバッグ ポートとその他の必要な引数](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers)を指定して、言語ワーカーのデバッグを有効にすることができます。 |
| **`--no-build`** | 実行前に現在のプロジェクトをビルドしません。 .NET クラス プロジェクトのみ。 既定値は、`false` です。  |
| **`--password`** | .pfx ファイルのパスワードまたはパスワードが格納されているファイルのいずれか。 `--cert` でのみ使用されます。 |
| **`--port`** | ローカル ポート。このポートでリッスンします。 既定値:7071。 |
| **`--timeout`** | Functions ホスト開始のタイムアウト (秒単位)。 既定値は20 秒。|
| **`--useHttps`** | `http://localhost:{port}` ではなく `https://localhost:{port}` にバインドします。 既定では、このオプションにより、信頼された証明書がコンピューターに作成されます。|

プロジェクトが実行中の場合に、[個々の関数エンドポイントを確認](functions-run-local.md#passing-test-data-to-a-function)できます。

# <a name="v1x"></a>[v1.x](#tab/v1)

```command
func host start
```

`func start` では、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--cors`** | CORS オリジンのコンマ区切りのリスト (スペースなし)。 |
| **`--port`** | ローカル ポート。このポートでリッスンします。 既定値:7071。 |
| **`--pause-on-error`** | プロセスを終了する前に、さらに入力できるように一時停止します。 統合開発環境 (IDE) から Core Tools を起動した場合にのみ使用されます。|
| **`--script-root`** | 実行または展開される関数アプリのルートへのパスを指定するために使用されます。 これは、サブフォルダーにプロジェクト ファイルを生成するコンパイル済みプロジェクトに使用されます。 たとえば、C# クラス ライブラリ プロジェクトをビルドすると、host.json、local.settings.json、および function.json ファイルが、`MyProject/bin/Debug/netstandard2.0` のようなパスの "*ルート*" サブフォルダーに生成されます。 この場合は、プレフィックスを `--script-root MyProject/bin/Debug/netstandard2.0` と設定します。 これは、Azure で実行する場合の関数アプリのルートです。 |
| **`--timeout`** | Functions ホスト開始のタイムアウト (秒単位)。 既定値は20 秒。|
| **`--useHttps`** | `http://localhost:{port}` ではなく `https://localhost:{port}` にバインドします。 既定では、このオプションにより、信頼された証明書がコンピューターに作成されます。|

バージョン 1.x では、[`func run` コマンド](#func-run)を使って特定の関数を実行し、テスト データを渡すこともできます。 

---

## <a name="func-azure-functionapp-fetch-app-settings"></a>func azure functionapp fetch-app-settings

特定の関数アプリから設定を取得します。

```command
func azure functionapp fetch-app-settings <APP_NAME>
```

例については、「[ストレージ接続文字列の取得](functions-run-local.md#get-your-storage-connection-strings)」を参照してください。

設定は、プロジェクトの local.settings.js ファイルにダウンロードされます。 画面上の値は、セキュリティのためにマスクされます。 local.settings.json ファイル内の設定を保護するには、[ローカル暗号化を有効](#func-settings-encrypt)にします。 

## <a name="func-azure-functionapp-list-functions"></a>func azure functionapp list-functions

指定された関数アプリ内の関数のリストを返します。

```command
func azure functionapp list-functions <APP_NAME>
```
## <a name="func-azure-functionapp-logstream"></a>func azure functionapp logstream

ローカル コマンド プロンプトを、Azure 内の関数アプリのストリーミング ログに接続します。

```command
func azure functionapp logstream <APP_NAME>
```

接続の既定のタイムアウトは 2 時間です。 タイムアウトを変更するには、[SCM_LOGSTREAM_TIMEOUT](functions-app-settings.md#scm_logstream_timeout) という名前のアプリ設定を追加し、タイムアウトの値を秒単位で指定します。 従量課金プランの Linux アプリではまだサポートされていません。 これらのアプリについては、`--browser` オプションを使用して、ポータル内のログを表示します。

`deploy` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--browser`** | 既定のブラウザー内で、関数アプリの Azure Application Insights Live Stream を開きます。 |

詳細については、「[ストリーミング ログを有効にする](functions-run-local.md#enable-streaming-logs)」を参照してください。

## <a name="func-azure-functionapp-publish"></a>func azure functionapp publish 

Azure 内の既存の function app リソースに Functions プロジェクトをデプロイします。 

```command
func azure functionapp publish <FunctionAppName>
```

詳細については、「[プロジェクト ファイルのデプロイ](functions-run-local.md#project-file-deployment)」を参照してください。

次の発行オプションは、バージョンに基づいて適用されます。

# <a name="v2x"></a>[v2.x+](#tab/v2)

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--additional-packages`** | ネイティブの依存関係を構築するときにインストールするパッケージの一覧 (例: `python3-dev libevent-dev`)。 |
| **`--build`**, **`-b`** | Linux 関数アプリにデプロイするときにビルド アクションを実行します。 `remote` および `local` を受け入れます。 |
| **`--build-native-deps`** | Python 関数アプリを発行するときに、`.wheels` フォルダーの生成をスキップします。 |
| **`--csx`** | C# スクリプト (.csx) プロジェクトを発行します。 |
| **`--force`** | 特定のシナリオで発行前の検証を無視します。 |
| **`--dotnet-cli-params`** | コンパイル済み C# (.csproj) 関数を発行するとき、Core Tools によって `dotnet build --output bin/publish` が呼び出されます。 これに渡されるすべてのパラメーターは、コマンド ラインに追加されます。 |
|**`--list-ignored-files`** | 発行時に無視されるファイルの一覧を表示します。これは、`.funcignore` ファイルに基づきます。 |
| **`--list-included-files`** | 発行されるファイルの一覧を表示します。これは、`.funcignore` ファイルに基づきます。 |
| **`--no-build`** | プロジェクトは発行中にはビルドされません。 Python の場合、`pip install` は実行されません。 |
| **`--nozip`** | 既定の `Run-From-Package` モードをオフにします。 |
| **`--overwrite-settings -y`** | `--publish-local-settings -i` を使用するときに、アプリの設定を上書きするプロンプトを抑制します。|
| **`--publish-local-settings -i`** |  local.settings.json の設定を Azure に発行し、設定が既に存在する場合は上書きを促します。 Microsoft Azure ストレージ エミュレーターを使用している場合は、まず、アプリ設定を[実際のストレージ接続](functions-run-local.md#get-your-storage-connection-strings)に変更します。 |
| **`--publish-settings-only`**, **`-o`** |  設定のみを発行し、コンテンツをスキップします。 既定値は prompt です。 |
| **`--slot`** | 発行先の特定のスロットの名前 (省略可能)。 |

# <a name="v1x"></a>[v1.x](#tab/v1)

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--overwrite-settings -y`** | `--publish-local-settings -i` を使用するときに、アプリの設定を上書きするプロンプトを抑制します。|
| **`--publish-local-settings -i`** |  local.settings.json の設定を Azure に発行し、設定が既に存在する場合は上書きを促します。 Microsoft Azure ストレージ エミュレーターを使用している場合は、まず、アプリ設定を[実際のストレージ接続](functions-run-local.md#get-your-storage-connection-strings)に変更します。 |

---

## <a name="func-azure-storage-fetch-connection-string"></a>func azure storage fetch-connection-string    

指定された Azure Storage アカウントの接続文字列を取得します。

```command
func azure storage fetch-connection-string <STORAGE_ACCOUNT_NAME>
```

## <a name="func-deploy"></a>func deploy

カスタム Linux コンテナー内の関数アプリを、KEDA を使用せずに Kubernetes クラスターにデプロイします。

```command
func deploy --name <FUNCTION_APP> --platform kubernetes --registry <DOCKER_USER>
```

このコマンドは、ご自身のプロジェクトをカスタム コンテナーとしてビルドし、既定のスケーラーまたは KNative を使用して Kubernetes クラスターに発行します。 動的スケールのために KEDA を使用してクラスターに発行するには、代わりに [`func kubernetes deploy` コマンド](#func-kubernetes-deploy)を使用します。 カスタム コンテナーには、Dockerfile が必要です。 Dockerfile でアプリを作成するには、[`func init` コマンド](#func-init) で `--dockerfile` オプションを使用します。     

`deploy` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--config`** | オプションのデプロイ構成ファイルを設定します。 |
| **`--max`**  | 必要に応じて、デプロイする関数アプリ インスタンスの最大数を設定します。 |
| **`--min`**  | 必要に応じて、デプロイする関数アプリ インスタンスの最小数を設定します。 |
| **`--name`** | 関数アプリ名 (必須)。 |
| **`--platform`** | 関数アプリのホスティング プラットフォーム (必須)。 有効なオプションは `kubernetes` と `knative` です。|
| **`--registry`** | 現在のユーザーがサインインしている Docker レジストリの名前。 |

Core Tools はローカルの Docker CLI を使って、イメージをビルドおよび発行します。 

ご自身の Docker が既にローカルにインストールされていることを確認してください。 ご自身のアカウントに接続するには、`docker login` コマンドを実行します。

## <a name="func-durable-delete-task-hub"></a>func durable delete-task-hub

Durable Functions タスク ハブ内のすべてのストレージ成果物を削除します。

```command
func durable delete-task-hub
```

`delete-task-hub` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 使用するストレージ接続文字列を含む設定の名前 (省略可能)。 |
| **`--task-hub-name`** |             使用する持続的なタスク ハブの名前 (省略可能)。 |

詳細については、[Durable Functions のドキュメント](./durable/durable-functions-instance-management.md#delete-a-task-hub)をご覧ください。

## <a name="func-durable-get-history"></a>func durable get-history

指定されたオーケストレーション インスタンスの履歴を返します。

```command
func durable get-history --id <INSTANCE_ID>
```

`get-history` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--id`** | オーケストレーション インスタンスの ID を指定します (必須)。 |
| **`--connection-string-setting`** | 使用するストレージ接続文字列を含む設定の名前 (省略可能)。 |
| **`--task-hub-name`** |             使用する持続的なタスク ハブの名前 (省略可能)。 |

詳細については、[Durable Functions のドキュメント](./durable/durable-functions-instance-management.md#azure-functions-core-tools-1)をご覧ください。

## <a name="func-durable-get-instances"></a>func durable get-instances

すべてのオーケストレーションインスタンスの状態を返します。 `top` パラメーターを使用したページングをサポートします。

```command
func durable get-instances
```

`get-instances` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--continuation-token`** | 返される要求の特定のページ/セクションを示すトークン (省略可能)。 |
| **`--connection-string-setting`** | 使用するストレージ接続文字列を含むアプリ設定の名前 (省略可能)。 |
| **`--created-after`** | 必要に応じて、この日付/時刻 (UTC) の後に作成されたインスタンスを取得します。 すべての ISO 8601 形式の日時が受け入れられます。 |
| **`--created-before`** | 必要に応じて、特定の日付/時刻 (UTC) の前に作成されたインスタンスを取得します。 すべての ISO 8601 形式の日時が受け入れられます。 |
| **`--runtime-status`** | 必要に応じて、`running`、`completed`、`failed` など、特定の状態に一致する状態を持つインスタンスを取得します。 1 つ以上のスペース区切りの状態を指定できます。 |
| **`--top`** | 必要に応じて、特定の要求内で返されるレコードの数を制限します。 |
| **`--task-hub-name`** | 使用する Durable Functions タスク ハブの名前 (省略可能)。 |

詳細については、[Durable Functions のドキュメント](./durable/durable-functions-instance-management.md#azure-functions-core-tools-2)をご覧ください。

## <a name="func-durable-get-runtime-status"></a>func durable get-runtime-status  

指定されたオーケストレーション インスタンスの状態を返します。

```command
func durable get-runtime-status --id <INSTANCE_ID>
```

`get-runtime-status` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 使用するストレージ接続文字列を含む設定の名前 (省略可能)。 |
| **`--id`** | オーケストレーション インスタンスの ID を指定します (必須)。 |
| **`--show-input`** | 設定されている場合、応答に関数の入力が含まれます。 |
| **`--show-output`** | 設定されている場合、応答に実行履歴が含まれます。 |
| **`--task-hub-name`** | 使用する Durable Functions タスク ハブの名前 (省略可能)。 |

詳細については、[Durable Functions のドキュメント](./durable/durable-functions-instance-management.md#azure-functions-core-tools-1)をご覧ください。

## <a name="func-durable-purge-history"></a>func durable purge-history

指定されたしきい値よりも前のオーケストレーションのオーケストレーション インスタンスの状態、履歴、BLOB ストレージを消去します。

```command
func durable purge-history
```

`purge-history` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 使用するストレージ接続文字列を含む設定の名前 (省略可能)。 |
| **`--created-after`** | 必要に応じて、この日付/時刻 (UTC) より後に作成されたインスタンスの履歴を削除します。 ISO 8601 形式のすべての datetime 値が受け入れられます。 |
| **`--created-before`** | 必要に応じて、この日付/時刻 (UTC) より前に作成されたインスタンスの履歴を削除します。 ISO 8601 形式のすべての datetime 値が受け入れられます。|
| **`--runtime-status`** | 必要に応じて、`completd`、`terminated`、`canceled`、`failed` など、特定の状態に一致する状態を持つインスタンスの履歴を削除します。 1 つ以上のスペース区切りの状態を指定できます。 `--runtime-status` を含めない場合、インスタンスの履歴は状態に関係なく削除されます。|
| **`--task-hub-name`** | 使用する Durable Functions タスク ハブの名前 (省略可能)。 |

詳細については、[Durable Functions のドキュメント](./durable/durable-functions-instance-management.md#azure-functions-core-tools-7)をご覧ください。

## <a name="func-durable-raise-event"></a>func durable raise-event         

指定したオーケストレーション インスタンスにイベントを発生させます。

```command
func durable raise-event --event-name <EVENT_NAME> --event-data <DATA>
```

`raise-event` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 使用するストレージ接続文字列を含む設定の名前 (省略可能)。 |
| **`--event-data`** | インラインで、または JSON ファイルからイベントに渡すデータ (必須)。 ファイルの場合は、アンパサンド (`@`) を使用してファイルへのパスにプレフィックスを付けます (例: `@path/to/file.json`)。 |
| **`--event-name`** | 発生させるイベントの名前 (必須)。
| **`--id`** | オーケストレーション インスタンスの ID を指定します (必須)。 |
| **`--task-hub-name`** | 使用する Durable Functions タスク ハブの名前 (省略可能)。 |

詳細については、[Durable Functions のドキュメント](./durable/durable-functions-instance-management.md#azure-functions-core-tools-5)をご覧ください。

## <a name="func-durable-rewind"></a>func durable rewind              

指定されたオーケストレーション インスタンスを巻き戻します。

```command
func durable rewind --id <INSTANCE_ID> --reason <REASON>
```

`rewind` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 使用するストレージ接続文字列を含む設定の名前 (省略可能)。 |
| **`--id`** | オーケストレーション インスタンスの ID を指定します (必須)。 |
| **`--reason`** | オーケストレーション を巻き戻す理由 (必須)。|
| **`--task-hub-name`** | 使用する Durable Functions タスク ハブの名前 (省略可能)。 |

詳細については、[Durable Functions のドキュメント](./durable/durable-functions-instance-management.md#azure-functions-core-tools-6)をご覧ください。

## <a name="func-durable-start-new"></a>func durable start-new

指定されたオーケストレーター関数の新しいインスタンスを開始します。

```command
func durable start-new --id <INSTANCE_ID> --function-name <FUNCTION_NAME> --input <INPUT>
```

`start-new` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 使用するストレージ接続文字列を含む設定の名前 (省略可能)。 |
| **`--function-name`** | 開始するオーケストレーター関数の名前 (必須)。|
| **`--id`** | オーケストレーション インスタンスの ID を指定します (必須)。 |
| **`--input`** | インラインまたは JSON ファイルからのオーケストレーター関数への入力 (必須)。 ファイルの場合は、アンパサンド (`@`) を使用してファイルへのパスにプレフィックスを付けます (例: `@path/to/file.json`)。 |
| **`--task-hub-name`** | 使用する Durable Functions タスク ハブの名前 (省略可能)。 |

詳細については、[Durable Functions のドキュメント](./durable/durable-functions-instance-management.md#azure-functions-core-tools)をご覧ください。

## <a name="func-durable-terminate"></a>func durable terminate

指定されたオーケストレーション インスタンスを停止します。

```command
func durable terminate --id <INSTANCE_ID> --reason <REASON>
```

`terminate` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 使用するストレージ接続文字列を含む設定の名前 (省略可能)。 |
| **`--id`** | オーケストレーション インスタンスの ID を指定します (必須)。 |
| **`--reason`** | オーケストレーション を停止する理由 (必須)。 |
| **`--task-hub-name`** | 使用する Durable Functions タスク ハブの名前 (省略可能)。 |

詳細については、[Durable Functions のドキュメント](./durable/durable-functions-instance-management.md#azure-functions-core-tools-4)をご覧ください。

## <a name="func-extensions-install"></a>func extensions install

C# 以外のクラス ライブラリ プロジェクトに Functions 拡張機能をインストールします。 

可能な場合は、代わりに拡張機能バンドルを使用する必要があります。 詳細については、「[拡張機能バンドル](functions-bindings-register.md#extension-bundles)」を参照してください。

C# クラス ライブラリおよび .NET 分離プロジェクトの場合は、代わりに `dotnet add package` などの標準 NuGet パッケージ インストール メソッドを使用します。

`install` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--configPath`** | extensions.csproj ファイルを含むディレクトリのパス。|
| **`--csx`** |   C# スクリプト (.csx) プロジェクトをサポートします。 |
| **`--force`** |  既存の拡張機能のバージョンを更新します。 |
| **`--output`** |  拡張機能の出力パス。 |
| **`--package`** |   特定の拡張パッケージの識別子。 指定しない場合は、`func extensions sync` と同様に、参照されている拡張機能すべてがインストールされます。|
| **`--source`** |  NuGet.org が使用されていない場合は、NuGet によってソースが提供されます。|
| **`--version`** |  拡張機能パッケージのバージョン。 |

host.json ファイル内で拡張機能バンドルが定義されている場合、アクションは実行されません。

## <a name="func-extensions-sync"></a>func extensions sync

追加されたすべての拡張機能を関数アプリにインストールします。

`sync` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--configPath`** | extensions.csproj ファイルを含むディレクトリのパス。|
| **`--csx`** |   C# スクリプト (.csx) プロジェクトをサポートします。 |
| **`--output`** |  拡張機能の出力パス。 |

不足している extensions.csproj ファイルを再生成します。 host.json ファイル内で拡張機能バンドルが定義されている場合、アクションは実行されません。

## <a name="func-kubernetes-deploy"></a>func kubernetes deploy

KEDA を使用して、Functions プロジェクトをカスタム Docker コンテナーとして Kubernetes クラスターにデプロイします。

```command
func kubernetes deploy 
```

このコマンドは、ご自身のプロジェクトをカスタム コンテナーとしてビルドし、動的スケールのために KEDA を使用して Kubernetes クラスターに発行します。 既定のスケーラーまたは KNative を使用してクラスターに発行するには、代わりに [`func deploy` コマンド](#func-deploy)を使用します。 カスタム コンテナーには、Dockerfile が必要です。 Dockerfile でアプリを作成するには、[`func init` コマンド](#func-init) で `--dockerfile` オプションを使用します。 

次の Kubernetes デプロイ オプションを使用できます。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--dry-run`** | 必要に応じて、実行前にデプロイ テンプレートを表示します。 |
| **`--config-map-name`** | デプロイ内で使用する[関数アプリの設定](functions-how-to-use-azure-function-app-settings.md#settings)を含む既存の構成マップの名前 (省略可能)。 `--use-config-map`を必要とします。 既定の動作は、[local.settings.jsonファイル]の `Values` オブジェクトに基づいた設定の作成です。|
| **`--cooldown-period`** | すべてのトリガーがアクティブでなくなった後、デプロイがゼロにスケールダウンするまでのクールダウン期間 (秒単位)。既定値は 300 秒です。 |
| **`--ignore-errors`** | リソースからエラーが返された後、デプロイを続行します。 既定の動作では、エラーの発生時に停止します。 |
| **`--image-name`** | ポッドのデプロイに使用するイメージの名前。このイメージから関数を読み取ります。 |
| **`--keda-version`** | インストールする KEDA のバージョンを設定します。 有効なオプションは `v1` と `v2` です (既定)。 |
| **`--keys-secret-name`** | [関数アクセス キー](functions-bindings-http-webhook-trigger.md#authorization-keys)の格納に使用する Kubernetes シークレット コレクションの名前。 |
| **`--max-replicas`** | ポッドの水平オートスケーラー (HPA) がスケーリングするときの最大レプリカ数を設定します。 |
| **`--min-replicas`** | HPA がスケーリングしなくなる最小レプリカ数を設定します。 |
| **`--mount-funckeys-as-containervolume`** | [関数アクセス キー](functions-bindings-http-webhook-trigger.md#authorization-keys)をコンテナー ボリュームとしてマウントします。 |
| **`--name`** | Kubernetes 内のデプロイと他の成果物に使用される名前。 |
| **`--namespace`** | デプロイする Kubernetes 名前空間を設定します。既定では、既定の名前空間に設定されます。 |
| **`--no-docker`** | 関数が、イメージではなく現在のディレクトリから読み取られます。 イメージ ファイルシステムをマウントする必要があります。 |
| **`--registry`** | 設定すると Docker ビルドが実行され、その名前のレジストリにイメージがプッシュされます。 `--image-name` で `--registry` を使用することはできません。 Docker の場合は、ユーザー名を使用します。 |
| **`--polling-interval`** | HTTP 以外のトリガーをチェックするためのポーリングの間隔 (秒単位)。 既定値は 30 秒です。 |
| **`--pull-secret`** | プライベート レジストリの資格情報にアクセスするために使用されるシークレット。 |
| **`--secret-name`** | デプロイ内で使用する[関数アプリの設定](functions-how-to-use-azure-function-app-settings.md#settings)を含む既存の Kubernetes シークレット コレクションの名前。 既定の動作は、[local.settings.jsonファイル]の `Values` オブジェクトに基づいた設定の作成です。 |
| **`--show-service-fqdn`** | IP アドレスを使用する既定の動作ではなく、Kubernetes FQDN を使用した HTTP トリガーの URL を表示します。 |
| **`--service-type`** | Kubernetes Service の種類を設定します。 サポートされる値は、`ClusterIP`、`NodePort`、`LoadBalancer` (既定) です。 |
| **`--use-config-map`** | `Secret` オブジェクト (v1) ではなく `ConfigMap` オブジェクト (v1) を使用して、[関数アプリの設定](functions-how-to-use-azure-function-app-settings.md#settings)を構成します。 マップ名は、`--config-map-name` を使用して設定されます。|

Core Tools はローカルの Docker CLI を使って、イメージをビルドおよび発行します。 

ご自身の Docker が既にローカルにインストールされていることを確認してください。 ご自身のアカウントに接続するには、`docker login` コマンドを実行します。

詳細については、「[Kubernetes への関数アプリのデプロイ](functions-kubernetes-keda.md#deploying-a-function-app-to-kubernetes)」を参照してください。

## <a name="func-kubernetes-install"></a>func kubernetes install

Kubernetes クラスターに KEDA をインストールします。

```command
func kubernetes install 
```

kubectl 構成ファイル内で定義されているクラスターに KEDA をインストールします。

`install` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--dry-run`** | 実行前にデプロイ テンプレートを表示します。 |
| **`--keda-version`** | インストールする KEDA のバージョンを設定します。 有効なオプションは `v1` と `v2` です (既定)。 |
| **`--namespace`** | 特定の Kubernetes 名前空間へのインストールをサポートします。 設定しない場合は、既定の名前空間が使用されます。 |

詳細については、「[Kubernetes での KEDA と関数の管理](functions-kubernetes-keda.md#managing-keda-and-functions-in-kubernetes)」を参照してください。

## <a name="func-kubernetes-remove"></a>func kubernetes remove

kubectl 構成ファイル内で定義されている Kubernetes クラスターから KEDA を削除します。

```command
func kubernetes remove 
```

kubectl 構成ファイル内で定義されている クラスターから KEDA を削除します。

`remove` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--namespace`** | 特定の Kubernetes 名前空間からのアンインストールをサポートします。 設定しない場合は、既定の名前空間が使用されます。 | 

詳細については、「[Kubernetes からの KEDA のアンインストール](functions-kubernetes-keda.md#uninstalling-keda-from-kubernetes)」を参照してください。

## <a name="func-settings-add"></a>func settings add

[local.settings.jsonファイル]内の `Values` コレクションに新しい設定を追加します。

```command
func settings add <SETTING_NAME> <VALUE>
```

`<SETTING_NAME>` はアプリ設定の名前に、`<VALUE>` はその設定の値に置き換えます。 

`add` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--connectionString`** | 名前と値の組を `Values` コレクションではなく `ConnectionStrings` コレクションに追加します。 `ConnectionStrings` コレクションは、特定のフレームワークで必要とされる場合にのみ使用します。 詳細については、[local.settings.jsonファイル]に関するページをご覧ください。 |

## <a name="func-settings-decrypt"></a>func settings decrypt

[local.settings.jsonファイル]の `Values` コレクション内で前に暗号化された値の暗号化を解除します。

```command
func settings decrypt
```

`ConnectionStrings` コレクション内の接続文字列の値についても、暗号化が解除されます。 また、local.settings.json では、`IsEncrypted` が `false` に設定されます。 貴重な情報が local.settings.json から漏洩するリスクを減らすために、ローカル設定は暗号化されます。 Azure では、アプリケーション設定は常に、暗号化されて格納されます。 

## <a name="func-settings-delete"></a>func settings delete

[local.settings.jsonファイル]内の `Values` コレクションから既存の設定を削除します。

```command
func settings delete <SETTING_NAME>
```

`<SETTING_NAME>` はアプリ設定の名前に、`<VALUE>` はその設定の値に置き換えます。 

`delete` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--connectionString`** | 名前と値の組を `Values` コレクションではなく `ConnectionStrings` コレクションから削除します。 |

## <a name="func-settings-encrypt"></a>func settings encrypt

[local.settings.jsonファイル]の `Values` コレクション内にある個々の項目の値を暗号化します。

```command
func settings encrypt
```

`ConnectionStrings` コレクション内の接続文字列の値も暗号化されます。 また、local.settings.json では、`IsEncrypted` が `true` に設定されます。これにより、ローカル ランタイムが設定を使用する前に暗号化を解除することが指定されます。 貴重な情報が local.settings.json から漏洩するリスクを減らすために、ローカル設定は暗号化されます。 Azure では、アプリケーション設定は常に、暗号化されて格納されます。 

## <a name="func-settings-list"></a>func settings list

[local.settings.jsonファイル]内の `Values` コレクションに設定のリストを出力します。 

```command
func settings list
```

`ConnectionStrings` コレクションの接続文字列も出力されます。 既定では値はセキュリティのためにマスクされます。 `--showValue` オプションを使用すると、実際の値を表示することができます。

`list` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--showValue`** | 出力内にマスクされていない実際の値を表示します。 |

## <a name="func-templates-list"></a>func templates list

使用可能な関数 (トリガー) テンプレートを一覧表示します。

`list` アクションでは、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--language`** | 返されたテンプレートをフィルター処理するための言語。 既定では、すべての言語が返されます。 |

[local.settings.jsonファイル]: functions-develop-local.md#local-settings-file

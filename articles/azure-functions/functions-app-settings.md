---
title: Azure Functions のアプリケーション設定のリファレンス
description: Azure Functions のアプリケーション設定または環境変数の参照ドキュメントです。
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: ada3429d32ea08f8185ba19937b963a30400c7b0
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659661"
---
# <a name="app-settings-reference-for-azure-functions"></a>Azure Functions のアプリケーション設定のリファレンス

関数アプリのアプリ設定には、その関数アプリのすべての関数に影響する構成オプションが含まれています。 ローカルで実行する場合、これらの設定は、ローカルの[環境変数](functions-develop-local.md#local-settings-file)としてアクセスされます。 この記事では、関数アプリで使用できるアプリケーション設定の一覧を紹介します。

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

[host.json](functions-host-json.md) ファイルと [local.settings.json](functions-develop-local.md#local-settings-file) ファイルには、他の関数アプリ構成オプションもあります。
例の接続文字列の値は、読みやすくするために省略されています。

> [!NOTE]
> アプリケーション設定を使用して、host.json ファイル自体を変更することなく、host.json 設定値をオーバーライドできます。 これは、特定の環境の特定の host.json 設定を構成または変更する必要がある場合に便利です。 これにより、プロジェクトを再発行しなくても、host.json 設定を変更できます。 詳細については、[host.json のリファレンスに関する記事](functions-host-json.md#override-hostjson-values)をご覧ください。 関数アプリの設定に変更を加えるためには、関数アプリを再起動する必要があります。

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Application Insights のインストルメンテーション キー。 `APPINSIGHTS_INSTRUMENTATIONKEY` または `APPLICATIONINSIGHTS_CONNECTION_STRING` のいずれかのみを使用してください。 Application Insights がソブリン クラウドで実行されている場合は、`APPLICATIONINSIGHTS_CONNECTION_STRING` を使用します。 詳細については、[Azure Functions で監視を構成する](configure-monitoring.md)方法に関するページを参照してください。

|Key|値の例|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|`55555555-af77-484b-9032-64f83bb83bb`|

## <a name="applicationinsights_connection_string"></a>APPLICATIONINSIGHTS_CONNECTION_STRING

Application Insights の接続文字列。 次の場合は、`APPINSIGHTS_INSTRUMENTATIONKEY` ではなく `APPLICATIONINSIGHTS_CONNECTION_STRING` を使用します。

+ お使いの関数アプリで接続文字列を使用した追加のカスタマイズ サポートが必要な場合。
+ カスタム エンドポイントを必要とするソブリン クラウドで Application Insights インスタンスが実行されている場合。

詳細については、[接続文字列](../azure-monitor/app/sdk-connection-string.md)に関するページを参照してください。

|Key|値の例|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTION_STRING|`InstrumentationKey=...`|

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

既定では、[Functions プロキシ](functions-proxies.md)は、ショートカットを使用して、同じ関数アプリ内の関数にプロキシから直接 API 呼び出しを送信します。 このショートカットは、新しい HTTP 要求を作成する代わりに使用されます。 この設定を使用すると、そのショートカット動作を無効にすることができます。

|Key|値|説明|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|`true`|ローカル関数アプリ内の関数を指すバックエンド URL を使用した呼び出しは、その関数に直接送信されません。 代わりに、要求は、関数アプリの HTTP フロントエンドに戻されます。|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|`false`|ローカル関数アプリ内の関数を指すバックエンド URL を使用した呼び出しは、その関数に直接転送されます。 これが既定値です。 |

## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

この設定は、文字 `%2F` がバックエンド URL に挿入されたときにこれをルート パラメーターでスラッシュとしてデコードするかどうかを制御します。

|Key|値|説明|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|`true`|エンコードされたスラッシュを含むルート パラメーターがデコードされます。 |
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|`false`|すべてのルート パラメーターは変更されずに渡されます。これは既定の動作です。 |

たとえば、`myfunction.com` ドメインの関数アプリ用の proxies.json ファイルを考えてみます。

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```

`AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES` が `true` に設定されている場合、URL `example.com/api%2ftest` は `example.com/api/test` に解決されます。 既定では、URL は `example.com/test%2fapi` のまま変更されません。 詳細については、[Functions プロキシ](functions-proxies.md)に関するページを参照してください。

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Functions ランタイムのバージョン 2.x 以降では、ランタイム環境に基づいてアプリの動作を構成します。 この値は初期化時に読み取られ、任意の値に設定できます。 ランタイムによって受け入れられるのは、`Development`、`Staging`、および `Production` の値のみです。 Azure での実行時にこのアプリケーション設定が存在しない場合、環境は `Production` と見なされます。 Azure のランタイム環境を `Production` 以外のものに変更する必要がある場合は、`ASPNETCORE_ENVIRONMENT` の代わりにこの設定を使用します。 ローカル コンピューターで実行している場合は、Azure Functions Core Tools により `AZURE_FUNCTIONS_ENVIRONMENT` が `Development` に設定されます。local.settings.json ファイルでこれをオーバーライドすることはできません。 詳細については、「[環境別の起動のクラスとメソッド](/aspnet/core/fundamentals/environments#environment-based-startup-class-and-methods)」を参照してください。

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

Functions ランタイムのバージョン 2.x 以降では、現在の環境の [host.json](functions-host-json.md) 設定をアプリケーション設定でオーバーライドできます。 これらのオーバーライドは、`AzureFunctionsJobHost__path__to__setting` という名前のアプリケーション設定として表されます。 詳細については、「[host.json 値をオーバーライドする](functions-host-json.md#override-hostjson-values)」を参照してください。

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

ログの保存と、それらをポータルの **[モニター]** タブに表示する、オプションのストレージ アカウントの接続文字列です。 この設定は、Azure Functions ランタイムのバージョン 1.x を対象とするアプリに対してのみ有効です。 このストレージ アカウントは、blob、キュー、およびテーブルをサポートする汎用的なものである必要があります。 詳しくは、「[ストレージ アカウントの要件](storage-considerations.md#storage-account-requirements)」をご覧ください。

|Key|値の例|
|---|------------|
|AzureWebJobsDashboard|`DefaultEndpointsProtocol=https;AccountName=...`|

> [!NOTE]
> より良いパフォーマンスとエクスペリエンスのために、ランタイムのバージョン 2.x 以降では、`AzureWebJobsDashboard` ではなく APPINSIGHTS_INSTRUMENTATIONKEY と App Insights を使用します。

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` は、関数アプリのルート URL 用に表示される既定のランディング ページを無効にすることを意味します。 既定値は `false` です。

|Key|値の例|
|---|------------|
|AzureWebJobsDisableHomepage|`true`|

このアプリ設定を省略するか、`false` に設定した場合、URL `<functionappname>.azurewebsites.net` の応答に対し、次の例のようなものが表示されます。

![関数アプリのランディング ページ](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation&quot;></a>AzureWebJobsDotNetReleaseCompilation

`true` は、.NET コードのコンパイルにリリース モードを使用することを意味し、`false` は、デバッグ モードを使用することを意味します。 既定値は `true` です。

|Key|値の例|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|`true`|

## <a name=&quot;azurewebjobsfeatureflags&quot;></a>AzureWebJobsFeatureFlags

有効にするベータ機能のコンマ区切りの一覧です。 これらのフラグで有効となるベータ機能は本番には適しませんが、公開前の実験的な使用には有効にすることができます。

|Key|値の例|
|---|------------|
|AzureWebJobsFeatureFlags|`feature1,feature2`|

## <a name=&quot;azurewebjobssecretstoragetype&quot;></a>AzureWebJobsSecretStorageType

キーの保存に使用するリポジトリまたはプロバイダーを指定します。 現時点でサポートされているリポジトリは、BLOB ストレージ (&quot;Blob") およびローカル ファイル システム ("Files") です。 既定では、バージョン 2 では BLOB、バージョン 1 ではファイル システムが使用されます。

|Key|値の例|
|---|------------|
|AzureWebJobsSecretStorageType|ファイル|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Azure Functions ランタイムでは、このストレージ アカウント接続文字列は通常の操作に使用されます。 このストレージ アカウントの使用方法としては、キー管理、タイマー トリガー管理、Event Hubs チェックポイントなどがあります。 このストレージ アカウントは、blob、キュー、およびテーブルをサポートする汎用的なものである必要があります。 「[ストレージ アカウント](functions-infrastructure-as-code.md#storage-account)」および「[ストレージ アカウントの要件](storage-considerations.md#storage-account-requirements)」を参照してください。

|Key|値の例|
|---|------------|
|AzureWebJobsStorage|`DefaultEndpointsProtocol=https;AccountName=...`|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Typescript で使用されるコンパイラへのパスです。 必要に応じて、既定値はオーバーライドできます。

|Key|値の例|
|---|------------|
|AzureWebJobs_TypeScriptPath|`%HOME%\typescript`|

## <a name="function_app_edit_mode"></a>FUNCTION\_APP\_EDIT\_MODE

Azure portal での編集が有効になっているかどうかを決定します。 有効な値は "readwrite" および "readonly" です。

|Key|値の例|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|`readonly`|

## <a name="functions_extension_version"></a>FUNCTIONS\_EXTENSION\_VERSION

関数アプリをホストする Functions ランタイムのバージョンです。 メジャー バージョンのチルダ (`~`) は、そのメジャー バージョンの最新バージョンを使用することを意味します (例: "~3")。 同じメジャー バージョンの新しいバージョンが使用できる場合、それらは関数アプリに自動的にインストールされています。 特定のバージョンにアプリを固定するには、完全なバージョン番号 (例: "3.0.12345") を使用します。 既定値は "~3" です。 `~1` の値は、アプリをバージョン 1.x のランタイムに固定します。 詳細については、「[Azure Functions ランタイム バージョンをターゲットにする方法](functions-versions.md)」をご覧ください。 `~4` の値を使用すると、プレビュー バージョンの Azure Functions を実行して .NET 6.0 プレビューを使用できます。 詳細については、[Azure Functions v4 の早期プレビュー](https://aka.ms/functions-dotnet6earlypreview-wiki)に関するページを参照してください。

|Key|値の例|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|`~3`|

## <a name="functions_v2_compatibility_mode"></a>FUNCTIONS\_V2\_COMPATIBILITY\_MODE

この設定により、関数アプリはバージョン 3.x ランタイムでバージョン 2.x 互換モードで実行できるようになります。 この設定は、[関数アプリをランタイムのバージョン 2.x から 3.x にアップグレードする](functions-versions.md#migrating-from-2x-to-3x)ときに問題が発生した場合にのみ使用してください。

>[!IMPORTANT]
> この設定は、アプリケーションをバージョン 3.x で正常に動作するように更新するときに、短期的な回避策としてのみ使用することを目的としています。 [2.x ランタイムがサポートされている](functions-versions.md)限り、この設定はサポートされます。 この設定を使用せずにバージョン 3.x でアプリを実行できない問題が発生した場合は、[問題を報告](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md)してください。

[FUNCTIONS\_EXTENSION\_VERSION](functions-app-settings.md#functions_extension_version) を `~3` に設定する必要があります。

|Key|値の例|
|---|------------|
|FUNCTIONS\_V2\_COMPATIBILITY\_MODE|`true`|

## <a name="functions_worker_process_count"></a>FUNCTIONS\_WORKER\_PROCESS\_COUNT

言語ワーカー プロセスの最大数を指定します。既定値は `1` です。 許容される最大値は `10` です。 関数呼び出しは、言語ワーカー プロセス間で均等に分散されます。 言語ワーカー プロセスは、FUNCTIONS\_WORKER\_PROCESS\_COUNT によって設定されたカウントに達するまで、10 秒ごとに生成されます。 複数の言語ワーカー プロセスの使用は、[スケーリング](functions-scale.md)と同じではありません。 CPU にバインドされた呼び出しと I/O にバインドされた呼び出しがワークロードに混在している場合は、この設定を使用することを検討してください。 この設定はすべての非 .NET 言語に適用されます。

|Key|値の例|
|---|------------|
|FUNCTIONS\_WORKER\_PROCESS\_COUNT|`2`|

## <a name="functions_worker_runtime"></a>FUNCTIONS\_WORKER\_RUNTIME

ワーカー ランタイムが関数アプリに読み込む言語。  これは、アプリケーションで使用されている言語に対応します (たとえば、`dotnet`)。 Azure Functions Runtime のバージョン 2.x 以降では、特定の関数アプリでサポートできる言語は 1 つだけです。

|Key|値の例|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|`node`|

有効な値:

| 値 | 言語 |
|---|---|
| `dotnet` | [C# (クラス ライブラリ)](functions-dotnet-class-library.md)<br/>[C# (スクリプト)](functions-reference-csharp.md) |
| `dotnet-isolated` | [C# (分離プロセス)](dotnet-isolated-process-guide.md) |
| `java` | [Java](functions-reference-java.md) |
| `node` | [JavaScript](functions-reference-node.md)<br/>[TypeScript](functions-reference-node.md#typescript) |
| `powershell` | [PowerShell](functions-reference-powershell.md) |
| `python` | [Python](functions-reference-python.md) |

## <a name="mdmaxbackgroundupgradeperiod"></a>MDMaxBackgroundUpgradePeriod

PowerShell 関数アプリの管理対象の依存関係のバックグラウンド更新期間を制御します。既定値は `7.00:00:00` (毎週) です。

各 PowerShell ワーカー プロセスは、そのプロセスの開始時に PowerShell ギャラリーでモジュールのアップグレードのチェックを開始し、その後は `MDMaxBackgroundUpgradePeriod` ごとにチェックします。 PowerShell ギャラリーで利用可能になった新しいモジュール バージョンは、ファイル システムにインストールされ、PowerShell ワーカーが使用できるになります。 この値を小さくすると、関数アプリは新しいモジュール バージョンを早く取得できますが、アプリ リソースの使用量 (ネットワーク I/O、CPU、ストレージ) も増加します。 この値を大きくすると、アプリ リソースの使用量は減少しますが、アプリへの新しいモジュール バージョンの配信が遅れる可能性があります。

|Key|値の例|
|---|------------|
|MDMaxBackgroundUpgradePeriod|`7.00:00:00`|

詳細については、「[依存関係の管理](functions-reference-powershell.md#dependency-management)」を参照してください。

## <a name="mdnewsnapshotcheckperiod"></a>MDNewSnapshotCheckPeriod

管理対象の依存関係のアップグレードがインストールされているかどうかを各 PowerShell ワーカーが確認する頻度を指定します。 既定の頻度は `01:00:00` (毎時) です。

新しいモジュール バージョンがファイル システムにインストールされたら、すべての PowerShell ワーカー プロセスを再起動する必要があります。 PowerShell ワーカーを再起動すると、現在の関数の実行が中断される可能性があるため、アプリの可用性がその影響を受けます。 すべての PowerShell ワーカー プロセスが再起動されるまで、関数呼び出しでは、前のモジュール バージョンまたは新しいモジュール バージョンのいずれかが使用される可能性があります。 すべての PowerShell ワーカーの再起動は `MDNewSnapshotCheckPeriod` 以内に完了します。

各 `MDNewSnapshotCheckPeriod` 内で、PowerShell ワーカーにより、管理対象の依存関係のアップグレードがインストールされているかどうかが確認されます。 アップグレードがインストールされると、再起動が開始されます。 この値を大きくすると、再起動による中断の頻度は減少します。 ただし、大きくすることにより、関数呼び出しで古いまたは新しいモジュール バージョンが非決定的に使用される可能性がある期間が長くなる恐れもあります。

|Key|値の例|
|---|------------|
|MDNewSnapshotCheckPeriod|`01:00:00`|

詳細については、「[依存関係の管理](functions-reference-powershell.md#dependency-management)」を参照してください。


## <a name="mdminbackgroundupgradeperiod"></a>MDMinBackgroundUpgradePeriod

管理対象の依存関係のアップグレードに関する前回のチェックの後、別のアップグレード チェックが開始されるまでの期間。既定値は `1.00:00:00` (毎日) です。

ワーカーの頻繁な再起動によってモジュールのアップグレードが過剰にならないように、任意のワーカーで直近 `MDMinBackgroundUpgradePeriod` 以内にモジュールのアップグレード確認が開始されているときは、その確認は行われません。

|Key|値の例|
|---|------------|
|MDMinBackgroundUpgradePeriod|`1.00:00:00`|

詳細については、「[依存関係の管理](functions-reference-powershell.md#dependency-management)」を参照してください。

## <a name="pip_extra_index_url"></a>PIP\_EXTRA\_INDEX\_URL

この設定の値は、Python アプリのカスタム パッケージ インデックス URL を示します。 この設定は、追加のパッケージ インデックスにあるカスタム依存関係を使用してリモート ビルドを実行する必要がある場合に使用します。

|Key|値の例|
|---|------------|
|PIP\_EXTRA\_INDEX\_URL|`http://my.custom.package.repo/simple` |

詳細については、Python 開発者リファレンスの「[カスタムの依存関係](functions-reference-python.md#remote-build-with-extra-index-url)」を参照してください。

## <a name="python_isolate_worker_dependencies-preview"></a>PYTHON\_ISOLATE\_WORKER\_DEPENDENCIES (プレビュー)

この構成は Python 関数アプリに固有です。 モジュール読み込み順序の優先順位を定義します。 Python 関数アプリでモジュール競合に関する問題が生じたとき (protobuf、tensorflow、grpcio をプロジェクトで使用しているときなど)、このアプリ設定を `1` に構成すると問題が解決します。 既定では、この値は `0` に設定されます。 このフラグは現在プレビューの段階です。

|キー|値|説明|
|---|-----|-----------|
|PYTHON\_ISOLATE\_WORKER\_DEPENDENCIES|`0`| Python ライブラリを内部 Python ワーカーの依存関係から読み込むことを優先します。 requirements.txt で定義されているサードパーティ ライブラリはシャドウされる場合があります。 |
|PYTHON\_ISOLATE\_WORKER\_DEPENDENCIES|`1`| Python ライブラリを requirements.txt で定義されているアプリケーションのパッケージから読み込むことを優先します。 これにより、ライブラリが内部 Python ワーカーのライブラリと衝突することがなくなります。 |

## <a name="python_enable_worker_extensions"></a>PYTHON\_ENABLE\_WORKER\_EXTENSIONS

この構成は Python 関数アプリに固有です。 これを `1` に設定することで、ワーカーは requirements.txt で定義されている [Python ワーカー拡張機能](functions-reference-python.md#python-worker-extensions)で読み込みが可能になります。 これにより、関数アプリはサードパーティ製パッケージによって提供される新機能にアクセスできます。 また、アプリでの関数の読み込みと呼び出しの動作が変更される場合もあります。 選択する拡張機能は、それを使用するリスクが生じるので、信頼できるものであることを確認してください。 Azure Functions は、あらゆる拡張機能に対して明示的な保証をしません。 拡張機能の使い方については、拡張機能のマニュアル ページまたは readme ドキュメントを参照してください。既定では、この値は `0` に設定されます。

|キー|値|説明|
|---|-----|-----------|
|PYTHON\_ENABLE\_WORKER\_EXTENSIONS|`0`| Python ワーカー拡張機能を無効にします。 |
|PYTHON\_ENABLE\_WORKER\_EXTENSIONS|`1`| Python ワーカーが requirements.txt から拡張機能を読み込めるようにします。 |

## <a name="python_threadpool_thread_count"></a>PYTHON\_THREADPOOL\_THREAD\_COUNT

関数呼び出しを実行するために Python 言語ワーカーによって使用されるスレッドの最大数を指定します。Python バージョン `3.8` 以前では、既定値 `1` を使用します。 Python バージョン `3.9` 以降では、値は `None` に設定されます。 この設定は、実行中に設定されるスレッドの数を保証するものではないことに注意してください。 この設定により、Python では、スレッドの数を指定された値に増やすことができます。 この設定は、Python 関数アプリにのみ適用されます。 また、この設定は、コルーチンではなく、同期関数の呼び出しに適用されます。

|Key|値の例|最大値|
|---|------------|---------|
|PYTHON\_THREADPOOL\_THREAD\_COUNT|2|32|

## <a name="scale_controller_logging_enabled"></a>SCALE\_CONTROLLER\_LOGGING\_ENABLED

"_この設定は現在プレビューの段階です。_ "

この設定は、Azure Functions スケール コントローラーからのログ記録を制御します。 詳細については、[スケール コントローラーのログ](functions-monitoring.md#scale-controller-logs)に関するセクションを参照してください。

|Key|値の例|
|-|-|
|SCALE_CONTROLLER_LOGGING_ENABLED|`AppInsights:Verbose`|

このキーの値は `<DESTINATION>:<VERBOSITY>` の形式で指定されます。これは次のように定義されます。

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

## <a name="scm_logstream_timeout"></a>SCM\_LOGSTREAM\_TIMEOUT

ストリーミング ログに接続されているときのタイムアウトを秒単位で制御します。 既定値は 7,200 (2 時間) です。 

|Key|値の例|
|-|-|
|SCM_LOGSTREAM_TIMEOUT|`1800`|

上記のサンプル値 `1800` では、タイムアウトが 30 分に設定されます。 詳細については、「[ストリーミング ログを有効にする](functions-run-local.md#enable-streaming-logs)」を参照してください。

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE\_CONTENTAZUREFILECONNECTIONSTRING

Windows 上で実行されているイベント ドリブン スケーリング プランに関数アプリのコードと構成が格納されているストレージ アカウントの接続文字列です。 詳細については、「[Function App を作成する](functions-infrastructure-as-code.md#windows)」を参照してください。

|Key|値の例|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|`DefaultEndpointsProtocol=https;AccountName=...`|

Windows 上で実行されている Premium プランまたは従量課金プランにデプロイする場合にのみ使用されます。 Linux を実行する従量課金プランではサポートされていません。 この設定を変更または削除すると、関数アプリが起動しなくなることがあります。 詳細については、[こちらのトラブルシューティング記事](functions-recover-storage-account.md#storage-account-application-settings-were-deleted)を参照してください。

## <a name="website_contentovervnet"></a>WEBSITE\_CONTENTOVERVNET

`1` の値を指定すると、ストレージ アカウントを仮想ネットワークに制限している場合に、関数アプリをスケーリングできます。 ストレージ アカウントを仮想ネットワークに制限する場合は、この設定を有効にする必要があります。 詳細については、「[ストレージ アカウントを仮想ネットワークに制限する](configure-networking-how-to.md#restrict-your-storage-account-to-a-virtual-network)」を参照してください。

|Key|値の例|
|---|------------|
|WEBSITE_CONTENTOVERVNET|`1`|

[Premium](functions-premium-plan.md) および [Dedicated (App Service) プラン](dedicated-plan.md) (Standard 以上) でサポートされています。 [従量課金プラン](consumption-plan.md)で実行している場合はサポートされません。 

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

Windows 上のイベント ドリブン スケーリング プラン内の関数アプリ コードと構成へのファイル パス。 WEBSITE_CONTENTAZUREFILECONNECTIONSTRING と共に使用されます。 既定は、関数アプリ名で始まる一意文字列です。 「[Function App を作成する](functions-infrastructure-as-code.md#windows)」を参照してください。

|Key|値の例|
|---|------------|
|WEBSITE_CONTENTSHARE|`functionapp091999e2`|

Windows 上で実行されている Premium プランまたは従量課金プランにデプロイする場合にのみ使用されます。 Linux を実行する従量課金プランではサポートされていません。 この設定を変更または削除すると、関数アプリが起動しなくなることがあります。 詳細については、[こちらのトラブルシューティング記事](functions-recover-storage-account.md#storage-account-application-settings-were-deleted)を参照してください。

デプロイ中に Azure Resource Manager テンプレートを使用して関数アプリを作成する場合、テンプレートに WEBSITE_CONTENTSHARE を含めないでください。 このスロット設定はデプロイ中に生成されます。 詳細については、[関数アプリのリソース デプロイを自動化する](functions-infrastructure-as-code.md#windows)方法に関するページを参照してください。

## <a name="website_dns_server"></a>WEBSITE\_DNS\_SERVER

IP アドレスの解決時にアプリによって使用される DNS サーバーを設定します。 この設定は、[Azure DNS Private Zones](functions-networking-options.md#azure-dns-private-zones) や[プライベート エンドポイント](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network)など、特定のネットワーク機能を使用する場合に必要になることがよくあります。

|Key|値の例|
|---|------------|
|WEBSITE\_DNS\_SERVER|`168.63.129.16`|

## <a name="website_enable_brotli_encoding"></a>WEBSITE\_ENABLE\_BROTLI\_ENCODING

圧縮のために既定の gzip 圧縮ではなく、Brotli エンコーディングが使用されるかどうかを制御します。 `WEBSITE_ENABLE_BROTLI_ENCODING` が `1` に設定されている場合は Brotli エンコーディングが使用され、それ以外の場合は gzip エンコーディングが使用されます。

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

アプリがスケールアウトできる最大のインスタンス数です。 既定は無制限です。

> [!IMPORTANT]
> この設定は、プレビューの段階です。  スケールアウトの制限に推奨される、[関数で最大にスケールアウトするためのアプリ プロパティ](./event-driven-scaling.md#limit-scale-out)が追加されています。

|Key|値の例|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|`5`|

## <a name="website_node_default_version"></a>WEBSITE\_NODE\_DEFAULT_VERSION

"_Windows のみ_"。
Windows で関数アプリを実行するときに使用する Node.js のバージョンを設定します。 チルダ (~) を使用して、ランタイムがターゲット メジャー バージョンの利用可能な最新バージョンを使用するようにする必要があります。 たとえば、`~10` に設定すると、最新バージョンの Node.js 10 が使用されます。 メジャー バージョンがチルダ付きで対象になっている場合は、マイナー バージョンを手動で更新する必要はありません。

|Key|値の例|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|`~10`|

## <a name="website_run_from_package"></a>WEBSITE\_RUN\_FROM\_PACKAGE

マウントされたパッケージ ファイルから関数アプリを実行できるようにします。

|Key|値の例|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|`1`|

有効な値は、展開パッケージ ファイルの場所に解決される URL、または `1` です。 `1` に設定した場合、パッケージは `d:\home\data\SitePackages` フォルダーに存在する必要があります。 この設定で zip デプロイを使用すると、パッケージは自動的にこの場所にアップロードされます。 プレビューでは、この設定は `WEBSITE_RUN_FROM_ZIP` という名前でした。 詳細については、[パッケージ ファイルからの関数の実行](run-functions-from-deployment-package.md)に関するページを参照してください。

## <a name="website_time_zone"></a>WEBSITE\_TIME\_ZONE

関数アプリのタイムゾーンを設定できます。

|Key|OS|値の例|
|---|--|------------|
|WEBSITE\_TIME\_ZONE|Windows|`Eastern Standard Time`|
|WEBSITE\_TIME\_ZONE|Linux|`America/New_York`|

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="website_vnet_route_all"></a>WEBSITE\_VNET\_ROUTE\_ALL

アプリからのすべての送信トラフィックが仮想ネットワーク経由でルーティングされるかどうかを示します。 設定値が `1` の場合は、すべてのトラフィックが仮想ネットワーク経由でルーティングされることを示します。 [リージョンでの仮想ネットワーク統合](functions-networking-options.md#regional-virtual-network-integration)の機能を使用する場合は、この設定が必要です。 また、[仮想ネットワーク NAT ゲートウェイを使用して静的な送信 IP アドレスを定義する](functions-how-to-use-nat-gateway.md)場合にも使用されます。

|Key|値の例|
|---|------------|
|WEBSITE\_VNET\_ROUTE\_ALL|`1`|

## <a name="next-steps"></a>次のステップ

[アプリケーション設定の更新方法](functions-how-to-use-azure-function-app-settings.md#settings)

[host.json ファイルの構成設定を参照する](functions-host-json.md)

[App Service アプリの他のアプリ設定を参照する](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

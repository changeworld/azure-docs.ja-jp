---
title: 環境変数とアプリ設定のリファレンス
description: 一般的に使用される環境変数と、アプリ設定で変更できるものについて説明します。
ms.topic: article
ms.date: 06/14/2021
ms.openlocfilehash: e315a51e7d160bcc2d8864cfa954924f0fe4c094
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132025757"
---
# <a name="environment-variables-and-app-settings-in-azure-app-service"></a>Azure App Service の環境変数とアプリ設定

[Azure App Service](overview.md) では、特定の設定をデプロイまたはランタイム環境で環境変数として使用できます。 これらの設定の一部は、[アプリ設定](configure-common.md#configure-app-settings)として手動で設定するときにカスタマイズできます。 このリファレンスでは、使用またはカスタマイズできる変数を示します。

## <a name="app-environment"></a>アプリ環境

次の環境変数は、一般的なアプリ環境に関連しています。

| 設定の名前| 説明 | 例 |
|-|-|-|
| `WEBSITE_SITE_NAME` | 読み取り専用です。 [アプリ名]:  ||
| `WEBSITE_RESOURCE_GROUP` | 読み取り専用です。 アプリ リソースを含む Azure リソース グループの名前。 ||
| `WEBSITE_OWNER_NAME` | 読み取り専用です。 アプリ、リソース グループ、Web スペースを所有する Azure サブスクリプション ID が含まれています。 ||
| `REGION_NAME` | 読み取り専用です。 アプリのリージョン名。 ||
| `WEBSITE_PLATFORM_VERSION` | 読み取り専用です。 App Service プラットフォームのバージョン。 ||
| `HOME` | 読み取り専用です。 ホーム ディレクトリへのパス (たとえば、Windows では `D:\home`)。 ||
| `SERVER_PORT` | 読み取り専用です。 アプリでリッスンされるポート。 | |
| `WEBSITE_WARMUP_PATH`  | アプリをウォームアップするために ping を実行する相対パス (スラッシュで始まる)。 既定値は `/` で、ルート パスに ping を実行します。 認証されていないクライアントを拒否するように [App Service 認証](overview-authentication-authorization.md)が設定されている場合でも、認証されていないクライアント (Azure Traffic Manager など) でこの特定のパスに ping を実行できます。 (注: このアプリ設定では、Always On で使用されるパスは変更されません。) ||
| `WEBSITE_COMPUTE_MODE` | 読み取り専用です。 アプリが専用 (`Dedicated`) と共有 (`Shared`) のどちらの VM で実行されるかを指定します。 ||
| `WEBSITE_SKU` | 読み取り専用です。 アプリの SKU。 指定できる値は、`Free`、`Shared`、`Basic`、および `Standard` です。 ||
| `SITE_BITNESS` | 読み取り専用です。 アプリが 32 ビット (`x86`) と 64 ビット (`AMD64`) のどちらであるかを示します。 ||
| `WEBSITE_HOSTNAME` | 読み取り専用です。 アプリのプライマリ ホスト名。 ここでは、カスタム ホスト名は考慮されません。 ||
| `WEBSITE_VOLUME_TYPE` | 読み取り専用です。 現在使用中のストレージ ボリュームの種類を示します。 ||
| `WEBSITE_NPM_DEFAULT_VERSION` | アプリが使用している既定の NPM バージョン。 ||
| `WEBSOCKET_CONCURRENT_REQUEST_LIMIT` | 読み取り専用です。 WebSocket の同時要求数の制限。 **Standard** レベル以上では、この値は `-1` ですが、VM のサイズに応じて VM あたりの制限があります ([VM 間数値制限](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)に関する記事をご覧ください)。 ||
| `WEBSITE_PRIVATE_EXTENSIONS` | プライベート サイト拡張機能の使用を無効にするには、`0` に設定します。 ||
| `WEBSITE_TIME_ZONE` | 既定では、アプリのタイム ゾーンは常に UTC です。 「[TimeZone](/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10))」に記載されている有効な値のいずれかに変更できます。 指定した値が認識されない場合は、UTC が使用されます。 | `Atlantic Standard Time` |
| `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG` | ストレージ ボリュームのフェールオーバーまたは再構成の場合、アプリはスタンバイ ストレージ ボリュームに切り替えられます。 既定の設定 (`1`) では、ストレージ インフラストラクチャの変更時にワーカー プロセスがリサイクルされません。 Windows Communication Foundation (WCF) アプリを実行している場合は、これを `0` に設定して無効にします。 この設定はスロット固有であるため、すべてのスロットで設定する必要があります。 ||
| `WEBSITE_PROACTIVE_AUTOHEAL_ENABLED` | 既定では、VM インスタンスは、割り当てられたメモリの 90% を 30 秒以上使用している場合、または過去 2 分間の全要求の 80% で実行時間が 200 秒を超えている場合に、プロアクティブに「自動修復」されます。 VM インスタンスでこれらの規則のいずれかがトリガーされた場合、復旧プロセスでインスタンスが重複して再起動されます。 この回復動作を無効にするには、`false` に設定します。 既定では、 `true`です。 詳細については、[プロアクティブな自動修復](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html)に関する記事をご覧ください。 ||
| `WEBSITE_PROACTIVE_CRASHMONITORING_ENABLED` | ハンドルされない例外が 24 時間以内に 4 回以上発生したために、アプリの VM インスタンスの w3wp.exe プロセスがクラッシュすると、そのインスタンスのメイン ワーカー プロセスにデバッガー プロセスがアタッチされ、ワーカー プロセスが再びクラッシュしたときにメモリ ダンプが収集されます。 その後、このメモリ ダンプが分析され、クラッシュの原因となったスレッドの呼び出し履歴が App Service のログに記録されます。 この自動監視動作を無効にするには、`false` に設定します。 既定では、 `true`です。 詳細については、[プロアクティブなクラッシュの監視](https://azure.github.io/AppService/2021/03/01/Proactive-Crash-Monitoring-in-Azure-App-Service.html)に関する記事をご覧ください。 ||
| `WEBSITE_DAAS_STORAGE_SASURI` | (プロアクティブまたは手動の) クラッシュの監視では、メモリ ダンプは既定で削除されます。 メモリ ダンプをストレージ BLOB コンテナーに保存するには、SAS URI を指定します。  ||
| `WEBSITE_CRASHMONITORING_ENABLED` | 手動で[クラッシュの監視](https://azure.github.io/AppService/2020/08/11/Crash-Monitoring-Feature-in-Azure-App-Service.html)を有効にするには、`true` に設定します。 `WEBSITE_DAAS_STORAGE_SASURI` と `WEBSITE_CRASHMONITORING_SETTINGS` も設定する必要があります。 既定では、 `false`です。 リモート デバッグが有効になっている場合、この設定は無効です。 また、この設定が `true` に設定されている場合、[プロアクティブなクラッシュの監視](https://azure.github.io/AppService/2020/08/11/Crash-Monitoring-Feature-in-Azure-App-Service.html)は無効になります。 ||
| `WEBSITE_CRASHMONITORING_SETTINGS` | 次の形式の JSON: `{"StartTimeUtc": "2020-02-10T08:21","MaxHours": "<elapsed-hours-from-StartTimeUtc>","MaxDumpCount": "<max-number-of-crash-dumps>"}`。 `WEBSITE_CRASHMONITORING_ENABLED` が指定されている場合に、[クラッシュの監視](https://azure.github.io/AppService/2020/08/11/Crash-Monitoring-Feature-in-Azure-App-Service.html)を構成するために必要です。 ストレージ アカウントにクラッシュ ダンプを保存せずに呼び出し履歴のみをログに記録するには、JSON に `,"UseStorageAccount":"false"` を追加します。 ||
| `REMOTEDEBUGGINGVERSION` | リモート デバッグ バージョン。 ||
| `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` | 既定では、アプリの作成時に App Service によって共有ストレージが作成されます。 代わりにカスタム ストレージ アカウントを使用するには、ストレージ アカウントの接続文字列に設定します。 関数については、[Functions のアプリ設定のリファレンス](../azure-functions/functions-app-settings.md#website_contentazurefileconnectionstring)をご覧ください。 | `DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>` |
| `WEBSITE_CONTENTSHARE` | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` でカスタム ストレージ アカウントを指定する場合は、App Service によってアプリのそのストレージ アカウントにファイル共有が作成されます。 カスタム名を使用するには、この変数に使用する名前を設定します。 指定した名前を持つファイル共有が存在しない場合は、App Service によって作成されます。 | `myapp123` |
| `WEBSITE_SCM_ALWAYS_ON_ENABLED` | 読み取り専用です。 Always On が有効 (`1`) か無効 (`0`) かを示します。 ||
| `WEBSITE_SCM_SEPARATE_STATUS` | 読み取り専用です。 Kudu アプリが別のプロセスで実行されている (`1`) かいない (`0`) かを示します。 ||

<!-- 
WEBSITE_PROACTIVE_STACKTRACING_ENABLED
WEBSITE_CLOUD_NAME
WEBSITE_MAXIMUM_CONCURRENTCOLDSTARTS
HOME_EXPANDED
USERPROFILE
WEBSITE_ISOLATION
WEBSITE_OS | only appears on windows
WEBSITE_CLASSIC_MODE
 -->

## <a name="variable-prefixes"></a>変数プレフィックス

次の表に、App Service でさまざまな目的に使用される環境変数のプレフィックスを示します。

| 設定の名前 | 説明 |
|-|-|
| `APPSETTING_` | 変数が顧客によってアプリ構成のアプリ設定として設定されることを示します。 これは、アプリ設定として .NET アプリに挿入されます。 |
| `MAINSITE_` | 変数がアプリ自体に固有であることを示します。 |
| `SCMSITE_` | 変数が Kudu アプリに固有であることを示します。 |
| `SQLCONNSTR_` | アプリ構成内の SQL Server 接続文字列を示します。 これは、接続文字列として .NET アプリに挿入されます。 |
| `SQLAZURECONNSTR_` | アプリ構成の Azure SQL Database 接続文字列を示します。 これは、接続文字列として .NET アプリに挿入されます。 |
| `POSTGRESQLCONNSTR_` | アプリ構成の PostgreSQL 接続文字列を示します。 これは、接続文字列として .NET アプリに挿入されます。 |
| `CUSTOMCONNSTR_` | アプリ構成のカスタム接続文字列を示します。 これは、接続文字列として .NET アプリに挿入されます。 |
| `MYSQLCONNSTR_` | アプリ構成の Azure SQL Database 接続文字列を示します。 これは、接続文字列として .NET アプリに挿入されます。 |
| `AZUREFILESSTORAGE_` | Azure Files のコンテナー アプリのカスタム共有への接続文字列。 |
| `AZUREBLOBSTORAGE_` | Azure Blob Storage のコンテナー アプリのカスタム ストレージ アカウントへの接続文字列。 |
| `NOTIFICATIONHUBCONNSTR_` | Azure Notification Hubs の通知ハブへの接続文字列を示します。 |
| `SERVICEBUSCONNSTR_` | Azure Service Bus のインスタンスへの接続文字列を示します。 |
| `EVENTHUBCONNSTR_` | Azure Event Hubs のイベント ハブへの接続文字列を示します。 |
| `DOCDBCONNSTR_` | Azure Cosmos DB のデータベースへの接続文字列を示します。 |
| `REDISCACHECONNSTR_` | Azure Cache for Redis のキャッシュへの接続文字列を示します。 |
| `FILESHARESTORAGE_` | カスタム ファイル共有への接続文字列を示します。 |

## <a name="deployment"></a>デプロイ

次の環境変数は、アプリのデプロイに関連しています。 App Service のビルド オートメーションに関連する変数については、「[ビルド オートメーション](#build-automation)」を参照してください。

| 設定の名前| 説明 |
|-|-|
| `DEPLOYMENT_BRANCH`| [ローカル Git](deploy-local-git.md) または[クラウド Git](deploy-continuous-deployment.md) デプロイ (GitHub など) の場合は、Azure 内のデプロイするブランチに設定します。 既定では `master` です。 |
| `WEBSITE_RUN_FROM_PACKAGE`| ローカル ZIP パッケージからアプリを実行するには、`1` に設定します。リモート ZIP パッケージからアプリを実行するには、外部 URL の URL に設定します。 詳細については、「[ZIP パッケージから Azure App Service のアプリを直接実行する](deploy-run-package.md)」を参照してください。 |
| `WEBSITE_USE_ZIP` | 非推奨になりました。 `WEBSITE_RUN_FROM_PACKAGE` を使用してください。 |
| `WEBSITE_RUN_FROM_ZIP` | 非推奨になりました。 `WEBSITE_RUN_FROM_PACKAGE` を使用してください。 | 
| `WEBSITE_WEBDEPLOY_USE_SCM` | WebDeploy で Kudu デプロイ エンジンの使用を停止するには、`false` に設定します。 既定では、 `true`です。 Visual Studio (WebDeploy/MSDeploy) を使用して Linux アプリにデプロイするには、`false` に設定します。 |
| `MSDEPLOY_RENAME_LOCKED_FILES` | WebDeploy デプロイで DLL をコピーできない場合に名前の変更を試行するには、`1` に設定します。 `WEBSITE_WEBDEPLOY_USE_SCM` が `false` に設定されている場合、この設定は適用されません。 |
| `WEBSITE_DISABLE_SCM_SEPARATION` | 既定では、メイン アプリと Kudu アプリは異なるサンドボックスで実行されます。 アプリを停止すると、Kudu アプリは引き続き実行され、Git デプロイと MSDeploy を使用し続けることができます。 各アプリには固有のローカル ファイルがあります。 この分離をオフにする設定 (`true`) は、完全にはサポートされなくなったレガシ モードです。 |
| `WEBSITE_ENABLE_SYNC_UPDATE_SITE` | `1` に設定すると、`site` と `siteconfig` を更新する REST API 呼び出しが、すべてのインスタンスに完全に適用されてから戻るようになります。 ARM テンプレートを使用してデプロイする場合、後続の ARM 呼び出しでの競争状態を回避するため、既定値は `1` です。 |
| `WEBSITE_START_SCM_ON_SITE_CREATION` | ARM テンプレートのデプロイで、アプリの作成の一環として Kudu アプリを事前に開始するには、ARM テンプレートで `1` に設定します。 |
| `WEBSITE_START_SCM_WITH_PRELOAD` | Linux アプリの場合、Always On を有効にするときに URL に ping を実行して Kudu アプリを強制的にプリロードするには、`true` に設定します。 既定では、 `false`です。 Windows アプリの場合、Kudu アプリは常にプリロードされます。 |

<!-- 
WEBSITE_RUN_FROM_PACKAGE_BLOB_MI_RESOURCE_ID
-->

## <a name="build-automation"></a>ビルド オートメーション

# <a name="kudu-windows"></a>[Kudu (Windows)](#tab/kudu)

Kudu ビルド構成は、ネイティブ Windows アプリに適用され、Git ベース (または ZIP ベース) のデプロイの動作を制御するために使用されます。

| 設定の名前| 説明 | 例 |
|-|-|-|
| `SCM_BUILD_ARGS` | msbuild コマンド ラインの最後に、既定のコマンド ラインの直前の部分をオーバーライドする要素を追加します。 | クリーン ビルドを実行するには: `-t:Clean;Compile`|
| `SCM_SCRIPT_GENERATOR_ARGS` | Kudu では、[こちら](http://blog.amitapple.com/post/38418009331/azurewebsitecustomdeploymentpart2)で説明されている `azure site deploymentscript` コマンドを使用してデプロイ スクリプトが生成されます。 言語フレームワークの型が自動的に検出され、コマンドに渡すパラメーターが決定されます。 この設定は、自動的に生成されるパラメーターをオーバーライドします。 | リポジトリをプレーン コンテンツ ファイルとして扱うには: `--basic -p <folder-to-deploy>` |
| `SCM_TRACE_LEVEL` | ビルドのトレース レベル。 既定では、 `1`です。 より多くのトレースを行う場合は、大きい値 (最大 4) に設定します。 | `4` |
| `SCM_COMMAND_IDLE_TIMEOUT` | ビルド プロセスによって起動される各コマンドが出力を生成するまでに待機するタイムアウト (秒)。 その後、コマンドはアイドル状態と見なされ、強制終了されます。 既定値は `60` (1 分) です。 Azure では、230 秒後にクライアントを切断する一般的なアイドル要求タイムアウトも発生します。 ただし、サーバー側ではその後もコマンドの実行が継続されます。 | |
| `SCM_LOGSTREAM_TIMEOUT` | ログ ストリーミングを停止するまでの非アクティブ状態のタイムアウト (秒)。 既定値は `1800` (30 分) です。| |
| `SCM_SITEEXTENSIONS_FEED_URL` | サイト拡張機能ギャラリーの URL。 既定では、 `https://www.nuget.org/api/v2/`です。 以前のフィードの URL は `http://www.siteextensions.net/api/v2/` です。 | |
| `SCM_USE_LIBGIT2SHARP_REPOSITORY` | Git 操作に libgit2sharp ではなく git.exe を使用するには、`0` に設定します。 | |
| `WEBSITE_LOAD_USER_PROFILE` | ASP.NET のビルド オートメーション (Git デプロイなど) でエラー `The specified user does not have a valid profile.` が発生した場合は、この変数を `1` に設定して、ビルド環境に完全なユーザー プロファイルを読み込みます。 この設定は `WEBSITE_COMPUTE_MODE` が `Dedicated` の場合にのみ適用されます。 | |
| `WEBSITE_SCM_IDLE_TIMEOUT_IN_MINUTES` | SCM (Kudu) サイトのタイムアウト (分)。 既定では、 `20`です。 | |
| `SCM_DO_BUILD_DURING_DEPLOYMENT` | [ZIP デプロイ](deploy-zip.md)のデプロイ エンジンでは、ZIP ファイルがそのまま実行できる状態であり、ビルド オートメーションは実行されないことが想定されています。 [Git デプロイ](deploy-local-git.md)と同じビルド オートメーションを有効にするには、`true` に設定します。 |

<!-- 
SCM_GIT_USERNAME
SCM_GIT_EMAIL
 -->

# <a name="oryx-linux"></a>[Oryx (Linux)](#tab/oryx)

Oryx ビルド構成は、Linux アプリに適用され、Git ベース (または ZIP ベース) のデプロイの動作を制御するために使用されます。 [Oryx 構成](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)に関する記事をご覧ください。

-----

## <a name="language-specific-settings"></a>言語固有の設定

このセクションでは、サポートされている各言語フレームワークの構成可能なランタイム設定を示します。 デプロイ時の[ビルド オートメーション](#build-automation)で、追加設定を使用できます。

# <a name="net"></a>[.NET](#tab/dotnet)

<!-- 
| DOTNET_HOSTING_OPTIMIZATION_CACHE | 
 -->
| 設定の名前 | 説明 |
|-|-|
| `PORT` | 読み取り専用です。 Linux アプリの場合、.NET ランタイムがコンテナー内でリッスンするポート。 |
| `WEBSITE_ROLE_INSTANCE_ID` | 読み取り専用です。 現在のインスタンスの ID。 |
| `HOME` | 読み取り専用です。 共有ストレージを参照するディレクトリ (`/home`)。 |
| `DUMP_DIR` | 読み取り専用です。 クラッシュ ダンプのディレクトリ (`/home/logs/dumps`)。 |
| `APP_SVC_RUN_FROM_COPY` | Linux アプリのみ。 既定では、アプリはすべてのスケールアウトされたインスタンスの共有ディレクトリである `/home/site/wwwroot` から実行されます。 アプリをコンテナー内のローカル ディレクトリにコピーし、そこから実行するには、この変数を `true` に設定します。 このオプションを使用する場合は、`/home/site/wwwroot` への参照をハードコーディングしないようにしてください。 代わりに、`/home/site/wwwroot` からの相対パスを使用します。 |
| `MACHINEKEY_Decryption` | Windows ネイティブ アプリまたは Windows コンテナー アプリの場合、ASP.NET の暗号化ルーチンを有効にするためにこの変数がアプリの環境またはコンテナーに挿入されます (「[machineKey 要素](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100))」を参照してください)。 既定の `decryption` 値をオーバーライドするには、App Service アプリの設定として構成するか、*Web.config* ファイルの `machineKey` 要素に直接設定します。 |
| `MACHINEKEY_DecryptionKey` | Windows ネイティブ アプリまたは Windows コンテナー アプリの場合、ASP.NET の暗号化ルーチンを有効にするためにこの変数がアプリの環境またはコンテナーに挿入されます (「[machineKey 要素](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100))」を参照してください)。 自動生成された `decryptionKey` 値をオーバーライドするには、App Service アプリの設定として構成するか、*Web.config* ファイルの `machineKey` 要素に直接設定します。|
| `MACHINEKEY_Validation` | Windows ネイティブ アプリまたは Windows コンテナー アプリの場合、ASP.NET の暗号化ルーチンを有効にするためにこの変数がアプリの環境またはコンテナーに挿入されます (「[machineKey 要素](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100))」を参照してください)。 既定の `validation` 値をオーバーライドするには、App Service アプリの設定として構成するか、*Web.config* ファイルの `machineKey` 要素に直接設定します。|
| `MACHINEKEY_ValidationKey` | Windows ネイティブ アプリまたは Windows コンテナー アプリの場合、ASP.NET の暗号化ルーチンを有効にするためにこの変数がアプリの環境またはコンテナーに挿入されます (「[machineKey 要素](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100))」を参照してください)。 自動生成された `validationKey` 値をオーバーライドするには、App Service アプリの設定として構成するか、*Web.config* ファイルの `machineKey` 要素に直接設定します。|
<!-- | `USE_DOTNET_MONITOR` | if =true then /opt/dotnetcore-tools/dotnet-monitor collect --urls "http://0.0.0.0:50051 " --metrics true --metricUrls "http://0.0.0.0:50050" > /dev/null 2>&1 & -->

# <a name="java"></a>[Java](#tab/java)

| 設定の名前 | 説明 | 例 |
|-|-|-|
| `JAVA_HOME` | Java インストール ディレクトリのパス ||
| `JAVA_OPTS` | Java SE アプリの場合、`java` コマンドに渡される環境変数。 システム変数を含めることができます。 Tomcat の場合、`CATALINA_OPTS` を使用します。 | `-Dmysysproperty=%DRIVEPATH%` |
| `AZURE_JAVA_APP_PATH` | カスタム スクリプトでは、環境変数が使用される可能性があるため、ローカルにコピーされた後の app.jar の場所が指定されます | |
| `SKIP_JAVA_KEYSTORE_LOAD` | App Service を無効にして、キーストアに証明書が自動的に読み込まれないようにするには、値を 1 にします ||
| `WEBSITE_JAVA_JAR_FILE_NAME` | 使用する .jar ファイル。 文字列が .jar で終わらない場合は、.jar を追加します。 既定値は app.jar です ||
| `WEBSITE_JAVA_WAR_FILE_NAME` | 使用する .war ファイル。 文字列が .war で終わらない場合は、.war を追加します。 既定値は app.war です ||
| `JAVA_ARGS` | 異なる Java Web サーバーで必要な Java オプション。 既定値は `-noverify -Djava.net.preferIPv4Stack=true` です ||
| `JAVA_WEBSERVER_PORT_ENVIRONMENT_VARIABLES` | サーバー ポート用の一般的な Java Web フレームワークで使用される環境変数。 含まれるフレームワークには、Spring、Micronaut、Grails、MicroProfile Thorntail、Helidon、Ratpack、Quarkus などがあります ||
| `JAVA_TMP_DIR` | Java 引数に `-Dsite.tempdir` として追加されます。 既定値は `TEMP` です。 ||
| `WEBSITE_SKIP_LOCAL_COPY` | 既定では、デプロイされた app.jar は `/home/site/wwwroot` からローカルの場所にコピーされます。 この動作を無効にして、`/home/site/wwwroot` から直接 app.jar を読み込むには、この変数を `1` または `true` に設定します。 ローカル キャッシュが有効になっている場合、この設定は無効です。 | |
| `TOMCAT_USE_STARTUP_BAT` | ネイティブ Windows アプリのみ。 既定では、Tomcat サーバーはその `startup.bat` を使用して起動されます。 代わりに、その `catalina.bat` を使用して起動するには、`0` または `False` に設定します。 | `%LOCAL_EXPANDED%\tomcat` |
| `CATALINA_OPTS` | Tomcat アプリの場合、`java` コマンドに渡される環境変数。 システム変数を含めることができます。 | |
| `CATALINA_BASE` | カスタム Tomcat インストールを使用するには、インストールの場所に設定します。 | |
| `WEBSITE_JAVA_MAX_HEAP_MB` | Java の最大ヒープ (MB)。 この設定は、実験的な Tomcat バージョンが使用されている場合にのみ有効です。 | |
| `WEBSITE_DISABLE_JAVA_HEAP_CONFIGURATION` | `WEBSITE_JAVA_MAX_HEAP_MB` を手動で無効にするには、この変数を `true` または `1` に設定します。 | |
| `WEBSITE_AUTH_SKIP_PRINCIPAL` | 既定では、組み込みの[認証](overview-authentication-authorization.md)を有効にしたときに、次の Tomcat [HttpServletRequest インターフェイス](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html)が取り込まれます: `isSecure`、`getRemoteAddr`、`getRemoteHost`、`getScheme`、`getServerPort`。 無効にするには、`1` に設定します。  | |
| `WEBSITE_SKIP_FILTERS` | App Service によって追加されたすべてのサーブレット フィルターを無効にするには、`1` に設定します。 ||
| `IGNORE_CATALINA_BASE` | 既定では、App Service で Tomcat 変数 `CATALINA_BASE` が定義されているかどうかが確認されます。 ない場合は、`%HOME%\tomcat\conf\server.xml` が存在するかどうか検索されます。 このファイルが存在する場合は、`CATALINA_BASE` が `%HOME%\tomcat` に設定されます。 この動作を無効にして、`CATALINA_BASE` を削除するには、この変数を `1` または `true` に設定します。 ||
| `PORT` | 読み取り専用です。 Linux アプリの場合、Java ランタイムがコンテナー内でリッスンするポート。 | |
| `WILDFLY_VERSION` | 読み取り専用です。 JBoss (Linux) アプリの場合、WildFly のバージョン。 | |
| `TOMCAT_VERSION` | 読み取り専用です。 Linux Tomcat アプリの場合、Tomcat のバージョン。 ||
| `JBOSS_HOME` | 読み取り専用です。 JBoss (Linux) アプリの場合、WildFly のインストール パス。 | |
| `AZURE_JETTY9_CMDLINE` | 読み取り専用です。 ネイティブ Windows アプリの場合、Jetty 9 を起動するためのコマンドライン引数。 | |
| `AZURE_JETTY9_HOME` | 読み取り専用です。 ネイティブ Windows アプリの場合、Jetty 9 のインストール パス。| |
| `AZURE_JETTY93_CMDLINE` | 読み取り専用です。 ネイティブ Windows アプリの場合、Jetty 9.3 を起動するためのコマンドライン引数。 | |
| `AZURE_JETTY93_HOME` | 読み取り専用です。 ネイティブ Windows アプリの場合、Jetty 9.3 のインストール パス。 | |
| `AZURE_TOMCAT7_CMDLINE` | 読み取り専用です。 ネイティブ Windows アプリの場合、Tomcat 7 を起動するためのコマンドライン引数。 | |
| `AZURE_TOMCAT7_HOME` | 読み取り専用です。 ネイティブ Windows アプリの場合、Tomcat 7 のインストール パス。 | |
| `AZURE_TOMCAT8_CMDLINE` | 読み取り専用です。 ネイティブ Windows アプリの場合、Tomcat 8 を起動するためのコマンドライン引数。 | |
| `AZURE_TOMCAT8_HOME` | 読み取り専用です。 ネイティブ Windows アプリの場合、Tomcat 8 のインストール パス。 | |
| `AZURE_TOMCAT85_CMDLINE` | 読み取り専用です。 ネイティブ Windows アプリの場合、Tomcat 8.5 を起動するためのコマンドライン引数。 | |
| `AZURE_TOMCAT85_HOME` | 読み取り専用です。 ネイティブ Windows アプリの場合、Tomcat 8.5 のインストール パス。 | |
| `AZURE_TOMCAT90_CMDLINE` | 読み取り専用です。 ネイティブ Windows アプリの場合、Tomcat 9 を起動するためのコマンドライン引数。 | |
| `AZURE_TOMCAT90_HOME` | 読み取り専用です。 ネイティブ Windows アプリの場合、Tomcat 9 のインストール パス。 | |
| `AZURE_SITE_HOME` | Java 引数に `-Dsite.home` として追加される値。 既定値は、`HOME` の値です。 | |
| `HTTP_PLATFORM_PORT` | Java 引数に `-Dport.http` として追加されます。 さまざまな Java Web フレームワークで使用される次の環境変数もこの値に設定されます: `SERVER_PORT`、`MICRONAUT_SERVER_PORT`、`THORNTAIL_HTTP_PORT`、`RATPACK_PORT`、`QUARKUS_HTTP_PORT`、`PAYARAMICRO_PORT`。 ||
| `AZURE_LOGGING_DIR` | ネイティブ Windows アプリのみ。 Java 引数に `-Dsite.logdir` として追加されます。 既定では、 `%HOME%\LogFiles\`です。 ||

<!-- 
WEBSITE_JAVA_COPY_ALL
AZURE_SITE_APP_BASE
 -->

# <a name="nodejs"></a>[Node.js](#tab/node)

| 設定の名前 | 説明 |
|-|-|
| `PORT` | 読み取り専用です。 Linux アプリの場合、Node.js アプリがコンテナー内でリッスンするポート。 |
| `WEBSITE_ROLE_INSTANCE_ID` | 読み取り専用です。 現在のインスタンスの ID。 |
| `PM2HOME` | |
| `WEBSITE_NODE_DEFAULT_VERSION` | ネイティブ Windows アプリの場合、アプリで使用されている既定のノード バージョン。 ここでは、[サポートされている Node.js バージョン](configure-language-nodejs.md#show-nodejs-version)のいずれかを使用できます。 |

<!-- APPSVC_REMOTE_DEBUGGING
APPSVC_REMOTE_DEBUGGING_BREAK
APPSVC_TUNNEL_PORT -->

# <a name="python"></a>[Python](#tab/python)

| 設定の名前 | 説明 |
|-|-|
| `APPSVC_VIRTUAL_ENV` | 読み取り専用です。 |
| `PORT` | 読み取り専用です。 Linux アプリの場合、Python アプリがコンテナー内でリッスンするポート。 |

<!-- APPSVC_REMOTE_DEBUGGING
APPSVC_TUNNEL_PORT | -debugAdapter ptvsd -debugPort $APPSVC_TUNNEL_PORT"
APPSVC_REMOTE_DEBUGGING_BREAK | debugArgs+=" -debugWait" -->

# <a name="php"></a>[PHP](#tab/php)

| 設定の名前 | 説明 | 例|
|-|-|-|
| `PHP_Extensions` | PHP 拡張機能のコンマ区切りリスト。 | `extension1.dll,extension2.dll,Name1=value1` |
| `PHP_ZENDEXTENSIONS` | Windows ネイティブ アプリの場合、XDebug 拡張機能のパス (`D:\devtools\xdebug\2.6.0\php_7.2\php_xdebug-2.6.0-7.2-vc15-nts.dll` など) に設定します。 Linux アプリの場合、PHP コンテナーの XDebug バージョンを使用するには、`xdebug` に設定します。 ||
| `PHP_VERSION` | 読み取り専用です。 選択された PHP バージョン。 ||
| `PORT` | 読み取り専用です。 Apache サーバーがコンテナー内でリッスンするポート。 ||
| `WEBSITE_ROLE_INSTANCE_ID` | 読み取り専用です。 現在のインスタンスの ID。 ||
| `WEBSITE_PROFILER_ENABLE_TRIGGER` | 既定の `php.ini` に `xdebug.profiler_enable_trigger=1` と `xdebug.profiler_enable=0` を追加するには、`TRUE` に設定します。 ||
| `WEBSITE_ENABLE_PHP_ACCESS_LOGS` | サーバーに対する要求をログに記録するには、`TRUE` に設定します (`CustomLog \dev\stderr combined` が `/etc/apache2/apache2.conf` に追加されます)。 ||
| `APACHE_SERVER_LIMIT` | Apache 固有の変数。 既定では、 `1000`です。 ||
| `APACHE_MAX_REQ_WORKERS` | Apache 固有の変数。 既定では、 `256`です。 ||

<!-- 
ZEND_BIN_PATH
MEMCACHESHIM_REDIS_ENABLE
MEMCACHESHIM_PORT 
APACHE_LOG_DIR | RUN sed -i 's!ErrorLog ${APACHE_LOG_DIR}/error.log!ErrorLog /dev/stderr!g' /etc/apache2/apache2.conf 
APACHE_RUN_USER | RUN sed -i 's!User ${APACHE_RUN_USER}!User www-data!g' /etc/apache2/apache2.conf 
APACHE_RUN_GROUP | RUN sed -i 's!User ${APACHE_RUN_GROUP}!Group www-data!g' /etc/apache2/apache2.conf  
-->

# <a name="ruby"></a>[Ruby](#tab/ruby)

| 設定の名前 | 説明 | 例 |
|-|-|-|
| `PORT` | 読み取り専用です。 Rails アプリがコンテナー内でリッスンするポート。 ||
| `WEBSITE_ROLE_INSTANCE_ID` | 読み取り専用です。 現在のインスタンスの ID。 ||
| `RAILS_IGNORE_SPLASH` | 既定では、Gemfile が見つからないときに既定のスプラッシュ ページが表示されます。 スプラッシュ ページを無効にするには、この変数を任意の値に設定します。 ||
| `BUNDLE_WITHOUT` | `bundle install` に `--without` オプションを追加するには、この変数を除外するグループ (スペース区切り) に設定します。 既定では、すべての gem がインストールされます。 | `test development` |
| `BUNDLE_INSTALL_LOCATION` | gem をインストールするディレクトリ。 既定では、 `/tmp/bundle`です。 ||
| `RUBY_SITE_CONFIG_DIR` | サイト構成ディレクトリ。 既定では、 `/home/site/config`です。 コンテナーでは、このディレクトリに ZIP 形式の gem があるかどうかを確認します。 ||
| `SECRET_KEY_BASE` | 既定では、ランダムな秘密キー ベースが生成されます。 カスタム秘密キー ベースを使用するには、この変数を目的のキー ベースに設定します。 ||
| `RAILS_ENV` | Rails 環境。 既定では、 `production`です。 ||
| `GEM_PRISTINE` | `gem pristine --all` を実行するには、この変数を任意の値に設定します。 ||

-----

## <a name="domain-and-dns"></a>ドメインと DNS

| 設定の名前| 説明 | 例 |
|-|-|-|
| `WEBSITE_DNS_SERVER` | (バックエンド サービスなどへの) 送信接続用のプライマリ DNS サーバーの IP アドレス。 App Service の既定の DNS サーバーは Azure DNS で、その IP アドレスは `168.63.129.16` です。 アプリで [VNet 統合](./overview-vnet-integration.md)が使用されているか、アプリが [App Service 環境](environment/intro.md)内にある場合、既定では VNet から DNS サーバー構成が継承されます。 | `10.0.0.1` |
| `WEBSITE_DNS_ALT_SERVER` | 発信接続の代替 DNS サーバーの IP アドレス。 「`WEBSITE_DNS_SERVER`」を参照してください。 | |

<!-- 
DOMAIN_OWNERSHIP_VERIFICATION_IDENTIFIERS
 -->

## <a name="tlsssl"></a>TLS/SSL

詳細については、「[Azure App Service の自分のコードから TLS/SSL 証明書を使用する](configure-ssl-certificate-in-code.md)」を参照してください。

| 設定の名前| 説明 |
|-|-|
| `WEBSITE_LOAD_CERTIFICATES` | コードに読み込む証明書の拇印のコンマ区切り値。すべての証明書をコードに読み込むことができるようにする場合は、`*`。 [アプリに追加された証明書](configure-ssl-certificate.md)のみを読み込むことができます。 |
| `WEBSITE_PRIVATE_CERTS_PATH` | 読み取り専用です。 Windows コンテナー内の、読み込まれたプライベート証明書へのパス。 |
| `WEBSITE_PUBLIC_CERTS_PATH` | 読み取り専用です。 Windows コンテナー内の、読み込まれた公開証明書へのパス。 |
| `WEBSITE_INTERMEDIATE_CERTS_PATH` | 読み取り専用です。 Windows コンテナー内の、読み込まれた中間証明書へのパス。 |
| `WEBSITE_ROOT_CERTS_PATH` | 読み取り専用です。 Windows コンテナー内の、読み込まれたルート証明書へのパス。 |

## <a name="deployment-slots"></a>デプロイ スロット 

デプロイ スロットの詳細については、「[Azure App Service でステージング環境を設定する](deploy-staging-slots.md)」を参照してください。

| 設定の名前| 説明 | 例 |
|-|-|-|
|`WEBSITE_SLOT_NAME`| 読み取り専用です。 現在のデプロイ スロットの名前。 運用スロットの名前は `Production` です。 ||
|`WEBSITE_OVERRIDE_STICKY_EXTENSION_VERSIONS`| 既定では、サイト拡張機能のバージョンは各スロットに固有です。 これにより、スワップ後に拡張機能のバージョンが変更されるために発生する予期しないアプリケーション動作を防ぐことができます。 拡張機能のバージョンもスワップする場合は、"*すべてのスロット*" で `1` に設定します。 ||
|`WEBSITE_OVERRIDE_PRESERVE_DEFAULT_STICKY_SLOT_SETTINGS`| 特定の設定を、[既定で固定またはスワップ不可能](deploy-staging-slots.md#which-settings-are-swapped)として指定します。 既定値は `true` です。 代わりに "*すべてのデプロイ スロット*" をスワップ可能にするには、それらに対してこの設定を `false` または `0` に設定します。 特定の設定の種類を細かく制御することはできません。 ||
|`WEBSITE_SWAP_WARMUP_PING_PATH`| ターゲット スロットをウォームアップするために ping を実行するパス (スラッシュで始まる)。 既定値は `/` で、HTTP 経由でルート パスに ping を実行します。 | `/statuscheck` |
|`WEBSITE_SWAP_WARMUP_PING_STATUSES`| スワップ中のウォームアップ操作の有効な HTTP 応答コード。 返された状態コードが一覧にない場合、ウォームアップとスワップの操作が停止されます。 既定で、すべての応答コードは有効です。 | `200,202` |
| `WEBSITE_SLOT_NUMBER_OF_TIMEOUTS_BEFORE_RESTART` | スロット スワップ中に特定の VM インスタンス上のサイトを強制的に再起動するまでのタイムアウトの最大回数。 既定では、 `3`です。 ||
| `WEBSITE_SLOT_MAX_NUMBER_OF_TIMEOUTS` | スロット スワップ中に 1 つの URL に対するタイムアウト要求を中止するまでの最大回数。 既定では、 `5`です。 ||
| `WEBSITE_SKIP_ALL_BINDINGS_IN_APPHOST_CONFIG` | `applicationHost.config` 内のすべてのバインドをスキップするには、`true` または `1` に設定します。 既定では、 `false`です。 `applicationHost.config` がスロットのスワップされたホスト名で更新されるためにアプリの再起動がトリガーされる場合は、このような再起動を回避するため、この変数を `true` に設定します。 Windows Communication Foundation (WCF) アプリを実行している場合は、この変数を設定しないでください。 ||

<!-- 
|`WEBSITE_SWAP_SLOTNAME`||| 
-->

## <a name="custom-containers"></a>カスタム コンテナー

カスタム コンテナーの詳細については、「[Azure でカスタム コンテナーを実行する](quickstart-custom-container.md)」を参照してください。

| 設定の名前| 説明 | 例 |
|-|-|-|
| `WEBSITES_ENABLE_APP_SERVICE_STORAGE` | スケーリングされたインスタンス間で `/home` ディレクトリを共有できるようにするには、`true` に設定します。 カスタム コンテナーの場合、既定値は `false` です。 ||
| `WEBSITES_CONTAINER_START_TIME_LIMIT` | コンテナーを再起動する前に、コンテナーの起動が完了するまで待機する時間 (秒)。 既定値は `230` です。 最大 `1800` まで増やすことができます。 ||
| `DOCKER_REGISTRY_SERVER_URL` | App Service でカスタム コンテナーを実行する場合のレジストリ サーバーの URL。 セキュリティのため、この変数はコンテナーに渡されません。 | `https://<server-name>.azurecr.io` |
| `DOCKER_REGISTRY_SERVER_USERNAME` | `DOCKER_REGISTRY_SERVER_URL` のレジストリ サーバーで認証するためのユーザー名。 セキュリティのため、この変数はコンテナーに渡されません。 ||
| `DOCKER_REGISTRY_SERVER_PASSWORD` | `DOCKER_REGISTRY_SERVER_URL` のレジストリ サーバーで認証するためのパスワード。 セキュリティのため、この変数はコンテナーに渡されません。 ||
| `WEBSITES_WEB_CONTAINER_NAME` | Docker Compose アプリでは、インターネットにアクセスできるコンテナーは 1 つだけです。 既定のコンテナーの選択をオーバーライドするには、構成ファイルで定義されているコンテナーの名前に設定します。 インターネットにアクセスできるコンテナーは、既定ではポート 80 または 8080 を定義する最初のコンテナーです。このコンテナーが見つからない場合は、構成ファイルで定義されている最初のコンテナーです。 |  |
| `WEBSITES_PORT` | カスタム コンテナーの場合、要求のルーティング先の App Service のコンテナーのカスタム ポート番号。 既定では、App Service でポート 80 および 8080 の自動ポート検出が試行されます。 この設定は、環境変数としてコンテナーに挿入 *されません*。 ||
| `WEBSITE_CPU_CORES_LIMIT` | 既定では、Windows コンテナーは、選択した価格レベルで使用できるすべてのコアで実行されます。 コア数を減らすには、必要なコア数の制限に設定します。 詳細については、「[コンピューティング コアの数をカスタマイズする](configure-custom-container.md?pivots=container-windows#customize-the-number-of-compute-cores)」を参照してください。||
| `WEBSITE_MEMORY_LIMIT_MB` | 既定では Azure App Service にデプロイされるすべての Windows コンテナーは、1 GB の RAM に制限されます。 目的のメモリ制限 (MB) に設定します。 同じプラン内のアプリ間でこの設定を累計した値が、選択した価格レベルで許可されている量を超えないようにする必要があります。 詳細については、「[コンテナー メモリをカスタマイズする](configure-custom-container.md?pivots=container-windows#customize-container-memory)」を参照してください。 ||
| `CONTAINER_WINRM_ENABLED` | Windows コンテナー アプリの場合、Windows リモート管理 (WIN-RM) 有効にするには、`1` に設定します。 ||

<!-- 
CONTAINER_ENCRYPTION_KEY
CONTAINER_NAME
CONTAINER_IMAGE_URL
AzureWebEncryptionKey
CONTAINER_START_CONTEXT_SAS_URI
CONTAINER_AZURE_FILES_VOLUME_MOUNT_PATH
CONTAINER_START_CONTEXT
DOCKER_ENABLE_CI
WEBSITE_DISABLE_PRELOAD_HANG_MITIGATION
 -->

## <a name="scaling"></a>Scaling

| 設定の名前| 説明 |
|-|-|
| `WEBSITE_INSTANCE_ID` | 読み取り専用です。 アプリが複数のインスタンスにスケールアウトされている場合の、現在の VM インスタンスの一意の ID。 |
| `WEBSITE_IIS_SITE_NAME` | 非推奨になりました。 `WEBSITE_INSTANCE_ID` を使用してください。 |
| `WEBSITE_DISABLE_OVERLAPPED_RECYCLING` | 重複したリサイクルにより、アプリの現在の VM インスタンスがシャットダウンされる前に、新しい VM インスタンスが開始されます。 場合によっては、ファイル ロックの問題が発生する可能性があります。 `1` に設定して、これを無効にしてみることができます。 |
| `WEBSITE_DISABLE_CROSS_STAMP_SCALE` | 既定では、アプリで Azure Files または Docker コンテナーを使用する場合、複数のスタンプにまたがってアプリをスケーリングできます。 アプリのリージョン内でスタンプ間スケーリングを無効にするには、`1` または `true` に設定します。 既定では、 `0`です。 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` を `true` または `1` に設定するカスタム Docker コンテナーでは、コンテンツが Docker コンテナーに完全にカプセル化されていないため、スタンプ間スケーリングができません。 |

## <a name="logging"></a>ログ記録

| 設定の名前| 説明 | 例 |
|-|-|-|
| `WEBSITE_HTTPLOGGING_ENABLED` | 読み取り専用です。 Windows ネイティブ アプリに関する Web サーバー ログが有効 (`1`) か無効 (`0`) かを示します。 ||
| `WEBSITE_HTTPLOGGING_RETENTION_DAYS` | Web サーバー ログが有効になっている場合、Windows ネイティブ アプリに関する Web サーバー ログの保有期間 (日数)。 | `10` |
| `WEBSITE_HTTPLOGGING_CONTAINER_URL` | Web サーバー ログが有効になっている場合、Windows ネイティブ アプリに関する Web サーバー ログを格納する BLOB ストレージ コンテナーの SAS URL。 設定されていない場合、Web サーバー ログはアプリのファイル システム (既定の共有ストレージ) に格納されます。 | |
| `DIAGNOSTICS_AZUREBLOBRETENTIONINDAYS` | アプリケーション ログが有効になっている場合、Windows ネイティブ アプリに関するアプリケーション ログの保有期間 (日数)。 | `10` |
| `DIAGNOSTICS_AZUREBLOBCONTAINERSASURL` | アプリケーション ログが有効になっている場合、Windows ネイティブ アプリに関するアプリケーション ログを格納する BLOB ストレージ コンテナーの SAS URL。 | |
| `APPSERVICEAPPLOGS_TRACE_LEVEL` | [AppServiceAppLogs](troubleshoot-diagnostic-logs.md#supported-log-types) ログの種類について Log Analytics に送信される最小ログ レベル。 | |
| `DIAGNOSTICS_LASTRESORTFILE` | リスナーのトラブルシューティングに役立つ内部エラーをログに記録するために作成するファイル名、またはログ ディレクトリへの相対パス。 既定では、 `logging-errors.txt`です。 ||
| `DIAGNOSTICS_LOGGINGSETTINGSFILE` | ログ設定ファイルの、`D:\home` または `/home` との相対パス。 既定では、 `site\diagnostics\settings.json`です。 | |
| `DIAGNOSTICS_TEXTTRACELOGDIRECTORY` | アプリのルート (`D:\home\site\wwwroot` または `/home/site/wwwroot`) を基準にしたログ フォルダー。 | `..\..\LogFiles\Application`|
| `DIAGNOSTICS_TEXTTRACEMAXLOGFILESIZEBYTES` | ログ ファイルの最大サイズ (バイト)。 既定値は `131072` (128 KB) です。 ||
| `DIAGNOSTICS_TEXTTRACEMAXLOGFOLDERSIZEBYTES` | ログ フォルダーの最大サイズ (バイト)。 既定値は `1048576` (1 MB) です。 ||
| `DIAGNOSTICS_TEXTTRACEMAXNUMLOGFILES` | 保持するログ ファイルの最大数。 既定では、 `20`です。 | |
| `DIAGNOSTICS_TEXTTRACETURNOFFPERIOD` | アプリケーション ログの有効な状態を維持するタイムアウト (ミリ秒)。 既定値は `43200000` (12 時間) です。 ||
| `WEBSITE_LOG_BUFFERING` | 既定では、ログ バッファーは有効になっています。 無効にするには、`0` に設定します。 ||
| `WEBSITE_ENABLE_PERF_MODE` | Windows ネイティブ アプリの場合、10 分以内に返された成功した要求の IIS ログ エントリをオフにするには、`TRUE` に設定します。 これは、拡張ログを削除することでパフォーマンス ベンチマークを行う簡単な方法です。 ||

<!-- 
| `DIAGNOSTICS_AZURETABLESASURL` | old? | |
| WEBSITE_APPSERVICEAPPLOGS_TRACE_ENABLED | Read-only. Added when | | 
| AZMON_LOG_CATEGORY_APPSERVICEAPPLOGS_ENABLED | Read-only. Shows when the AppServiceAppLogs category in Azure Monitor settings is enabled. |
AZMON_LOG_CATEGORY_APPSERVICEPLATFORMLOGS_ENABLED  | Read-only. Shows when the AppServiceAppLogs category in Azure Monitor settings is enabled. |
AZMON_LOG_CATEGORY_APPSERVICECONSOLELOGS_ENABLED | Read-only. Shows when the AppServiceConsoleLogs category in Azure Monitor settings is enabled. |
WEBSITE_FUNCTIONS_AZUREMONITOR_CATEGORIES
WINDOWS_TRACING_FLAGS
WINDOWS_TRACING_LOGFILE
WEBSITE_FREB_DISABLE
WEBSITE_ARR_SESSION_AFFINITY_DISABLE

-->

## <a name="performance-counters"></a>パフォーマンス カウンター

以下は、列挙しても存在しない「偽りの」環境変数ですが、個別に参照するとその値が返されます。 値は動的であり、参照のたびに変わる可能性があります。

| 設定の名前| 説明 |
|-|-|
| `WEBSITE_COUNTERS_ASPNET` | ASP.NET のパフォーマンス カウンターを含む JSON オブジェクト。 |
| `WEBSITE_COUNTERS_APP` | サンドボックスのカウンターを含む JSON オブジェクト。 |
| `WEBSITE_COUNTERS_CLR` | CLR のカウンターを含む JSON オブジェクト。 |
| `WEBSITE_COUNTERS_ALL` | 他の 3 つの変数の組み合わせを含む JSON オブジェクト。 |

## <a name="caching"></a>キャッシュ

| 設定の名前| 説明 |
|-|-|
| `WEBSITE_LOCAL_CACHE_OPTION` | ローカル キャッシュが有効かどうか。 使用できるオプションは次のとおりです。 <br/>- `Default`: スタンプレベルのグローバル設定を継承します。<br/>- `Always`: アプリで有効にします。<br/>- OnStorageUnavailability<br/>- `Disabled`: アプリで無効にします。 |
| `WEBSITE_LOCAL_CACHE_READWRITE_OPTION` | ローカル キャッシュの読み取り/書き込みオプション。 使用できるオプションは次のとおりです。 <br/>- `ReadOnly`: キャッシュは読み取り専用です。<br/>- `WriteWithCopyBack`: ローカル キャッシュへの書き込みを許可し、共有ストレージに定期的にコピーします。 SCM サイトではローカル キャッシュが参照されるため、単一インスタンス アプリにのみ適用されます。<br/>- `WriteButDiscardChanges`: ローカル キャッシュへの書き込みを許可しますが、ローカルで行われた変更は破棄します。 |
| `WEBSITE_LOCAL_CACHE_SIZEINMB` | ローカル キャッシュのサイズ (MB)。 既定値は `1000` (1 GB) です。 |
| `WEBSITE_LOCALCACHE_READY` | アプリでローカル キャッシュが使用されるかどうかを示す読み取り専用フラグ。 |
| `WEBSITE_DYNAMIC_CACHE` | 複数インスタンスのアクセスを許可するネットワーク ファイルの共有の性質により、動的キャッシュでは、最近アクセスしたファイルをインスタンス上でローカルにキャッシュすることで、パフォーマンスが向上します。 ファイルが変更された場合、キャッシュは無効になります。 キャッシュの場所は `%SYSTEMDRIVE%\local\DynamicCache` です (同じ `%SYSTEMDRIVE%\local` クォータが適用されます)。 既定では、完全なコンテンツ キャッシュが有効になっています (`1` に設定されています)。これには、ファイルのコンテンツとディレクトリ/ファイルのメタデータ (タイムスタンプ、サイズ、ディレクトリ コンテンツ) の両方が含まれます。 ローカル ディスクの使用量を節約するには、`2` に設定して、ディレクトリ/ファイルのメタデータ (タイムスタンプ、サイズ、ディレクトリ コンテンツ) のみをキャッシュします。 キャッシュを無効にするには、`0` に設定します。 |
| `WEBSITE_READONLY_APP` | 動的キャッシュを使用する場合は、この変数を `1` に設定して、アプリ ルート (`D:\home\site\wwwroot` または `/home/site/wwwroot`) への書き込みアクセスを無効にできます。 ロックされたファイルによってデプロイがブロックされないように、`App_Data` ディレクトリを除き、排他的ロックは許可されません。 |

<!-- 
HTTP_X_LOCALCACHE_READY_CHECK
HTTP_X_APPINIT_CHECK
X_SERVER_ROUTED
HTTP_X_MS_REQUEST_ID
HTTP_X_MS_APIM_HOST
HTTP_X_MS_FORWARD_HOSTNAMES
HTTP_X_MS_FORWARD_TOKEN
HTTP_MWH_SECURITYTOKEN
IS_SERVICE_ENDPOINT
VNET_CLIENT_IP
HTTP_X_MS_SITE_RESTRICTED_TOKEN
HTTP_X_FROM
| LOCAL_ADDR | internal private IP address of app |
SERVERS_FOR_HOSTING_SERVER_PROVIDER
NEED_PLATFORM_AUTHORIZATION
TIP_VALUE
TIP_RULE_NAME
TIP_RULE_MAX_AGE
REWRITE_PATH
NEGOTIATE_CLIENT_CERT
| CLIENT_CERT_MODE | used with ClientCertEnabled. Required means ClientCert is required.  Optional means ClientCert is optional or accepted. OptionalInteractiveUser is similar to Optional; however, it will not ask user for Certificate in Browser Interactive scenario.|
| HTTPS_ONLY | set with terraform? |
 -->

## <a name="networking"></a>ネットワーク

次の環境変数は、[ハイブリッド接続](app-service-hybrid-connections.md)および [VNet 統合](./overview-vnet-integration.md)に関連しています。

| 設定の名前 | 説明 |
|-|-|
| `WEBSITE_RELAYS` | 読み取り専用です。 ハイブリッド接続を構成するために必要なデータ (エンドポイントと Service Bus データを含む)。 |
| `WEBSITE_REWRITE_TABLE` | 読み取り専用です。 実行時に参照を実行し、接続を適切に書き換えるために使用されます。 | 
| `WEBSITE_VNET_ROUTE_ALL` | 既定では、[リージョン VNet 統合](./overview-vnet-integration.md#regional-virtual-network-integration)を使用する場合、アプリでは RFC1918 トラフィックのみを VNet にルーティングします。 すべての送信トラフィックを VNet にルーティングし、同じ NSG と UDR を適用するには、`1` に設定します。 この設定を使用すると、VNet を介して RFC1918 以外のエンドポイントにアクセスし、アプリから送信されるすべての送信トラフィックをセキュリティで保護し、すべての送信トラフィックを選択したネットワーク アプライアンスに強制的にトンネリングすることができます。 |
| `WEBSITE_PRIVATE_IP` | 読み取り専用です。 [VNet](./overview-vnet-integration.md) と統合されている場合に、アプリに関連付けられている IP アドレス。 リージョン VNet 統合の場合、この値は委任されたサブネットのアドレス範囲の IP になります。また、ゲートウェイが必要な VNet 統合の場合、この値は仮想ネットワーク ゲートウェイに構成されているポイント対サイトのアドレス プールのアドレス範囲の IP になります。 この IP は、アプリで VNet を介してリソースに接続するために使用されます。 また、説明したアドレス範囲内で変更される可能性があります。 |
| `WEBSITE_PRIVATE_PORTS` | 読み取り専用です。 VNet 統合では、アプリで他のノードと通信するために使用できるポートを示します。 |

<!-- | WEBSITE_SLOT_POLL_WORKER_FOR_CHANGE_NOTIFICATION | Poll worker before pinging the site to detect when change notification has been processed. |
WEBSITE_SPECIAL_CACHE
WEBSITE_SOCKET_STATISTICS_ENABLED
| `WEBSITE_ENABLE_NETWORK_HEALTHCHECK` | Enable network health checks that won't be blocked by CORS or built-in authentication. Three check methods can be utilized: <br/>- Ping an IP address (configurable by `WEBSITE_NETWORK_HEALTH_IPADDRS`). <br/>- Check DNS resolution (configurable by `WEBSITE_NETWORK_HEALTH_DNS_ENDPOINTS`). <br/>- Poll URI endpoints (configurable by `WEBSITE_NETWORK_HEALTH_URI_ENDPOINTS`).<br/> |
| `WEBSITE_NETWORK_HEALTH_IPADDRS` | https://msazure.visualstudio.com/One/_git/AAPT-Antares-EasyAuth/pullrequest/3763264 |
| `WEBSITE_NETWORK_HEALTH_DNS_ENDPOINTS` | |
| `WEBSITE_NETWORK_HEALTH_URI_ENDPOINTS` | |
| `WEBSITE_NETWORK_HEALTH_INTEVALSECS` | Interval of the network health check in seconds. The minimum value is 30 seconds. | |
| `WEBSITE_NETWORK_HEALTH_TIMEOUT_INTEVALSECS` | Time-out of the network health check in seconds. | |

-->
<!-- | CONTAINER_WINRM_USERNAME |
| CONTAINER_WINRM_PASSWORD| -->

## <a name="key-vault-references"></a>Key Vault 参照

次の環境変数は、[Key Vault 参照](app-service-key-vault-references.md)に関連しています。

| 設定の名前 | 説明 |
|-|-|
| `WEBSITE_KEYVAULT_REFERENCES` | 読み取り専用です。 アプリで現在構成されているすべての Key Vault 参照の情報 (状態を含む) が含まれています。 |
| `WEBSITE_SKIP_CONTENTSHARE_VALIDATION` | (`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` を使用して) アプリの共有ストレージ接続を Key Vault 参照に設定した場合、アプリの作成または更新時に次のいずれかの条件に当てはまる場合は、Key Vault 参照を解決できません。 <br/>- アプリでシステム割り当て ID を使用してキー コンテナーにアクセスする。<br/>- アプリでユーザー割り当て ID を使用してキー コンテナーにアクセスし、キー コンテナーが [VNet でロックされている](../key-vault/general/overview-vnet-service-endpoints.md)。<br/>作成または更新時のエラーを回避するには、この変数を `1` に設定します。 |
| `WEBSITE_DELAY_CERT_DELETION` | ユーザーは、ワーカー プロセスが依存する証明書がプロセスの終了時まで削除されないようにするため、この環境変数を 1 に設定できます。 |
<!-- | `WEBSITE_ALLOW_DOUBLE_ESCAPING_URL` | TODO | -->

## <a name="cors"></a>CORS

次の環境変数は、クロスオリジン リソース共有 (CORS) の構成に関連しています。

| 設定の名前 | 説明 |
|-|-|
| `WEBSITE_CORS_ALLOWED_ORIGINS` | 読み取り専用です。 CORS で許可されているオリジンを示します。 |
| `WEBSITE_CORS_SUPPORT_CREDENTIALS` | 読み取り専用です。 `Access-Control-Allow-Credentials` ヘッダーを `true` に設定することが有効 (`True`) か無効 (`False`) かを示します。 |

## <a name="authentication--authorization"></a>認証と承認

次の環境変数は、[App Service 認証](overview-authentication-authorization.md)に関連しています。

| 設定の名前| 説明|
|-|-|
| `WEBSITE_AUTH_DISABLE_IDENTITY_FLOW`  | `true` に設定すると、ASP.NET ベースの Web アプリケーション (V1 関数アプリを含む) のスレッド プリンシパル ID の割り当てが無効になります。 これは、開発者が認証を使用してサイトへのアクセスを保護しながら、アプリ ロジック内で別のログイン メカニズムを使用できるように設計されています。 既定では、 `false`です。 |
| `WEBSITE_AUTH_HIDE_DEPRECATED_SID` | `true` または `false`。 既定値は `false` です。 これは、Azure App Service のレガシである Azure Mobile Apps 統合用の設定です。 これを `true` に設定すると、認証されたユーザーが自分のプロファイル情報を変更した場合に、そのユーザー用に生成された SID (セキュリティ ID) が変更されるという問題が解決されます。 この値を変更すると、既存の Azure Mobile Apps のユーザー ID が変更される可能性があります。 ほとんどのアプリでは、この設定を使用する必要はありません。 |
| `WEBSITE_AUTH_NONCE_DURATION`| `_hours_:_minutes_:_seconds_` 形式の _timespan_ 値。 既定値は `00:05:00` (5 分) です。 この設定は、すべてのブラウザー駆動型ログインで生成される[暗号化 nonce](https://en.wikipedia.org/wiki/Cryptographic_nonce) の有効期間を制御します。 指定した時間内にログインを完了できない場合、ログイン フローが自動的に再試行されます。 このアプリケーション設定は、V1 (クラシック) 構成エクスペリエンスで使用することを目的としています。 V2 認証構成スキーマを使用する場合は、代わりに `login.nonce.nonceExpirationInterval` 構成値を使用してください。 |
| `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` | `true` に設定し、ユーザーが URL フラグメントを含むアプリ リンクをクリックすると、ログイン リダイレクト プロセスで URL の URL フラグメント部分が失われなくなります。 詳細については、[Azure App Service 認証でのサインインとサインアウトのカスタマイズ](configure-authentication-customize-sign-in-out.md#preserve-url-fragments)に関する記事をご覧ください。 |
| `WEBSITE_AUTH_USE_LEGACY_CLAIMS` | 認証モジュールでは、アップグレード間の下位互換性を維持するため、`/.auth/me` API で従来の短い名前から長い名前への要求マッピングが使用されるため、特定のマッピング (「ロール」など) が除外されます。 要求マッピングの最新バージョンを取得するには、この変数を `False` に設定します。 「ロール」の例では、長い要求名「 http://schemas.microsoft.com/ws/2008/06/identity/claims/role 」にマップされます。 |
| `WEBSITE_AUTH_DISABLE_WWWAUTHENTICATE` | `true` または `false`。 既定値は `false` です。 `true` に設定すると、モジュールで生成される HTTP 401 応答から [`WWW-Authenticate`](https://developer.mozilla.org/docs/Web/HTTP/Headers/WWW-Authenticate) HTTP 応答ヘッダーが削除されます。 このアプリケーション設定は、V1 (クラシック) 構成エクスペリエンスで使用することを目的としています。 V2 認証構成スキーマを使用する場合は、代わりに `identityProviders.azureActiveDirectory.login.disableWwwAuthenticate` 構成値を使用してください。 |
| `WEBSITE_AUTH_STATE_DIRECTORY`  | ファイルベースのトークン ストアが有効になっている場合に、トークンが格納されるローカル ファイル システムのディレクトリ パス。 既定値は `%HOME%\Data\.auth` です。 このアプリケーション設定は、V1 (クラシック) 構成エクスペリエンスで使用することを目的としています。 V2 認証構成スキーマを使用する場合は、代わりに `login.tokenStore.fileSystem.directory` 構成値を使用してください。 |
| `WEBSITE_AUTH_TOKEN_CONTAINER_SASURL` | 完全修飾の BLOB コンテナー URL。 既定のローカル ファイル システムを使用する代わりに、指定された BLOB ストレージ コンテナーにすべての暗号化されたトークンを格納して読み込むように、認証モジュールに指示します。  |
| `WEBSITE_AUTH_TOKEN_REFRESH_HOURS` | 任意の正の 10 進数。 既定値は `72` (時間) です。 この設定は、セッション トークンの有効期限が切れた後で `/.auth/refresh` API を使用してそれを更新できる時間を制御します。 これは、セッション トークンに依存する Azure Mobile Apps で使用することを主な目的としています。 この期間が過ぎると、更新の試行は失敗し、エンド ユーザーはもう一度サインインする必要があります。 このアプリケーション設定は、V1 (クラシック) 構成エクスペリエンスで使用することを目的としています。 V2 認証構成スキーマを使用する場合は、代わりに `login.tokenStore.tokenRefreshExtensionHours` 構成値を使用してください。 |
| `WEBSITE_AUTH_TRACE_LEVEL`| [アプリケーション ログ](troubleshoot-diagnostic-logs.md#enable-application-logging-windows)に書き込まれる認証トレースの詳細度を制御します。 有効な値は、`Off`、`Error`、`Warning`、`Information`、または`Verbose` です。 既定値は `Verbose` です。 |
| `WEBSITE_AUTH_VALIDATE_NONCE`| `true` または `false`。 既定値は `true` です。 対話型ログイン中に発生する[暗号化 nonce](https://en.wikipedia.org/wiki/Cryptographic_nonce) の検証エラーを一時的にデバッグする場合を除き、この値を `false` に設定しないでください。 このアプリケーション設定は、V1 (クラシック) 構成エクスペリエンスで使用することを目的としています。 V2 認証構成スキーマを使用する場合は、代わりに `login.nonce.validateNonce` 構成値を使用してください。 |
| `WEBSITE_AUTH_V2_CONFIG_JSON` | この環境変数は、Azure App Service プラットフォームによって自動的に設定され、統合認証モジュールを構成するために使用されます。 この環境変数の値は、Azure Resource Manager における現在のアプリの V2 (クラシックではない) 認証構成に対応しています。 明示的に構成するためのものではありません。 |
| `WEBSITE_AUTH_ENABLED` | 読み取り専用です。 App Service 認証が有効かどうかを示すために Windows または Linux アプリに挿入されます。 |
| `WEBSITE_AUTH_ENCRYPTION_KEY` | 既定では、自動生成されたキーが暗号化キーとして使用されます。 オーバーライドするには、目的のキーに設定します。 これは、複数のアプリでトークンまたはセッションを共有する場合にお勧めします。 指定された場合は、`MACHINEKEY_DecryptionKey` 設定よりも優先されます。 |
| `WEBSITE_AUTH_SIGNING_KEY` | 既定では、自動生成されたキーが署名キーとして使用されます。 オーバーライドするには、目的のキーに設定します。 これは、複数のアプリでトークンまたはセッションを共有する場合にお勧めします。 指定された場合は、`MACHINEKEY_ValidationKey` 設定よりも優先されます。 |

<!-- System settings
WEBSITE_AUTH_RUNTIME_VERSION
WEBSITE_AUTH_API_PREFIX
WEBSITE_AUTH_TOKEN_STORE
WEBSITE_AUTH_MOBILE_COMPAT
WEBSITE_AUTH_AAD_BYPASS_SINGLE_TENANCY_CHECK
WEBSITE_AUTH_COOKIE_EXPIRATION_TIME
WEBSITE_AUTH_COOKIE_EXPIRATION_MODE
WEBSITE_AUTH_PROXY_HEADER_CONVENTION
WEBSITE_AUTH_PROXY_HOST_HEADER
WEBSITE_AUTH_PROXY_PROTO_HEADER
WEBSITE_AUTH_REQUIRE_HTTPS
WEBSITE_AUTH_DEFAULT_PROVIDER
WEBSITE_AUTH_UNAUTHENTICATED_ACTION
WEBSITE_AUTH_EXTERNAL_REDIRECT_URLS
WEBSITE_AUTH_CUSTOM_IDPS
WEBSITE_AUTH_CUSTOM_AUTHZ_SETTINGS
WEBSITE_AUTH_CLIENT_ID
WEBSITE_AUTH_CLIENT_SECRET
WEBSITE_AUTH_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_CLIENT_SECRET_CERTIFICATE_THUMBPRINT
WEBSITE_AUTH_OPENID_ISSUER
WEBSITE_AUTH_ALLOWED_AUDIENCES
WEBSITE_AUTH_LOGIN_PARAMS
WEBSITE_AUTH_AUTO_AAD
WEBSITE_AUTH_AAD_CLAIMS_AUTHORIZATION
WEBSITE_AUTH_GOOGLE_CLIENT_ID
WEBSITE_AUTH_GOOGLE_CLIENT_SECRET
WEBSITE_AUTH_GOOGLE_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_GOOGLE_SCOPE
WEBSITE_AUTH_FB_APP_ID
WEBSITE_AUTH_FB_APP_SECRET
WEBSITE_AUTH_FB_APP_SECRET_SETTING_NAME
WEBSITE_AUTH_FB_SCOPE
WEBSITE_AUTH_GITHUB_CLIENT_ID
WEBSITE_AUTH_GITHUB_CLIENT_SECRET
WEBSITE_AUTH_GITHUB_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_GITHUB_APP_SCOPE
WEBSITE_AUTH_TWITTER_CONSUMER_KEY
WEBSITE_AUTH_TWITTER_CONSUMER_SECRET
WEBSITE_AUTH_TWITTER_CONSUMER_SECRET_SETTING_NAME
WEBSITE_AUTH_MSA_CLIENT_ID
WEBSITE_AUTH_MSA_CLIENT_SECRET
WEBSITE_AUTH_MSA_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_MSA_SCOPE
WEBSITE_AUTH_FROM_FILE
WEBSITE_AUTH_FILE_PATH
| `WEBSITE_AUTH_CONFIG_DIR` | (Used for the deprecated "routes" feature) |
| `WEBSITE_AUTH_ZUMO_USE_TOKEN_STORE_CLAIMS` | (looks like only a tactical fix) ||
 -->

## <a name="managed-identity"></a>マネージド ID

次の環境変数は、[マネージド ID](overview-managed-identity.md) に関連しています。

|設定の名前 | 説明 |
|-|-|
|`IDENTITY_ENDPOINT` | 読み取り専用です。 アプリの[マネージド ID](overview-managed-identity.md) のトークンを取得するための URL。 |
| `MSI_ENDPOINT` | 非推奨になりました。 `IDENTITY_ENDPOINT` を使用してください。 |
| `IDENTITY_HEADER` | 読み取り専用です。 `IDENTITY_ENDPOINT` に対して HTTP GET 要求を行う際に `X-IDENTITY-HEADER` ヘッダーに追加する必要がある値。 値は、プラットフォームによってローテーションされます。 |
| `MSI_SECRET` | 非推奨になりました。 `IDENTITY_HEADER` を使用してください。 |
<!-- | `WEBSITE_AUTHENTICATION_ENDPOINT_ENABLED` | Disabled by default? TODO | -->

## <a name="health-check"></a>正常性チェック

次の環境変数は、[正常性チェック](monitor-instances-health-check.md)に関連しています。

| 設定の名前 | 説明 |
|-|-|
| `WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | インスタンスを削除するまでに失敗する ping の最大数。 `2` から `100` までの値に設定します。 スケールアップまたはスケールアウトする場合は、新しいインスタンスの準備ができていることを確認するため、App Service で正常性チェックのパスに ping が実行されます。 詳細については、「[正常性チェック](monitor-instances-health-check.md)」を参照してください。|
| `WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` | 正常なインスタンスが過負荷にならないように、ご利用のインスタンスの半分以下が除外されます。 たとえば、App Service プランが 4 つのインスタンスにスケーリングされ、3 つに異常が発生した場合、最大 2 つが除外されます。 他の 2 つのインスタンス (1 つは正常、1 つは異常) は、引き続き要求を受信することになります。 すべてのインスタンスが異常であるという最悪のシナリオでは、何も除外されません。 この動作をオーバーライドするには、`0` から `100` までの値に設定します。 値を大きくすると、異常なインスタンスがより多く削除されます。 既定値は `50` (50%) です。 |

## <a name="push-notifications"></a>プッシュ通知

次の環境変数は、[プッシュ通知](/previous-versions/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#configure-hub)機能に関連しています。

| 設定の名前 | 説明 |
|-|-|
| `WEBSITE_PUSH_ENABLED` | 読み取り専用です。 プッシュ通知が有効になったときに追加されます。 |
| `WEBSITE_PUSH_TAG_WHITELIST` | 読み取り専用です。 通知登録のタグが含まれています。 |
| `WEBSITE_PUSH_TAGS_REQUIRING_AUTH` | 読み取り専用です。 通知登録でユーザー認証を必要とするタグの一覧が含まれています。 |
| `WEBSITE_PUSH_TAGS_DYNAMIC` | 読み取り専用です。 通知登録で自動的に追加されたタグの一覧が含まれています。 | 

>[!NOTE]
> この記事には、Microsoft が使用しなくなった "*ホワイトリスト*" という用語への言及があります。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。

<!-- 
## WellKnownAppSettings

WEBSITE_ALWAYS_PERFORM_PRELOAD
| WEBSITE_DISABLE_PRIMARY_VOLUMES | Set to `true` to disable the primary storage volume for that app. The default is `false`. |
| WEBSITE_DISABLE_STANDBY_VOLUMES | Set to `true` to disable the stand-by storage volume for that app. The default is `false`. This setting has no effect if `WEBSITE_DISABLE_PRIMARY_VOLUMES` is `true`. |
WEBSITE_FAILOVER_ONLY_ON_SBX_NW_FAILURES
WEBSITE_ENABLE_SYSTEM_LOG
WEBSITE_FRAMEWORK_JIT
WEBSITE_ADMIN_SITEID
WEBSITE_STAMP_DEPLOYMENT_ID
| WEBSITE_DEPLOYMENT_ID | Read-only. internal ID of deployment slot |
| WEBSITE_DISABLE_MSI | deprecated |
WEBSITE_VNET_BLOCK_FOR_SETUP_MAIN_SITE
WEBSITE_VNET_BLOCK_FOR_SETUP_SCM_SITE

 -->

## <a name="webjobs"></a>Web ジョブ

次の環境変数は、[Web ジョブ](webjobs-create.md)に関連しています。

| 設定の名前| 説明 |
|-|-|
| `WEBJOBS_RESTART_TIME`|連続ジョブの場合、ジョブのプロセスが何らかの理由で停止してから再起動するまでの遅延時間 (秒)。 |
| `WEBJOBS_IDLE_TIMEOUT`| トリガーされたジョブの場合、ジョブがアイドル状態で、CPU 時間も出力もない場合に、ジョブが中止されるまでのタイムアウト (秒)。 |
| `WEBJOBS_HISTORY_SIZE`| トリガーされたジョブの場合、ジョブごとに履歴ディレクトリに保持される実行の最大数。 既定では、 `50`です。 |
| `WEBJOBS_STOPPED`| ジョブの実行を無効にし、現在実行中のすべてのジョブを停止するには、`1` に設定します。 |
| `WEBJOBS_DISABLE_SCHEDULE`| スケジュールされたすべてのトリガーを無効にするには、`1` に設定します。 その場合でも、手動でジョブを呼び出すことがでできます。 |
| `WEBJOBS_ROOT_PATH`| Web ジョブ ファイルの絶対または相対パス。 相対パスの場合、この値は既定のルート パス (`D:/home/site/wwwroot/` または `/home/site/wwwroot/`) と結合されます。 |
| `WEBJOBS_LOG_TRIGGERED_JOBS_TO_APP_LOGS`| トリガーされた Web ジョブからの出力をアプリケーション ログのパイプライン (ファイル システム、BLOB、テーブルをサポート) に送信するには、True に設定します。 |
| `WEBJOBS_SHUTDOWN_FILE` | シャットダウン要求が検出されたときに App Service によって作成されるファイル。 このファイルの存在を検出してシャットダウンを開始するのは、Web ジョブ プロセスの役割です。 Web ジョブ SDK を使用する場合、この部分は自動的に処理されます。 |
| `WEBJOBS_PATH` | 読み取り専用です。 現在実行中のジョブのルート パス (何らかの一時ディレクトリの下に配置されます)。 |
| `WEBJOBS_NAME` | 読み取り専用です。 現在のジョブ名。 |
| `WEBJOBS_TYPE` | 読み取り専用です。 現在のジョブの種類 (`triggered` または `continuous`)。 |
| `WEBJOBS_DATA_PATH` | 読み取り専用です。 ジョブのログ、履歴、ジョブの成果物を格納する、現在のジョブのメタデータ パス。 |
| `WEBJOBS_RUN_ID` | 読み取り専用です。 トリガーされたジョブの場合、ジョブの現在の実行 ID。 |

## <a name="functions"></a>関数

| 設定の名前 | 説明 |
|-|-|
| `WEBSITE_FUNCTIONS_ARMCACHE_ENABLED` | 関数キャッシュを無効にするには、`0` に設定します。 |
| `WEBSITE_MAX_DYNAMIC_APPLICATION_SCALE_OUT` | [Azure Functions のアプリケーション設定のリファレンス](../azure-functions/functions-app-settings.md) |
| `FUNCTIONS_EXTENSION_VERSION` | [Azure Functions のアプリケーション設定のリファレンス](../azure-functions/functions-app-settings.md) |
`AzureWebJobsSecretStorageType` | [Azure Functions のアプリケーション設定のリファレンス](../azure-functions/functions-app-settings.md) |
| `FUNCTIONS_WORKER_RUNTIME` | [Azure Functions のアプリケーション設定のリファレンス](../azure-functions/functions-app-settings.md) |
| `AzureWebJobsStorage` | [Azure Functions のアプリケーション設定のリファレンス](../azure-functions/functions-app-settings.md) |
| `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` | [Azure Functions のアプリケーション設定のリファレンス](../azure-functions/functions-app-settings.md) |
| `WEBSITE_CONTENTSHARE` | [Azure Functions のアプリケーション設定のリファレンス](../azure-functions/functions-app-settings.md) |
| `WEBSITE_CONTENTOVERVNET` | [Azure Functions のアプリケーション設定のリファレンス](../azure-functions/functions-app-settings.md) |
| `WEBSITE_ENABLE_BROTLI_ENCODING` | [Azure Functions のアプリケーション設定のリファレンス](../azure-functions/functions-app-settings.md) |
| `WEBSITE_USE_PLACEHOLDER` | 従量課金プランでプレースホルダー関数の最適化を無効にするには、`0` に設定します。 プレースホルダーは、[コールド スタートを改善する](../azure-functions/functions-scale.md#cold-start-behavior)最適化です。 |
| `WEBSITE_PLACEHOLDER_MODE` | 読み取り専用です。 関数アプリがプレースホルダー ホスト (`generalized`) と独自のホスト (`specialized`) のどちらで実行されるかを示します。 |
| `WEBSITE_DISABLE_ZIP_CACHE` | アプリが [ZIP パッケージ](deploy-run-package.md)から実行される場合 (`WEBSITE_RUN_FROM_PACKAGE=1`) は、最近デプロイされた 5 つの ZIP パッケージがアプリのファイル システム (D:\home\data\SitePackages) にキャッシュされます。 このキャッシュを無効にするには、この変数を `1` に設定します。 Linux 従量課金アプリの場合、ZIP パッケージ キャッシュは既定で無効になっています。 |
<!--
| `FUNCTIONS_RUNTIME_SCALE_MONITORING_ENABLED` | TODO |
| `WEBSITE_SKIP_FUNCTION_APP_WARMUP` | Apps can use appsetting to opt out of warmup. Restricted to linux only since this is primarily for static sites that use Linux dynamic function apps. Linux dynamic sites are used as placeholder sites for static sites. Function apps don't get specialized until static sites are deployed. This allows function apps used by static sites to skip warmup and using up containers before any content is deployed. TODO |
 WEBSITE_IDLE_TIMEOUT_IN_MINUTES | removed WEBSITE_IDLE_TIMEOUT_IN_MINUTES because they aren't used in Linux Consumption.???
| `WEBSITE_DISABLE_FUNCTIONS_STARTUPCONTEXT_CACHE`| This env var can be set to 1 by users in order to avoid using the Functions StartupContext Cache feature. |
| `WEBSITE_CONTAINER_READY` | The env var is set to '1' to indicate to the Functions Runtime that it can proceed with initializing/specializing 
        // itself. For placeholders, it is set once specialization is complete on DWAS side and detours are reinitialized. For 
        // non-placeholder function apps, it is simply set to 1 when the process is started, because detours are initialized 
        // as part of starting the process (when PicoHelper.dll is loaded, well before any managed code is running).
        // NOTE: This is set on all sites, irrespective of whether it is a Functions site, because the EnvSettings module depends 
        // upon it to decide when to inject the app-settings.|
| `WEBSITE_PLACEHOLDER_PING_PATH` | This env var can be used to set a special warmup ping path on placeholder template sites. |
| ` WEBSITE_PLACEHOLDER_DISABLE_AUTOSPECIALIZATION` | This env var can be used to disabe specialization from being enabled automatically for a given placeholder template site. |
| `WEBSITE_FUNCTIONS_STARTUPCONTEXT_CACHE` | This env var is set only during specialization of a placeholder, to indicate to the Functions Runtime that
        // some function-app related data needed at startup, like secrets, are available in a file at the path specified
        // by this env var. |
WEBSITE_ENABLE_COLD_START_PROFILING | This env var can be set to 1 by internal SLA sites in order to trigger collection of perf profiles, if feature is enabled on the stamp. |
WEBSITE_CURRENT_STAMPNAME | these environments contain the stamp name used for various scale decisions |
WEBSITE_HOME_STAMPNAME | these environments contain the stamp name used for various scale decisions |
WEBSITE_ELASTIC_SCALING_ENABLED
WEBSITE_FILECHANGEAUDIT_ENABLED
| `WEBSITE_HTTPSCALEV2_ENABLED` | This is the default behavior for both v1 and v2 Azure Functions apps. | 
WEBSITE_CHANGEANALYSISSCAN_ENABLED
WEBSITE_DISABLE_CHILD_SPECIALIZATION
 -->

<!-- 
## Server variables
|HTTP_HOST| |
|HTTP_DISGUISED_HOST|the runtime site name for inbound requests.|
HTTP_CACHE_CONTROL
HTTP_X_SITE_DEPLOYMENT_ID
HTTP_WAS_DEFAULT_HOSTNAME
HTTP_X_ORIGINAL_URL
HTTP_X_FORWARDED_FOR
HTTP_X_ARR_SSL
HTTP_X_FORWARDED_PROTO
HTTP_X_APPSERVICE_PROTO
HTTP_X_FORWARDED_TLSVERSION
X-WAWS-Unencoded-URL
CLIENT-IP
X-ARR-LOG-ID
DISGUISED-HOST
X-SITE-DEPLOYMENT-ID
WAS-DEFAULT-HOSTNAME
X-Original-URL
X-MS-CLIENT-PRINCIPAL-NAME
X-MS-CLIENT-DISPLAY-NAME
X-Forwarded-For
X-ARR-SSL
X-Forwarded-Proto
X-AppService-Proto
X-Forwarded-TlsVersion
URL
HTTP_CLIENT_IP
APP_WARMING_UP |Regular/external requests made while warmup is in progress will have a APP_WARMING_UP server variable set to 1|
HTTP_COOKIE
SERVER_NAME
HTTP_X_FORWARDED_HOST
| HTTP_X_AZURE_FDID | Azure Front Door ID. See [](../frontdoor/front-door-faq.md#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-) |
HTTP_X_FD_HEALTHPROBE
|WEBSITE_LOCALCACHE_ENABLED | shows up in w3wp.exe worker process |
HTTP_X_ARR_LOG_ID
| SCM_BASIC_AUTH_ALLOWED | set to "false" or "0" to disable basic authentication |
HTTP_X_MS_WAWS_JWT
HTTP_MWH_SecurityToken
LB_ALGO_FOR_HOSTING_SERVER_PROVIDER
ENABLE_CLIENT_AFFINITY
HTTP_X_MS_FROM_GEOMASTER
HTTP_X_MS_USE_GEOMASTER_CERT
HTTP_X_MS_STAMP_TOKEN
HTTPSCALE_REQUEST_ID
HTTPSCALE_FORWARD_FRONTEND_KEY
HTTPSCALE_FORWARD_REQUEST
IS_VALID_STAMP_TOKEN
NEEDS_SITE_RESTRICTED_TOKEN
HTTP_X_MS_PRIVATELINK_ID
  -->
---
title: Azure Data Factory のトラブルシューティング | Microsoft Docs
description: Azure Data Factory における外部の制御アクティビティのトラブルシューティング方法について説明します。
services: data-factory
author: abnarain
manager: craigg
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 6/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: c76242c176ba4f4c9ffc0d6934f6b645743d77f4
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234582"
---
# <a name="troubleshoot-azure-data-factory"></a>Azure Data Factory のトラブルシューティング

この記事では、Azure Data Factory における外部の制御アクティビティの一般的なトラブルシューティング方法について説明します。

## <a name="azure-databricks"></a>Azure Databricks

| エラー コード | エラー メッセージ                                          | 説明                             | 推奨                             |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | エラー 403。                                                    | Databricks アクセス トークンの有効期限が切れています。                         | 既定では、Databricks アクセス トークンは 90 日間有効です。  新しいトークンを作成し、リンクされたサービスを更新してください。 |
| 3201           | Missing   required field: settings.task.notebook_task.notebook_path (必須フィールドが欠落しています: settings.task.notebook_task.notebook_path) | 誤ったオーサリング:Notebook のパスが正しく指定されていません。 | Databricks アクティビティに Notebook のパスを指定してください。 |
| 3201           | クラスター ... が存在しません。                                 | オーサリング エラー:Databricks クラスターは存在しないか、削除されています。 | Databricks クラスターが存在することを確認してください。 |
| 3201           | Invalid   Python file URI ....Please visit Databricks user guide for supported URI   schemes. (無効な Python ファイル URI ...。Databricks ユーザー ガイドを参照して、サポートされている URI スキームを確認してください。) | 誤った作成。                                                | ワークスペース アドレス指定スキームの絶対パス (Databricks ファイル システムに格納されているファイルの場合は `dbfs:/folder/subfolder/foo.py`) を指定してください。 |
| 3201           | {0}   LinkedService should have domain and accessToken as required properties. ({0}   LinkedService には、必須プロパティとしてドメインと accessToken が必要です。) | 誤った作成。                                                | [リンクされたサービスの定義](compute-linked-services.md#azure-databricks-linked-service)を確認してください。 |
| 3201           | {0}   LinkedService should specify either existing cluster ID or new cluster   information for creation. ({0} LinkedService では、既存のクラスター ID、または作成用の新しいクラスター情報を指定する必要があります。) | 誤った作成。                                                | [リンクされたサービスの定義](compute-linked-services.md#azure-databricks-linked-service)を確認してください。 |
| 3201           | Node   type Standard_D16S_v3 is not supported. Supported node types: (ノードの種類 Standard_D16S_v3 はサポートされていません。サポートされているノードの種類は次のとおりです。) Standard_DS3_v2、Standard_DS4_v2、Standard_DS5_v2、Standard_D8s_v3、Standard_D16s_v3、Standard_D32s_v3、Standard_D64s_v3、Standard_D3_v2、Standard_D8_v3、Standard_D16_v3、Standard_D32_v3、Standard_D64_v3、Standard_D12_v2、Standard_D13_v2、Standard_D14_v2、Standard_D15_v2、Standard_DS12_v2、Standard_DS13_v2、Standard_DS14_v2、Standard_DS15_v2、Standard_E8s_v3、Standard_E16s_v3、Standard_E32s_v3、Standard_E64s_v3、Standard_L4s、Standard_L8s、Standard_L16s、Standard_L32s、Standard_F4s、Standard_F8s、Standard_F16s、Standard_H16、Standard_F4s_v2、Standard_F8s_v2、Standard_F16s_v2、Standard_F32s_v2、Standard_F64s_v2、Standard_F72s_v2、Standard_NC12、Standard_NC24、Standard_NC6s_v3、Standard_NC12s_v3、Standard_NC24s_v3、Standard_L8s_v2、Standard_L16s_v2、Standard_L32s_v2、Standard_L64s_v2、Standard_L80s_v2。 | 誤った作成。                                                | エラー メッセージを参照してください。                                          |
| 3201           | Invalid notebook_path: ....Only absolute paths are currently supported. Paths must   begin with '/'. (無効な notebook_path: ....。現在サポートされているのは絶対パスのみです。パスは '/' で開始する必要があります。) | 誤った作成。                                                | エラー メッセージを参照してください。                                          |
| 3202           | There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:  1000 job creations per 3600 seconds. (過去 3600 秒間に既に 1000 個のジョブが作成されました。速度制限である 3600 秒間あたり 1000 個のジョブ作成を超えています。) | 1 時間で実行される Databricks の数が多すぎます。                         | この Databricks ワークスペースを使用しているすべてのパイプラインでジョブの作成速度を確認します。  パイプラインで起動された Databricks の実行数が全体として多すぎる場合は、一部のパイプラインを新しいワークスペースに移行してください。 |
| 3202           | Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead. (要求オブジェクトを解析できませんでした: JSON マップ フィールド base_parameters には 'key' と 'value' が設定されることが予期されていましたが、代わりに 'key: "..."' が設定されました。) | オーサリング エラー:パラメーターの値が指定されていません。         | パイプラインの JSON を検査して、baseParameters ノートブックのすべてのパラメーターに空以外の値が指定されていることを確認してください。 |
| 3202           | User: `SimpleUserContext{userId=..., name=user@company.com, orgId=...}` is not   authorized to access cluster. (ユーザー `SimpleUserContext{userId=..., name=user@company.com, orgId=...}` はクラスターにアクセスする権限がありません。) | アクセス トークンを生成したユーザーは、リンクされたサービスに指定されている Databricks クラスターへのアクセスを許可されていません。 | ワークスペースで必要なアクセス許可がユーザーにあることを確認します。   |
| 3203           | The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later. (クラスターは終了状態であり、ジョブを受信できません。クラスターを修正するか、後で再試行してください。) | クラスターは終了されました。    対話型クラスターの場合、競合状態になる可能性があります。 | このエラーを回避する最善の方法は、ジョブ クラスターを使用することです。             |
| 3204           | Job execution failed. (ジョブの実行が失敗しました。)  | エラー メッセージは、予期しないクラスター状態や特定のアクティビティなど、さまざまな問題を示します。 ほとんどの場合、エラー メッセージがまったく表示されません。                                                          | 該当なし                                                          |



## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

次の表は、U-SQL について記載したものです。

| エラー コード         | エラー メッセージ                                                | 説明                                          | 推奨                            |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | The access token is from the wrong tenant. (アクセス トークンは正しくないテナントからのものです)。                    | Azure Active Directory (Azure AD) テナントが正しくありません。                                         | Azure Data Lake Analytics へのアクセスに使用されたサービス プリンシパルは、別の Azure AD テナントに属しています。 Data Lake Analytics アカウントと同じテナントに新しいサービス プリンシパルを作成してください。 |
| 2711、2705、2704 | Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation. (禁止。ACL の検証が失敗しました。リソースが存在しないか、要求された操作を実行する権限がユーザーにありません。)<br/><br/>User is not able to access Data Lake Store. (ユーザーは Data Lake Store にアクセスできません。)  <br/><br/>User is not authorized to use Data Lake Analytics. (ユーザーには Data Lake Analytics を使用する権限がありません。) | サービス プリンシパルまたは証明書には、ストレージ内のファイルへのアクセス権がありません。 | Data Lake Analytics ジョブ用にユーザーが指定したサービス プリンシパルまたは証明書が、Data Lake Analytics アカウントにアクセスできること、また、ルート フォルダーから既定の Data Lake Storage インスタンスにアクセスできることを確認します。 |
| 2711                 | Cannot find the 'Azure Data Lake Store' file or folder. ('Azure Data Lake Store' ファイルまたはフォルダーが見つかりません。)       | U-SQL ファイルのパスが間違っているか、リンクされたサービスの資格情報がアクセス権を持っていません。 | パスと、リンクされたサービスに指定された資格情報を確認してください。 |
| 2707                 | Cannot resolve the account of   AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'. (AzureDataLakeAnalytics のアカウントを解決できません。'AccountName' と 'DataLakeAnalyticsUri' を確認してください。) | リンクされたサービスの Data Lake Analytics アカウントに誤りがあります。                  | 正しいアカウントが指定されていることを確認してください。             |
| 2703                 | エラー ID: E_CQO_SYSTEM_INTERNAL_ERROR (または "Error   Id:" で始まる任意のエラー) | Data Lake Analytics からのエラーです。                                    | この例のようなエラーは、Data Lake Analytics にジョブが送信されたものの、そこでスクリプトが失敗したことを意味します。 Data Lake Analytics 内を調査してください。 ポータルから Data Lake Analytics アカウントに移動し、Data Factory の (パイプラインの実行 ID ではなく) アクティビティの実行 IDを使用して該当するジョブを探します。 見つかったジョブから、エラーに関する詳細な情報を入手し、トラブルシューティングに役立てることができます。 解決方法が明確でない場合は、Data Lake Analytics サポート チームに連絡し、お使いのアカウント名とジョブ ID を含むジョブの URL を伝えてください。 |
| 2709                 | We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. (現時点では、ジョブを受け入れられません。ご使用のアカウントでキューに入れられるジョブの最大数は 200 です。) | このエラーは、Data Lake Analytics のスロットリングに起因しています。                                           | アクティビティに対する Data Factory のトリガーとコンカレンシーの設定を変更して、Data Lake Analytics に送信されるジョブの数を減らしてください。 または、Data Lake Analytics の上限を引き上げます。 |
| 2709                 | This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs. (このジョブは 24 個の AU を必要とするため、拒否されました。1 つのジョブで 5 個を超える AU を使用することは、このアカウントの管理者によって定義されたポリシーで禁止されています。) | このエラーは、Data Lake Analytics のスロットリングに起因しています。                                           | アクティビティに対する Data Factory のトリガーとコンカレンシーの設定を変更して、Data Lake Analytics に送信されるジョブの数を減らしてください。 または、Data Lake Analytics の上限を引き上げます。 |



## <a name="azure-functions"></a>Azure 関数

| エラー コード | エラー メッセージ                           | 説明                                                  | 推奨                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | Response Content is not a valid JObject. (応答コンテンツは有効な JObject ではありません。) | 呼び出した Azure 関数の応答で JSON ペイロードが返されませんでした。 Data Factory の Azure 関数アクティビティでサポートされるのは JSON 応答コンテンツだけです。 | 有効な JSON ペイロードを返すように Azure 関数を更新してください。 たとえば、C# の関数から `(ActionResult)new<OkObjectResult("{`\"Id\":\"123\"`}");` を返すことが考えられます。 |
| 3600         | Invalid HttpMethod: '..'. (無効な HttpMethod: '..'。)               | アクティビティのペイロードに指定された HTTP メソッドが Azure 関数アクティビティでサポートされていません。 | PUT、POST、GET、DELETE、OPTIONS、HEAD、TRACE など、サポートされている HTTP メソッドを使用してください。 |



## <a name="custom"></a>カスタム

次の表は、Azure Batch に当てはまります。

| エラー コード | エラー メッセージ                                                | 説明                                                  | 推奨                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2500         | Hit unexpected exception and execution failed. (予期しない例外が発生し、実行が失敗しました。)             | コマンドを起動できないか、プログラムでエラー コードが返されました。 | 実行可能ファイルが存在することを確認してください。 プログラムが起動している場合は、*stdout.txt* と *stderr.txt* がストレージ アカウントにアップロードされていることを確認します。 コードでは、デバッグ用に詳しいログを出力することをお勧めします。 |
| 2501         | Cannot   access user batch account, please check batch account settings. (ユーザーの Batch アカウントにアクセスできません。Batch アカウントの設定を確認してください。) | Batch アクセス キーまたはプール名が正しくありません。            | リンクされたサービスでプール名と Batch アクセス キーを確認してください。 |
| 2502         | Can not   access user storage account; please check storage account settings. (ユーザーのストレージ アカウントにアクセスできません。ストレージ アカウントの設定を確認してください。) | ストレージ アカウント名またはアクセス キーが正しくありません。       | リンクされたサービスでストレージ アカウント名とアクセス キーを確認してください。 |
| 2504         | Operation   returned an invalid status code 'BadRequest'. (操作によって無効な状態コード 'BadRequest' が返されました)     | カスタム アクティビティの folderPath に含まれるファイルが多すぎます。 resourceFiles の合計サイズは 32,768 文字を超えないようにします。 | 不要なファイルを削除してください。 または、それらを zip 圧縮したうえで、展開するための解凍コマンドを追加してください。 たとえば、`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe` を使用します。 |
| 2505         | Cannot   create Shared Access Signature unless Account Key credentials are used. (アカウント キーの資格情報が使用されない限り、Shared Access Signature を作成できません。) | カスタム アクティビティでは、アクセス キーを使用するストレージ アカウントのみがサポートされます。 | エラーの説明を参照してください。                                            |
| 2507         | The   folder path does not exist or is empty: .... (フォルダー パスは存在しないか、空です: ....)            | 指定されたパスのストレージ アカウントにファイルがありません。       | このフォルダー パスには、実行したい実行可能ファイルが含まれている必要があります。 |
| 2508         | There are   duplicate files in the resource folder. (リソース フォルダーに重複するファイルがあります。)               | folderPath の異なるサブフォルダーに、同じ名前のファイルが複数あります。 | カスタム アクティビティでは、folderPath の下でフォルダー構造がフラット化されます。  フォルダー構造を維持する必要がある場合は、ファイルを zip 圧縮し、Azure Batch で解凍コマンドを使用してそれらを展開します。 たとえば、`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe` を使用します。 |
| 2509         | Batch   url ... is invalid; it must be in Uri format. (Batch URL が無効です。URI 形式にする必要があります。)         | Batch URL は次のようになります: `https://mybatchaccount.eastus.batch.azure.com` | エラーの説明を参照してください。                                            |
| 2510         | An   error occurred while sending the request. (この要求の送信中にエラーが発生しました。)               | Batch URL が無効です。                                         | Batch URL を確認してください。                                            |

## <a name="hdinsight"></a>HDInsight

次の表は、Spark、Hive、MapReduce、Pig、Hadoop Streaming に当てはまります。

| エラー コード | エラー メッセージ                                                | 説明                                                  | 推奨                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300、2310 | Hadoop job submission failed. (Hadoope ジョブの送信に失敗しました。) エラー:リモート名を解決できませんでした" という WebException を時々発生させる問題を修正しました。 <br/><br/>The cluster is not found. (クラスターが見つかりません。) | 指定されたクラスター URI が無効です。                              | クラスターが削除されていないことと指定した URI が正しいことを確認してください。 ブラウザーでこの URI を開くと、Ambari UI が表示されます。 クラスターが仮想ネットワークに存在する場合、この URI はプライベート URI になります。 それを開くには、同じ仮想ネットワークに属している VM を使用します。 詳細については、「[Apache Hadoop サービスへの直接接続](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services)」を参照してください。 |
| 2300         | Hadoop job submission failed. Job: …, Cluster: …/. エラー:A task was canceled. (Hadoop ジョブの送信に失敗しました。ジョブ: …、クラスター: …/。エラー: タスクがキャンセルされました。) | ジョブの送信がタイムアウトしました。                         | 一般的な HDInsight の接続またはネットワーク接続の問題が考えられます。 まず、HDInsight Ambari UI がどのブラウザーからも使用可能であることを確認します。 ご自分の資格情報がまだ有効であることを確認してください。 セルフホステッド統合ランタイム (IR) を使用している場合は、必ずセルフホステッド IR がインストールされている VM またはマシンからこれを実行してください。 その後、Data Factory から再びジョブの送信を試みてください。 それでも失敗する場合は、Data Factory チームに連絡してサポートを依頼してください。 |
| 2300         | Unauthorized: (許可されていません:) Ambari user name or password is incorrect (Ambari のユーザー名またはパスワードが正しくありません)  <br/><br/>Unauthorized: (許可されていません:) User admin is locked out in Ambari. (ユーザー管理者が Ambari でロックアウトされています。)   <br/><br/>403 - 禁止: アクセスが拒否されました。 | HDInsight の資格情報が正しくないか、有効期限が切れています。 | 資格情報を訂正して、リンクされたサービスを再デプロイしてください。 任意のブラウザーでクラスター URI を開いてサインインを試行することで、まず HDInsight で資格情報が機能することを確認します。 資格情報が機能しない場合は、Azure portal からリセットできます。 |
| 2300、2310 | 502 - Web サーバーがゲートウェイまたはプロキシ サーバーとして動作しているときに、無効な応答を受信しました。       <br/>無効なゲートウェイ。 | HDInsight からのエラーです。                               | このエラーは HDInsight クラスターからのものです。 詳細については、「[Ambari UI 502 エラー](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)」、[Spark Thrift サーバーへの接続時の 502 エラー](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)に関するページ、[Spark Thrift サーバーへの接続時の 502 エラー](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)に関するページ、「[Application Gateway での無効なゲートウェイによるエラーのトラブルシューティング](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502)」を参照してください。 |
| 2300         | Hadoop job submission failed. (Hadoope ジョブの送信に失敗しました。) Job: …, Cluster: ... Error:   {\"error\":\"Unable to service the submit job request as   templeton service is busy with too many submit job requests. Please wait for   some time before retrying the operation. Please refer to the config   templeton.parallellism.job.submit to configure concurrent requests. (ジョブ: …、クラスター: ... エラー:   {"error":"ジョブの送信要求の数が多すぎて templeton サービスがビジーのため、ジョブの送信要求を処理できません。操作を再試行する前にしばらくお待ちください。コンカレント要求を構成するには、構成 templeton.parallellism.job.submit を参照してください。)  <br/><br/>Hadoop job submission failed. Job: 161da5d4-6fa8-4ef4-a240-6b6428c5ae2f, Cluster: `https://abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/`.   Error: {\"error\":\"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: Failed to submit   application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: Queue root.joblauncher already has 500 applications, cannot accept submission of application: application_1561147195099_3730\ (Hadoope ジョブの送信に失敗しました。ジョブ: 161da5d4-6fa8-4ef4-a240-6b6428c5ae2f、クラスター: `https://abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/`。エラー: {"error":"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: application_1561147195099_3730 を YARN に送信できませんでした: org.apache.hadoop.security.AccessControlException: キュー root.joblauncher には既に 500 個のアプリケーションがあり、アプリケーション: application_1561147195099_3730 の送信を受け入れられません) | HDInsight に同時に送信されているジョブの数が多すぎます。 | HDInsight に送信されているコンカレント ジョブの数を制限することを検討してください。 ジョブが同じアクティビティによって送信されている場合は、Data Factory アクティビティのコンカレンシーを参照してください。 同時実行のパイプラインが時間を分散して実行されるようにトリガーを変更してください。 HDInsight のドキュメントを参照し、エラーの提案に従って `templeton.parallellism.job.submit` を調整してください。 |
| 2303、2347 | Hadoop job failed with exit code '5'. See   'wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr'   for more details. (Hadoop ジョブが終了コード '5' で失敗しました。詳細については、'wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' を参照してください。)  <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out'   for more details. (Hive の実行は、エラー コード 'UserErrorHiveOdbcCommandExecutionFailure' で失敗しました。詳細については、'wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' を参照してください。) | ジョブが HDInsight に送信され、HDInsight で失敗しました。 | ジョブは HDInsight に正常に送信されました。 クラスターで失敗しました。 HDInsight Ambari UI でジョブとログを開くか、エラー メッセージに示されているファイルをストレージから開いてください。 そのファイルにエラーの詳細が表示されます。 |
| 2328         | Internal server error occurred while processing the request. Please retry the request   or contact support. (要求を処理しているときに内部サーバー エラーが発生しました。要求を再試行するか、サポートに連絡してください。) | このエラーは、HDInsight のオンデマンドで発生します。                              | このエラーは、HDInsight のプロビジョニングが失敗したときに HDInsight サービスによって生成されます。 HDInsight チームに連絡して、オンデマンド クラスターの名前を伝えてください。 |
| 2310         | java.lang.NullPointerException                               | このエラーは、Spark クラスターにジョブが送信されたときに発生します。      | この例外は HDInsight から発生します。 実際の問題が隠されています。 HDInsight チームに連絡してサポートを依頼してください。 クラスター名とアクティビティの実行時間の範囲を伝えてください。 |
|              | その他のすべてのエラー                                             |                                                              | [HDInsight を使用したトラブルシューティング](../hdinsight/hdinsight-troubleshoot-guide.md)に関するページと [HDInsight の FAQ](https://hdinsight.github.io/) ページを参照してください。 |



## <a name="web-activity"></a>Web アクティビティ

| エラー コード | エラー メッセージ                                                | 説明                                                  | 推奨                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | Invalid HttpMethod: '..'. (無効な HttpMethod: '..'。)                                    | アクティビティのペイロードで指定された HTTP メソッドが Web アクティビティでサポートされていません。 | サポート対象の HTTP メソッドは PUT、POST、GET、DELETE です。 |
| 2108         | Invalid Server Error 500. (無効なサーバー エラー 500。)                                     | エンドポイントの内部エラー。                               | 該当 URL に対する機能を Fiddler または Postman を使用してチェックしてください。 |
| 2108         | Unauthorized 401. (許可されていません 401。)                                             | 有効な認証が要求にありません。                      | トークンの有効期限が切れている可能性があります。 有効な認証方法を指定してください。 該当 URL に対する機能を Fiddler または Postman を使用してチェックしてください。 |
| 2108         | Forbidden 403. (禁止 403。)                                                | 必要なアクセス許可がありません。                                 | アクセスしたリソースでユーザーのアクセス許可を確認してください。 該当 URL に対する機能を Fiddler または Postman を使用してチェックしてください。  |
| 2108         | Bad Request 400. (無効な要求 400。)                                              | HTTP 要求が無効です。                                         | 要求の URL、動詞、本文を確認してください。 Fiddler または Postman を使って要求を確認してください。  |
| 2108         | Not found 404. (見つかりません 404。)                                                | リソースが見つかりませんでした。                                       | Fiddler または Postman を使って要求を確認してください。  |
| 2108         | Service unavailable. (サービス利用不可。)                                          | サービスを利用できません。                                       | Fiddler または Postman を使って要求を確認してください。  |
| 2108         | Unsupported Media Type. (メディアの種類がサポートされていません。)                                       | コンテンツ タイプと Web アクティビティの本文が一致していません。           | ペイロードの形式と一致するコンテンツ タイプを指定してください。 Fiddler または Postman を使って要求を確認してください。 |
| 2108         | 探しているリソースは削除されたか、名前が変更されたか、または一時的に使用不可能になっています。 | このリソースは利用できません。                                | Fiddler または Postman を使ってエンドポイントを確認してください。 |
| 2108         | 無効なメソッド (HTTP 動詞) が使用されているため、検索しているページを表示できません。 | 誤った Web アクティビティ メソッドが要求に指定されました。   | Fiddler または Postman を使ってエンドポイントを確認してください。 |
| 2108         | invalid_payload                                              | Web アクティビティの本文が正しくありません。                       | Fiddler または Postman を使ってエンドポイントを確認してください。 |

Fiddler を使用して監視対象 Web アプリケーションの HTTP セッションを作成するには:

1. [Fiddler](https://www.telerik.com/download/fiddler) をダウンロードし、インストールして開きます。

1. Web アプリケーションで HTTPS が使用されている場合は、 **[Tools]\(ツール\)**  >  **[Fiddler Options]\(Fiddler オプション\)**  >  **[HTTPS]** の順に選択します。 **[Capture HTTPS CONNECTs]\(HTTPS 接続をキャプチャする\)** と **[Decrypt HTTPS traffic]\(HTTPS トラフィックの暗号化を解除する\)** をオンにします。 
   
   ![Fiddler オプション](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. お使いのアプリケーションで SSL 証明書が使用されている場合は、Fiddler の証明書をデバイスに追加します。 **[Tools]\(ツール\)**  >  **[Fiddler Options]\(Fiddler オプション\)**  >  **[HTTPS]**  >  **[Actions]\(アクション\)**  >  **[Export Root Certificate to Desktop]\(ルート証明書をデスクトップにエクスポート\)** に移動します。

1. **[File]\(ファイル\)**  >  **[Capture Traffic]\(トラフィックのキャプチャ\)** に移動して、キャプチャをオフにします。 または **F12** キーを押します。

1. キャッシュされたすべての項目が削除されて、再ダウンロードが必要になるように、ブラウザーのキャッシュをクリアします。

1. 要求を作成します。 

   a. **[Composer]\(コンポーザー\)** タブを選択します。

   b. HTTP メソッドと URL を設定します。

   c. 必要に応じてヘッダーと要求本文を追加します。

   d. **[Execute (実行)]** を選択します。

9. トラフィックのキャプチャを再び有効にし、ページで問題のあるトランザクションを完了します。

10. **[File]\(ファイル\)**  >  **[Save]\(保存\)**  >  **[All Sessions]\(すべてのセッション\)** に移動します。

詳細については、「[Fiddler の基本](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)」を参照してください。

## <a name="next-steps"></a>次の手順

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)




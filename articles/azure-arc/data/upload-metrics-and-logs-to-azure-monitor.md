---
title: 使用状況データ、メトリック、およびログを Azure Monitor にアップロードする
description: リソース インベントリ、使用状況データ、メトリック、およびログを Azure Monitor にアップロードする
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 69a2bc002b2ccccba90bde5b6c92d162b6e78815
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2020
ms.locfileid: "91952252"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>使用状況データ、メトリック、およびログを Azure Monitor にアップロードする

課金目的の使用状況情報、監視メトリック、およびログを定期的にエクスポートし、Azure にアップロードすることができます。  これらの 3 種類のデータのエクスポートとアップロードでは、データ コントローラー、SQL マネージド インスタンス、および Azure の PostgreSQL Hyperscale サーバー グループ リソースも作成および更新されます。

> [!NOTE] 
> プレビュー期間中は、Azure Arc 対応データ サービス利用のコストは発生しません。

## <a name="prerequisites"></a>前提条件

Azure CLI (az) と Azure Data CLI (azdata) がインストールされている必要があります。  [ツールをインストールします](./install-client-tools.md)。

Azure にデータをアップロードする前に、Azure サブスクリプションに Microsoft.AzureData リソース プロバイダーが登録されていることを確認する必要があります。

これを検証するには、次のコマンドを実行します。

```console
az provider show -n Microsoft.AzureData -o table
```

現在、リソース プロバイダーがサブスクリプションに登録されていない場合は、次のコマンドを実行して登録できます。  このコマンドは、完了するまで 1 - 2 分かかります。

```console
az provider register -n Microsoft.AzureData --wait
```

## <a name="upload-usage-data"></a>使用状況データをアップロードする

インベントリやリソースの使用状況などの使用状況情報は、次の 2 段階の方法で Azure にアップロードできます。

1. 次のように、```azdata export``` コマンドを使用して使用状況データをエクスポートします。

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the export command
   azdata arc dc export --type usage --path usage.json
   ```
   このコマンドにより、データ コントローラー上に作成されたすべての Azure Arc 対応データ リソース (SQL マネージド インスタンスや PostgreSQL ハイパースケール インスタンスなど) を含む `usage.json` ファイルが作成されます。

2. ```azdata upload``` コマンドを使用して使用状況データをアップロードする

   > [!NOTE]
   > アップロードを実行する前に、Azure Arc データ コントローラーを作成してから少なくとも 24 時間お待ちください。

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the upload command
   azdata arc dc upload --path usage.json
   ```

## <a name="upload-metrics-and-logs"></a>メトリックとログのアップロード

Azure Arc データ サービスを使用すると、必要に応じてメトリックやログを Azure Monitor にアップロードできます。これにより、メトリックの集計と分析、ログ記録、アラートの生成、通知の送信、自動化されたアクションのトリガーを行えます。 

Azure Monitor にデータを送信することで、監視とログのデータをオフサイトで大規模に保存することも可能になるため、高度な分析のためにデータを長期間保存できるようになります。

Azure Arc データ サービスを使用するサイトが複数ある場合は、サイト全体のすべてのログとメトリックを収集する中央の場所として Azure Monitor を利用できます。

### <a name="before-you-begin"></a>開始する前に

ログとメトリックのアップロード シナリオを有効にするために、1 回限り必要な、いくつかの手順から成る設定があります。

1. クライアントのアクセス シークレットの作成を含め、サービス プリンシパルまたは Azure Active Directory アプリケーションを作成し、そのサービス プリンシパルを、データベース インスタンスのリソースが配置されているサブスクリプションの "監視メトリック パブリッシャー" ロールに割り当てます。
2. ログ分析ワークスペースを作成し、キーを取得して、環境変数に情報を設定します。

最初の項目はメトリックをアップロードするために必要で、2 つ目はログをアップロードするために必要です。

以下のコマンドを順に実行して、メトリック アップロード用のサービス プリンシパルを作成し、サービス プリンシパルがメトリックをアップロードして作成およびアップロードの操作を実行できるように、それを "監視メトリック パブリッシャー" ロールと "共同作成者" ロールに割り当てます。

## <a name="create-service-principal-and-assign-roles"></a>サービス プリンシパルを作成してロールを割り当てる

以下のコマンドを順に実行して、メトリック アップロード用のサービス プリンシパルを作成し、それを "監視メトリック パブリッシャー" ロールに割り当てます。

サービス プリンシパルを作成するには、次のコマンドを実行します。

> [!NOTE]
> サービス プリンシパルを作成するには、[Azure で一定のアクセス許可](/azure/active-directory/develop/howto-create-service-principal-portal#permissions-required-for-registering-an-app)を持っている必要があります。

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

出力例:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

後で使用するため、appId とテナントの値を環境変数に保存します。 

PowerShell を使用して appId とテナントの値を保存するには、次の例に従います。

```powershell
$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'
```

あるいは、Linux または macOS で、次の例に従って appId とテナントの値を保存できます。

   ```console
   export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

   #Example (using Linux):
   export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
   export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
   export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
   ```

次のコマンドを実行して、サービス プリンシパルを、データベース インスタンスのリソースが配置されているサブスクリプションの "監視メトリック パブリッシャー" ロールに割り当てます。


> [!NOTE]
> Windows 環境から実行する場合は、ロール名に二重引用符を使用する必要があります。


```console
az role assignment create --assignee <appId value from output above> --role "Monitoring Metrics Publisher" --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123

#On Windows environment
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Contributor" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

出力例:

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>ログ分析ワークスペースを作成する

次に、以下のコマンドを実行して、Log Analytics ワークスペースを作成し、アクセス情報を環境変数内に設定します。

> [!NOTE]
> 既にワークスペースがある場合は、この手順をスキップしてください。

```console
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

出力例:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>環境変数に ID と共有キーを割り当てる

後で使用するため、customerId (ワークスペース ID) を環境変数として保存します。

```console
#PowerShell
$Env:WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Linux/macOS
export WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Example (using Linux)
#export WORKSPACE_ID='d6abb435-2626-4df1-b887-445fe44a4123'
```

このコマンドでは、ログ分析ワークスペースに接続するために必要なアクセスキーを出力します。

```console
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

出力例:

```console
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

後で使用するため、プライマリ キーを環境変数に保存します。

```console
#PowerShell:
$Env:WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Linux/macOS:
export WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Example (using Linux):
export WORKSPACE_SHARED_KEY='JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123=='

```

## <a name="set-final-environment-variables-and-confirm"></a>最終的な環境変数を設定して確認する

環境変数に SPN 機関の URL を設定します。

```console
#PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'

#Linux/macOS:
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

必要に応じて、必要なすべての環境変数が設定されていることを調べて確認します。

```console
#PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY

#Linux/macOS
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

## <a name="upload-metrics-to-azure-monitor"></a>メトリックを Azure Monitor にアップロードする

Azure Arc 対応 SQL マネージド インスタンスと Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのメトリックをアップロードするには、以下の CLI コマンドを実行します。

1. 指定したファイルにすべてのメトリックをエクスポートします。

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the metrics
   azdata arc dc export --type metrics --path metrics.json
   ```

2. メトリックを Azure Monitor にアップロードします。

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #upload the metrics
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >Azure Arc 対応データ インスタンスが最初のアップロード用に作成された後、少なくとも 30 分間待機します。
   >
   >Azure Monitor では過去 30 分間のメトリックのみが受け入れられるため、メトリックに対して `export` の後に直ちに `upload` を実行してください。 [詳細情報](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)


エクスポート中に "メトリックを取得できませんでした" というエラーが表示された場合は、次のコマンドを実行して、データ収集が ```true``` に設定されているかどうかを確認します。

```console
azdata arc dc config show
```

"security セクション" を参照してください。

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

`allowNodeMetricsCollection` プロパティと `allowPodMetricsCollection` プロパティが `true` に設定されているかどうかを確認します。

## <a name="view-the-metrics-in-the-portal"></a>ポータルでメトリックを表示する

メトリックがアップロードされたら、Azure portal で表示できるようになります。
> [!NOTE]
> ポータルでメトリックを表示する前に、アップロードしたデータが処理されるまでに数分かかる場合があることに注意してください。


ポータルでメトリックを表示するには、次のリンクを使用してポータルを開きます: <https://portal.azure.com>。次に、検索バーに名前を入力し、目的のデータベース インスタンスを検索します。

CPU 使用率は [概要] ページで確認できます。または、より詳細なメトリックが必要な場合は、左側のナビゲーション ウィンドウでメトリックをクリックできます。

メトリックの名前空間として [sql server] を選択します。

視覚化するメトリックを選択します (複数選択することもできます)。

頻度を過去 30 分に変更します。

> [!NOTE]
> メトリックをアップロードできるのは過去 30 分間のみです。 Azure Monitor では、30 分より古いメトリックは拒否されます。

## <a name="upload-logs-to-azure-monitor"></a>ログを Azure Monitor にアップロードする

 Azure Arc 対応 SQL マネージド インスタンスと Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのログをアップロードするには、以下の CLI コマンドを実行します。

1. 指定したファイルにすべてのログをエクスポートします。

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the logs
   azdata arc dc export --type logs --path logs.json
   ```

2. Azure Monitor のログ分析ワークスペースにログをアップロードします。

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #Upload the logs
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Azure portal でログを表示する

ログがアップロードされたら、ログ クエリ エクスプローラーを使用して次のようにクエリを実行できるようになります。

1. Azure portal を開いてから、上部の検索バーで名前を指定してワークスペースを検索し、それを選択します
2. 左側のパネルで [ログ] をクリックします
3. [作業の開始] をクリックします (または、Log Analytics を初めて使用する場合は、[作業の開始] ページのリンクをクリックして詳細を確認します)
4. Log Analytics を初めて使用する場合は、チュートリアルに従って Log Analytics の詳細を確認します
5. テーブルの一覧の下部にある [カスタム ログ] を展開すると、'sql_instance_logs_CL' という名前のテーブルが表示されます。
6. テーブル名の横にある目のアイコンをクリックします
7. [クエリ エディターで表示] ボタンをクリックします
8. これでクエリ エディター内にクエリが表示されるようになり、ログ内で最も新しい 10 個のイベントが表示されます
9. ここからは、クエリ エディターやアラートの設定を使用して、ログのクエリを試してみることができます。

## <a name="automating-uploads-optional"></a>アップロードの自動化 (省略可能)

メトリックとログをスケジュールに基づいてアップロードする場合は、スクリプトを作成して、タイマーで数分ごとに実行できます。 次に、Linux シェル スクリプトを使用してアップロードを自動化する例を示します。

お好きなテキスト エディターまたはコード エディターで、ファイルに次のスクリプトを追加し、.sh (Linux/Mac)、.cmd、.bat、.ps1 などのスクリプト実行可能ファイルとして保存します。

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

スクリプト ファイルを実行可能にする

```console
chmod +x myuploadscript.sh
```

20 分ごとにスクリプトを実行します。

```console
watch -n 1200 ./myuploadscript.sh
```

Cron や Windows タスク スケジューラのようなジョブ スケジューラを使用することも、Ansible、Puppet、Chef のようなオーケストレーターを使用することもできます。

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>使用状況、メトリックのエクスポートとアップロードに関する一般的なガイダンス

Azure Arc 対応データ サービスに対する作成、読み取り、更新、削除 (CRUD) 操作は、課金および監視の目的でログに記録されます。 これらの CRUD 操作を監視し、使用量を適切に計算するバックグラウンド サービスがあります。 実際の使用状況または使用量の計算は、スケジュールに従って、バックグラウンドで実行されます。 

プレビュー期間中、この処理は夜間に行われます。 一般的なガイダンスは、使用状況を 1 日に 1 回だけアップロードすることです。 使用状況情報をエクスポートし、同じ 24 時間以内に複数回アップロードすると、リソースの使用状況ではなく Azure portal のリソース インベントリのみが更新されます。

メトリックをアップロードする場合、Azure Monitor では過去 30 分間のデータのみが受け入れられます ([詳細を参照](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting))。 メトリックをアップロードするためのガイダンスは、エクスポート ファイルを作成した直後にメトリックをアップロードして、Azure portal でデータセット全体を表示できるようにすることです。 たとえば、午後 2:00 にメトリックをエクスポートし、午後 2:50 にアップロード コマンドを実行した場合などです。 Azure Monitor では過去 30 分間のデータのみが受け入れられるため、ポータルにデータが表示されない場合があります。 

## <a name="next-steps"></a>次の手順

[課金データを Azure にアップロードして Azure portal に表示する](view-billing-data-in-azure.md)

[Azure portal で Azure Arc データ コントローラー リソースを表示する](view-data-controller-in-azure-portal.md)

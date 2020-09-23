---
title: リソース インベントリ、使用状況データ、メトリック、およびログを Azure Monitor にアップロードする
description: リソース インベントリ、使用状況データ、メトリック、およびログを Azure Monitor にアップロードする
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ac6ffd2b5bf48079db6a0cd261dbe2535e1821ac
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90931294"
---
# <a name="upload-resource-inventory-usage-data-metrics-and-logs-to-azure-monitor"></a>リソース インベントリ、使用状況データ、メトリック、およびログを Azure Monitor にアップロードする

Azure Arc データ サービスを使用すると、*必要に応じて*メトリックやログを Azure Monitor にアップロードできます。これにより、メトリックの集計と分析、ログ記録、アラートの生成、通知の送信、自動化されたアクションのトリガーを行えます。 Azure Monitor にデータを送信することで、監視とログのデータをオフサイトで大規模に保存することも可能になるため、高度な分析のためにデータを長期間保存できるようになります。  Azure Arc データ サービスを使用するサイトが複数ある場合は、サイト全体のすべてのログとメトリックを収集する中央の場所として Azure Monitor を利用できます。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>開始する前に

ログとメトリックのアップロード シナリオを有効にするために、1 回限り必要な、いくつかの手順から成る設定があります。

1) クライアントのアクセス シークレットの作成を含め、サービス プリンシパル/Azure Active Directory アプリケーションを作成し、そのサービス プリンシパルを、データベース インスタンスのリソースが配置されているサブスクリプションの "監視メトリック パブリッシャー" ロールに割り当てます。
2) ログ分析ワークスペースを作成し、キーを取得して、環境変数に情報を設定します。

最初の項目はメトリックをアップロードするために必要で、2 つ目はログをアップロードするために必要です。

以下のコマンドを順に実行して、メトリック アップロード用のサービス プリンシパルを作成し、サービス プリンシパルがメトリックをアップロードして作成およびアップロードの操作を実行できるように、それを "監視メトリック パブリッシャー" ロールと "共同作成者" ロールに割り当てます。

## <a name="create-service-principal-and-assign-roles"></a>サービス プリンシパルを作成してロールを割り当てる

以下のコマンドを順に実行して、メトリック アップロード用のサービス プリンシパルを作成し、それを "監視メトリック パブリッシャー" ロールに割り当てます。

サービス プリンシパルを作成するには、次のコマンドを実行します。

> [!NOTE]
> サービス プリンシパルを作成するには、[Azure で一定のアクセス許可](/active-directory/develop/howto-create-service-principal-portal#required-permissions)を持っている必要があります。

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

出力例:

```console
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

後で使用するため、appId とテナントの値を環境変数に保存します。

```console
#PowerShell

$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Linux/macOS

export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Example (using Linux):
export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
```

次のコマンドを実行して、サービス プリンシパルを、データベース インスタンスのリソースが配置されているサブスクリプションの "監視メトリック パブリッシャー" ロールに割り当てます。

```console
az role assignment create --assignee <appId value from output above> --role 'Monitoring Metrics Publisher' --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Monitoring Metrics Publisher' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

出力例:

```console
{
  "canDelegate": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>ログ分析ワークスペースを作成する

次に、以下のコマンドを実行して、Log Analytics ワークスペースを作成し、アクセス情報を環境変数内に設定します。

> [!NOTE]
> 既にワークスペースがある場合は、この手順をスキップしてください。

```console
az monitor log-analytics workspace create --resource-group <resource group name> --name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --name MyLogsWorkpace
```

出力例:

```console
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
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
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --name MyLogsWorkpace
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

Azure SQL マネージド インスタンスと Azure Database for PostgreSQL Hyperscale サーバー グループのメトリックをアップロードするには、以下の CLI コマンドを実行します。

これにより、すべてのメトリックが指定したファイルにエクスポートされます。

```console
azdata arc dc export -t metrics --path metrics.json
```

これにより、Azure Monitor にメトリックがアップロードされます。

```console
azdata arc dc upload --path metrics.json
```

## <a name="view-the-metrics-in-the-portal"></a>ポータルでメトリックを表示する

メトリックがアップロードされたら、Azure portal からそれらを視覚化できるようになります。

ポータルでメトリックを表示するには、次の特別なリンクを使用してポータルを開きます:<https://portal.azure.com>。次に、検索バーに名前を入力し、目的のデータベース インスタンスを検索します。

CPU 使用率は [概要] ページで確認できます。または、より詳細なメトリックが必要な場合は、左側のナビゲーション ウィンドウでメトリックをクリックできます。

メトリックの名前空間として [sql server] を選択します。

視覚化するメトリックを選択します (複数選択することもできます)。

頻度を過去 30 分に変更します。

> [!NOTE]
> メトリックをアップロードできるのは過去 30 分間のみです。 Azure Monitor では、30 分より古いメトリックは拒否されます。

## <a name="upload-logs-to-azure-monitor"></a>ログを Azure Monitor にアップロードする

 Azure SQL マネージド インスタンスと Azure Database for PostgreSQL Hyperscale サーバー グループのログをアップロードするには、以下の CLI コマンドを実行します。

これにより、すべてのログが指定したファイルにエクスポートされます。

```console
azdata arc dc export -t logs --path logs.json
```

これにより、Azure Monitor のログ分析ワークスペースにログがアップロードされます。

```console
azdata arc dc upload --path logs.json
```

## <a name="view-your-logs-in-azure-portal"></a>Azure portal でログを表示する

ログがアップロードされたら、ログ クエリ エクスプローラーを使用して次のようにクエリを実行できるようになります。

1. Azure portal を開いてから、上部の検索バーで名前を指定してワークスペースを検索し、それを選択します
2. 左側のパネルで [ログ] をクリックします
3. [作業の開始] をクリックします (または、Log Analytics を初めて使用する場合は、[作業の開始] ページのリンクをクリックして詳細を確認します)
4. 初めての場合は、チュートリアルに従って Log Analytics の詳細を確認します
5. テーブルの一覧の下部にある [カスタム ログ] を展開すると、"sql_instance_logs_CL" という名前のテーブルが表示されます。
6. テーブル名の横にある目のアイコンをクリックします
7. [クエリ エディターで表示] ボタンをクリックします
8. これでクエリ エディター内にクエリが表示されるようになり、ログ内で最も新しい 10 個のイベントが表示されます
9. ここから、クエリ エディターを使用してログのクエリを試すこと、アラートを設定することなどが可能です。

## <a name="automating-metrics-and-logs-uploads-optional"></a>メトリックとログのアップロードの自動化 (省略可能)

メトリックとログを絶えずアップロードする場合は、スクリプトを作成して、タイマーで数分ごとに実行できます。  次に、Linux シェル スクリプトを使用してアップロードを自動化する例を示します。

お好きなテキスト エディターまたはコード エディターで、スクリプトの内容に次のコードを追加し、.sh (Linux/Mac)、.cmd、.bat、.ps1 などのスクリプト実行可能ファイルとして保存します。

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

スクリプト ファイルを実行可能にする

```console
chmod +x myuploadscript.sh
```

2 分ごとにスクリプトを実行します。

```console
watch -n 120 ./myuploadscript.sh
```

Cron や Windows タスク スケジューラのようなジョブ スケジューラを使用することも、Ansible、Puppet、Chef のようなオーケストレーターを使用することもできます。

---
title: Azure Data Explorer を使用して Azure Monitor のデータのクエリを実行する (プレビュー)
description: Azure Data Explorer を使用して、Azure Data Explorer と、Azure Monitor の Log Analytics ワークスペースおよび従来の Application Insights アプリケーション間で製品をまたぐクエリを実行します。
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 9b434c426264fcfee0dfe663a7d1b21a354badec
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491258"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Azure Data Explorer を使用して Azure Monitor のデータのクエリを実行する (プレビュー)
Azure Data Explorer プロキシ クラスターによって、Azure Data Explorer と、Azure Monitor の Log Analytics ワークスペースおよび従来の Application Insights アプリケーション間で製品をまたぐクエリを実行できます。 Azure Monitor の Log Analytics ワークスペースまたは従来の Application Insights アプリをプロキシ クラスターとしてマップできます。 その後、Azure Data Explorer ツールを使用してプロキシ クラスターにクエリを実行し、それをクロス クラスター クエリで参照できます。 この記事では、プロキシ クラスターに接続し、プロキシ クラスターを Azure Data Explorer の Web UI に追加した後、Azure Data Explorer から Log Analytics ワークスペースまたは従来の Application Insights アプリに対してクエリを実行する方法を示します。

次の図は、Azure Data Explorer プロキシのフローを示しています。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-monitor-proxy-flow.png" alt-text="Azure Data Explorer プロキシのフロー":::


> [!NOTE]
> Azure Data Explorer プロキシはパブリック プレビューの段階にあります。 [プロキシに接続](#connect-to-the-proxy)して、クラスターのプロキシ機能を有効にします。 

## <a name="connect-to-the-proxy"></a>プロキシに接続する
Log Analytics ワークスペースまたは従来の Application Insights アプリに接続するには、[Azure Data Explorer の Web UI](https://dataexplorer.azure.com/clusters) を開きます。 Log Analytics または Application Insights クラスターに接続する前に、Azure Data Explorer ネイティブ クラスター ( *help* クラスターなど) が左側のメニューに表示されていることを確認します。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Azure Data Explorer ネイティブ クラスター":::

**[クラスターの追加]** をクリックし、Log Analytics または Application Insights のクラスターの URL を、次のいずれかの形式で追加します。 
    
* Log Analytics の場合: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
* Application Insights の場合: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

**[追加]** をクリックして接続を確立します。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="クラスターを追加する":::
 
> [!NOTE]
> 複数のプロキシ クラスターに接続を追加する場合は、それぞれに異なる名前を付けます。 そうしないと、左側のウィンドウですべてが同じ名前になります。

接続が確立されると、Log Analytics または Application Insights のクラスターが、ネイティブの Azure Data Explorer クラスターと共に左側のペインに表示されます。 

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics と Azure Data Explorer のクラスター":::
 
> [!NOTE]
> マップできる Azure Monitor ワークスペースの数は、100 に制限されています。

## <a name="create-queries-using-azure-monitor-data"></a>Azure Monitor データを使用してクエリを作成する

Kusto クエリをサポートするクライアント ツールを使用してクエリを実行できます。例:Kusto Explorer、Azure Data Explorer の Web UI、Jupyter Kqlmagic、Flow、PowerQuery、PowerShell、Jarvis、Lens、REST API。

> [!NOTE]
> Azure Data Explorer プロキシ機能は、データ取得のみに使用されます。 詳細については、「[関数のサポート](#function-supportability)」を参照してください。

> [!TIP]
> * データベース名は、プロキシ クラスターで指定したリソースと同じ名前にする必要があります。 名前は大文字と小文字が区別されます。
> * クロス クラスター クエリでは、Application Insights アプリと Log Analytics ワークスペースの名前付けが正しいことを確認してください。
>     * 名前に特殊文字が含まれている場合は、プロキシ クラスター名の URL エンコードで置き換えられます。 
>     * 名前に [KQL 識別子の名前規則](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/entity-names)を満たしていない文字が含まれている場合は、ダッシュ **-** 文字で置き換えられます。

### <a name="direct-query-from-your-log-analytics-or-application-insights-proxy-cluster"></a>Log Analytics または Application Insights のプロキシ クラスターからの直接クエリ

Log Analytics または Application Insights のクラスターからクエリを実行します。 左側のペインでクラスターが選択されていることを確認します。 
 
```kusto
Perf | take 10 // Demonstrate query through the proxy on the Log Analaytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Log Analytics ワークスペースにクエリを実行する":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-proxy-cluster-and-the-azure-data-explorer-native-cluster"></a>Log Analytics または Application Insights のプロキシ クラスターと Azure Data Explorer ネイティブ クラスターのクロス クエリ

プロキシからクロス クラスター クエリを実行する場合は、左側のペインで Azure Data Explorer ネイティブ クラスターが選択されていることを確認してください。 次の例では、[union](/azure/data-explorer/kusto/query/unionoperator) 演算子と Log Analytics ワークスペースを使用して、Azure Data Explorer クラスター テーブルを結合する方法を示します。

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```
union の代わりに [`join` 演算子](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor)を使用するには、それを (プロキシに対してではなく) Azure Data Explorer ネイティブ クラスターに対して実行するための[ヒント](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor#join-hints)が必要になる場合があります。 

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>一方のテナントの Azure Data Explorer クラスターのデータを他方の Azure Monitor リソースと結合する

クロステナント クエリは Azure Data Explorer プロキシでサポートされていません。 両方のリソースにまたがるクエリを実行するためには、1 つのテナントにサインインします。

Azure Data Explorer リソースがテナント 'A' にあり、Log Analytics ワークスペースがテナント 'B' にある場合は、次の 2 つの方法のいずれかを使用します。

- Azure Data Explorer を使用すると、異なるテナントにプリンシパルのロールを追加できます。 Azure Data Explorer クラスターにテナント 'B' のユーザー ID を許可されているユーザーとして追加します。 Azure Data Explorer クラスター上の *['TrustedExternalTenant'](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* プロパティにテナント 'B' が含まれていることを検証します。 テナント 'B' でクロスクエリを完全に実行します。

- [Lighthouse](/azure/lighthouse/) を使用して、Azure Monitor リソースをテナント 'A' に射影します。

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>さまざまなテナントから Azure Data Explorer クラスターに接続する

Kusto Explorer では、ユーザー アカウントが最初に属しているテナントに自動的にサインインされます。 同じユーザー アカウントを使用して他のテナントのリソースにアクセスするには、接続文字列に `tenantId` を明示的に指定する必要があります: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`**TenantId**

## <a name="function-supportability"></a>関数のサポート

Azure Data Explorer プロキシ クラスターでは、Log Analytics と Application Insights の両方の関数がサポートされています。 この機能により、クロス クラスター クエリで Azure Monitor の表形式関数を直接参照できます。

以下のコマンドがプロキシによってサポートされています。

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

次の図は、Azure Data Explorer の Web UI から表形式関数にクエリを実行する例を示しています。 関数を使用するには、[クエリ] ウィンドウで名前を実行します。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Azure Data Explorer の Web UI から表形式関数のクエリを実行する":::
 
> [!NOTE]
> Azure Monitor は、パラメーターをサポートしない表形式関数のみをサポートしています。

## <a name="additional-syntax-examples"></a>その他の構文例

Log Analytics または Application Insights のクラスターを呼び出す場合は、次の構文オプションを使用できます。

|構文の説明  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| このサブスクリプションで定義されているリソースのみを含むクラスター内のデータベース ( **クロス クラスター クエリの場合に推奨** ) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| このサブスクリプション内のすべてのアプリ/ワークスペースを含むクラスター    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|サブスクリプション内のすべてのアプリ/ワークスペースを含み、このリソース グループのメンバーであるクラスター    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|このサブスクリプションで定義されているリソースのみを含むクラスター      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>次のステップ

- [Log Analytics ワークスペースと Application Insights のデータ構造](data-platform-logs.md)の詳細をご覧ください。
- [Azure Data Explorer のクエリの記述](https://docs.microsoft.com/azure/data-explorer/write-queries)に関するページをご覧ください。
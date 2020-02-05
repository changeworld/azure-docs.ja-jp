---
title: Azure Data Explorer を使用して Azure Monitor でデータのクエリを実行する (プレビュー)
description: このトピックでは、Application Insights と Log Analytics でのクロス積クエリ用の Azure Data Explorer プロキシを作成することによって、Azure Monitor でデータのクエリを実行します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: d39ffa05448600fe3bd09baf6080aa1565ae19ba
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843585"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Azure Data Explorer を使用して Azure Monitor でデータのクエリを実行する (プレビュー)

Azure Data Explorer プロキシ クラスター (ADX プロキシ) は、[Azure Monitor](/azure/azure-monitor/) サービスで Azure Data Explorer、[Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)、および [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) の間のクロス積クエリを実行できるようにするエンティティです。 Azure Monitor Log Analytics ワークスペースまたは Application Insights アプリをプロキシ クラスターとしてマップできます。 その後、Azure Data Explorer ツールを使用してプロキシ クラスターにクエリを実行し、それをクロス クラスター クエリで参照できます。 この記事では、プロキシ クラスターに接続し、プロキシ クラスターを Azure Data Explorer の Web UI に追加した後、Azure Data Explorer から AI アプリまたは LA ワークスペースに対してクエリを実行する方法を示します。

Azure Data Explorer プロキシのフロー: 

![ADX プロキシのフロー](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>前提条件

> [!NOTE]
> ADX プロキシはプレビュー モードです。 [プロキシに接続](#connect-to-the-proxy)して、クラスターの ADX プロキシ機能を有効にします。 ご質問がある場合は、[ADXProxy](mailto:adxproxy@microsoft.com) チームにお問い合わせください。

## <a name="connect-to-the-proxy"></a>プロキシに接続する

1. Log Analytics または Application Insights クラスターに接続する前に、Azure Data Explorer ネイティブ クラスター (*help* クラスターなど) が左側のメニューに表示されていることを確認します。

    ![ADX ネイティブ クラスター](media/adx-proxy/web-ui-help-cluster.png)

1. Azure Data Explorer の UI (https://dataexplorer.azure.com/clusters) ) で、 **[クラスターの追加]** を選択します。

1. **[クラスターの追加]** ウィンドウで、LA または AI クラスターへの URL を追加します。 
    
    * LA の場合: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * AI の場合: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * **[追加]** を選択します。

    ![クラスターを追加する](media/adx-proxy/add-cluster.png)

    複数のプロキシ クラスターに接続を追加する場合は、それぞれに異なる名前を付けます。 そうしないと、左側のウィンドウですべてが同じ名前になります。

1. 接続が確立されると、LA または AI クラスターが ADX ネイティブ クラスターと共に左側のウィンドウに表示されます。 

    ![Log Analytics クラスターと Azure Data Explorer クラスター](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>クエリを実行する

Kusto クエリをサポートするクライアント ツール (Kusto Explorer、ADX Web UI、Jupyter Kqlmagic、Flow、PowerQuery、PowerShell、Jarvis、Lens、REST API など) を使用してクエリを実行できます。

> [!TIP]
> * データベース名は、プロキシ クラスターで指定したリソースと同じ名前にする必要があります。 名前は大文字と小文字が区別されます。
> * クロス クラスター クエリでは、Application Insights アプリと Log Analytics ワークスペースの名前付けが正しいことを確認してください。
>     * 名前に特殊文字が含まれている場合は、プロキシ クラスター名の URL エンコードで置き換えられます。 
>     * 名前に [KQL 識別子の名前規則](/azure/kusto/query/schema-entities/entity-names)を満たしていない文字が含まれている場合は、ダッシュ **-** 文字で置き換えられます。

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>LA または AI ADX プロキシ クラスターからの直接クエリ

LA または AI クラスターでクエリを実行します。 左側のペインでクラスターが選択されていることを確認します。 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![LA ワークスペースにクエリを実行する](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>LA または AI ADX プロキシ クラスターと ADX ネイティブ クラスターのクロス クエリ 

プロキシからクロス クラスター クエリを実行する場合は、左側のウィンドウで ADX ネイティブ クラスターが選択されていることを確認してください。 次の例は、ADX クラスター テーブルの LA ワークスペースとの (`union` を使用した) 組み合わせを示しています。

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [ ![Azure Data Explorer プロキシからのクロス クエリ](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

union の代わりに [`join` 演算子](/azure/kusto/query/joinoperator)を使用するには、それを (プロキシに対してではなく) Azure Data Explorer ネイティブ クラスターに対して実行するための [`hint`](/azure/kusto/query/joinoperator#join-hints) が必要になる場合があります。 

## <a name="additional-syntax-examples"></a>その他の構文例

Application Insights (AI) または Log Analytics (LA) クラスターを呼び出す場合は、次の構文オプションを使用できます。

|構文の説明  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| このサブスクリプションで定義されているリソースのみを含むクラスター内のデータベース (**クロス クラスター クエリの場合に推奨**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| このサブスクリプション内のすべてのアプリ/ワークスペースを含むクラスター    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|サブスクリプション内のすべてのアプリ/ワークスペースを含み、このリソース グループのメンバーであるクラスター    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|このサブスクリプションで定義されているリソースのみを含むクラスター      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>次のステップ

[クエリを作成する](write-queries.md)

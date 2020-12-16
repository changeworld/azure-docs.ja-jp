---
title: Azure Monitor を使用した Azure Data Explorer のクロス リソース クエリ
description: Azure Monitor を使用して、Azure Data Explorer、Log Analytics ワークスペース、Azure Monitor の従来の Application Insights アプリケーションの間で製品にまたがるクエリを実行します。
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 5cb2f7b3b07c20e09d61e97412bc35f03b15cb3b
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572152"
---
# <a name="cross-resource-query-azure-data-explorer-using-azure-monitor"></a>Azure Monitor を使用した Azure Data Explorer のクロス リソース クエリ
Azure Monitor では、Azure Data Explorer、[Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)、[Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) の間のクロス サービス クエリがサポートされています。 それにより、Log Analytics または Application Insights ツールを使用して Azure Data Explorer クラスターに対してクエリを実行し、それをクロス サービス クエリで参照することができます。 この記事では、クロス サービス クエリを作成する方法について説明します。

Azure Monitor のクロス サービス フロー::::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Azure Monitor と Azure Data Explorer のクロス サービス フロー。":::

>[!NOTE]
>* Azure Monitor のクロス サービス クエリはプライベート プレビュー段階にあり、許可リストが必要です。
>* 不明な点がある場合は、[サービス チーム](mailto:ADXProxy@microsoft.com)にお問い合わせください。
## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Log Analytics または Application Insights リソースと Azure Data Explorer のクロス クエリ

次のような、Kusto クエリをサポートするクライアント ツールを使用してクロス リソース クエリを実行できます。Log Analytics の Web UI、Workbooks、PowerShell、REST API など。

* Azure Data Explorer クラスターの識別子をクエリの 'adx' パターン内に入力し、その後にデータベース名とテーブルを入力します。

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="クロス サービス クエリの例。":::

> [!NOTE]
>* データベース名は大文字と小文字が区別されます。
>* アラートとしてのクロス リソース クエリはサポートされていません。
## <a name="combining-azure-data-explorer-cluster-tables-using-union-and-join-with-la-workspace"></a>Azure Data Explorer クラスター テーブルと LA ワークスペースとの (union と join を使用した) 組み合わせ。

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="union を使用したクロス サービス クエリの例。":::

>[!Tip]
>* 短縮形が許可されています (ClusterName/InitialCatalog)。 たとえば、adx('help/Samples') は adx('help.kusto.windows.net/Samples') に変換されます。
## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>一方のテナントの Azure Data Explorer クラスターのデータを他方の Azure Monitor リソースと結合する

サービス間のクロス テナント クエリはサポートされていません。 両方のリソースにまたがるクエリを実行するためには、1 つのテナントにサインインします。

Azure Data Explorer リソースがテナント 'A' にあり、Log Analytics ワークスペースがテナント 'B' にある場合は、次の 2 つの方法のいずれかを使用します。

*  Azure Data Explorer を使用すると、異なるテナントにプリンシパルのロールを追加できます。 Azure Data Explorer クラスターにテナント 'B' のユーザー ID を許可されているユーザーとして追加します。 Azure Data Explorer クラスター上の *['TrustedExternalTenant'](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* プロパティにテナント 'B' が含まれていることを検証します。 テナント 'B' でクロスクエリを完全に実行します。
*  [Lighthouse](https://docs.microsoft.com/azure/lighthouse/) を使用して、Azure Monitor リソースをテナント 'A' に射影します。
## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>さまざまなテナントから Azure Data Explorer クラスターに接続する

Kusto Explorer では、ユーザー アカウントが最初に属しているテナントに自動的にサインインされます。 同じユーザー アカウントを使用して他のテナントのリソースにアクセスするには、接続文字列に `tenantId` を明示的に指定する必要があります: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`**TenantId**

## <a name="next-steps"></a>次のステップ
* [クエリを作成する](https://docs.microsoft.com/azure/data-explorer/write-queries)
* [Azure Data Explorer を使用して Azure Monitor のデータのクエリを実行する](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)
* [Azure Monitor でクロスリソース ログ クエリを実行する](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query)

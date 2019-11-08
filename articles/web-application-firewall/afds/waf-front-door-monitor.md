---
title: Azure Web アプリケーション ファイアウォールの監視とログ記録
description: Front Door の監視とログ記録での Web アプリケーション ファイアウォール (WAF) について説明します
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 0c705139c082f13f40362e598f0fda9ba0a128a5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510176"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure Web アプリケーション ファイアウォールの監視とログ記録 

Azure Web アプリケーション ファイアウォール (WAF) の監視とログ記録は、Azure Monitor および Azure Monitor ログのログ記録およびそれらとの統合を通じて提供されます。

## <a name="azure-monitor"></a>Azure Monitor

Front Door での WAF のログは、[Azure Monitor](../../azure-monitor/overview.md) と統合されます。 Azure Monitor により、WAF のアラートやログなどの診断情報を追跡できます。 Front Door リソース内での WAF の監視は、ポータルの **[診断]** タブから、または Azure Monitor サービスから直接、構成できます。

Azure portal から、Front Door リソースの種類に移動します。 左側の **[監視]** > **[メトリック]** タブから **WebApplicationFirewallRequestCount** を追加し、WAF 規則に一致する要求の数を追跡できます。 アクションの種類と規則の名前に基づいて、カスタム フィルターを作成できます。

![WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>ログと診断

Front Door の WAF では、検出された脅威ごとに詳細なレポートが提供されます。 ログ記録は Azure Diagnostics ログに統合されており、json 形式でアラートが記録されます。 これらのログは、[Azure Monitor ログ](../../azure-monitor/insights/azure-networking-analytics.md)と統合できます。

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog では、顧客のバックエンドに転送されるすべての要求が記録されます。 FrontdoorWebApplicationFirewallLog では、WAF 規則と一致するすべての要求が記録されます。

次のクエリ例では、ブロックされた要求についての WAF ログを取得します。

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

次のクエリ例では、AccessLogs のエントリを取得します。

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>次の手順

- [Front Door](../../frontdoor/front-door-overview.md) の詳細を確認する。


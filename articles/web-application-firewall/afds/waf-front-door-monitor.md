---
title: Azure Web アプリケーション ファイアウォールの監視とログ記録
description: Front Door の監視とログ記録での Web アプリケーション ファイアウォール (WAF) について説明します
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: b4f666415a96307b89022c6caf6af90581f294f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115365"
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

WAF ログに記録された要求の例を次に示します。

``` WAFlogQuerySample
{
    "PreciseTimeStamp": "2020-01-25T00:11:19.3866091Z",
    "time": "2020-01-25T00:11:19.3866091Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "xx.xx.xxx.xxx",
        "socketIP": "xx.xx.xxx.xxx",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/?q=../../x",
        "ruleName": "Microsoft_DefaultRuleSet-1.1-LFI-930100",
        "policy": "WafDemoCustomPolicy",
        "action": "Block",
        "host": "wafdemofrontdoorwebapp.azurefd.net",
        "refString": "0p4crXgAAAABgMq5aIpu0T6AUfCYOroltV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode": "prevention"
    }
}

``` 

次のクエリ例では、AccessLogs のエントリを取得します。

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

アクセス ログに記録された要求の例を次に示します。

``` AccessLogSample
{
    "PreciseTimeStamp": "2020-01-25T00:11:12.0160150Z",
    "time": "2020-01-25T00:11:12.0160150Z",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0n4crXgAAAACnRKbdALbyToAqNfSHssDvV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "httpMethod": "GET",
        "httpVersion": "2.0",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/",
        "requestBytes": "710",
        "responseBytes": "3116",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4017.0 Safari/537.36 Edg/81.0.389.2",
        "clientIp": "xx.xx.xxx.xxx",
        "timeTaken": "0.598",
        "securityProtocol": "TLS 1.2",
        "routingRuleName": "WAFdemoWebAppRouting",
        "backendHostname": "wafdemouksouth.azurewebsites.net:443",
        "sentToOriginShield": false,
        "httpStatusCode": "200",
        "httpStatusDetails": "200"
    }
}

```

## <a name="next-steps"></a>次のステップ

- [Front Door](../../frontdoor/front-door-overview.md) の詳細を確認する。

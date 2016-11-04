---
title: Microsoft Azure のアラートの概要 | Microsoft Docs
description: アラートにより、Azure リソースのメトリック、イベント、またはログを監視し、指定した条件が満たされたときに通知します。
author: rboucher
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2016
ms.author: robb

---
# Microsoft Azure のアラートの概要
この記事では、アラートの概要、利点、および使用方法について説明します。

## アラートとは
アラートとは、Azure リソースのメトリック、イベント、またはログを監視し、指定した条件が満たされたときに通知する手段です。

アラートは、以下の値とイベントに基づいて受け取ることができます。

* メトリック値 - アラートは、指定したメトリックの値が、割り当てたしきい値をいずれかの方向で超えたときにトリガーされます。つまり、条件を最初に満たしたときと、後でその条件を満たさなくなったときの両方でトリガーされます。
* アクティビティ ログ イベント - アラートは、"すべて" のイベントに対して、または発生したイベントが特定の数に達したときにのみトリガーされます。

## さまざまな Azure サービスのアラート
アラートは、さまざまなサービスで使用できます

* **Application Insights** - Web テストとメトリックのアラートが許可されています。「[Application Insights のアラートの設定](../application-insights/app-insights-alerts.md)」および「[Web サイトの可用性と応答性の監視](../application-insights/app-insights-monitor-web-app-availability.md)」を参照してください。
* **OMS Log Analytics** - 診断ログを Log Analytics にルーティングできます。OMS では、メトリック、ログなどのアラートの種類が許可されています。詳細については、「[Log Analytics のアラート](../log-analytics/log-analytics-alerts.md)」を参照してください。
* **Azure Monitor** - Azure Monitor では、メトリック値とアクティビティ ログ イベントの両方に基づいてアラートが許可されます。Azure Monitor には、[Azure Insights REST API](https://msdn.microsoft.com/library/dn931943.aspx) が含まれます。[Azure ポータル、PowerShell、またはコマンド ライン インターフェイスでのアラートの作成](../monitoring-and-diagnostics/insights-alerts-portal.md)に関するページをご覧ください

## アラート アクション
アラートがトリガーされたときに実行されるように構成できる処理は次のとおりです。

* 電子メール通知を、サービス管理者、共同管理者、指定した追加の電子メール アドレスに送信する。
* webhook を呼び出す。これにより、追加のオートメーション アクションを実行できます
  
  ![アラートについて説明します。](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)

## 次のステップ
アラート ルールと、以下を使用してそのルールを構成する方法を確認します

* [Azure ポータル](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [コマンド ライン インターフェイス (CLI)](insights-alerts-command-line-interface.md)
* [Azure Insights REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

<!---HONumber=AcomDC_0928_2016-->
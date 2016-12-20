---
title: "Microsoft Azure のアラートの概要 | Microsoft Docs"
description: "アラートを使用すると、Azure リソースのメトリック、イベント、またはログを監視し、指定した条件が満たされたときに通知を受けることができます。"
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f80179a487d08244cbd5e57d34b10c4ab67b3cf5


---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Microsoft Azure のアラートの概要
この記事では、アラートの概要、利点、使用方法について説明します。  

## <a name="what-are-alerts"></a>アラートとは
アラートとは、Azure リソースのメトリック、イベント、またはログを監視し、指定した条件が満たされたときに通知する手段です。

アラートは、以下の値とイベントに基づいて受け取ることができます。

* **メトリック値**: アラートは、指定したメトリックの値が、割り当てたしきい値をいずれかの方向で超えたときにトリガーされます。 つまり、条件を最初に満たしたときと、後でその条件を満たさなくなったときの両方でトリガーされます。
* **アクティビティ ログ イベント**: アラートは、すべてのイベントに対して、または発生したイベントが特定の数に達したときにのみトリガーされます。

## <a name="alerts-in-different-azure-services"></a>さまざまな Azure サービスのアラート
アラートは、次のようなさまざまなサービスで使用できます。

* **Application Insights**: Web テストとメトリックのアラートを有効にします。 「[Application Insights のアラートの設定](../application-insights/app-insights-alerts.md)」と「[Web サイトの可用性と応答性の監視](../application-insights/app-insights-monitor-web-app-availability.md)」をご覧ください。
* **Log Analytics (Operations Management Suite)**: 診断ログの Log Analytics へのルーティングを有効にします。 Operations Management Suite では、メトリック、ログなどのアラートの種類が許可されています。 詳細については、「 [Log Analytics のアラート](../log-analytics/log-analytics-alerts.md)」を参照してください。  
* **Azure Monitor**: メトリック値とアクティビティ ログ イベントの両方に基づいてアラートを有効にします。 Azure Monitor には、[Azure Monitor REST API](https://msdn.microsoft.com/library/dn931943.aspx) が含まれます。  詳細については、[Azure Portal、PowerShell、またはコマンド ライン インターフェイスでのアラートの作成](insights-alerts-portal.md)に関するページをご覧ください。

## <a name="alert-actions"></a>アラート アクション
次を実行するようにアラートを構成できます。

* メール通知をサービス管理者、共同管理者、または指定した追加のメール アドレスに送信する。
* webhook を呼び出す。これにより、追加のオートメーション アクションを実行できます。
  
  ![アラートについて説明します。](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)

## <a name="next-steps"></a>次のステップ
アラート ルールとその構成方法については、以下をご覧ください。

* [Azure ポータル](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [コマンド ライン インターフェイス (CLI)](insights-alerts-command-line-interface.md)
* [Azure 監視 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)




<!--HONumber=Nov16_HO3-->



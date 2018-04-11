---
title: Microsoft Azure と Azure Monitor でのクラシック アラートの概要 | Microsoft Docs
description: アラートを使用すると、Azure リソースのメトリック、イベント、またはログを監視し、指定した条件が満たされたときに通知を受けることができます。
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: robb
ms.openlocfilehash: 06ba05f71cf1f696033099c04448526a0421ad42
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Microsoft Azure のクラシック アラートの概要

> [!NOTE]
> この記事では、古いクラシック メトリック アラートの作成方法について説明します。 Azure Monitor では、[新しいほぼリアルタイムのメトリック アラート](monitoring-overview-unified-alerts.md)がサポートされるようになっています。
>

この記事では、Microsoft Azure のさまざまなソースのアラート、アラートの目的、アラートの利点、アラートとの基本的な使用方法について説明します。 具体的には、Azure Monitor のクラシック アラートに適用されます。 アラートは Azure の監視方法の 1 つです。データに対する条件を構成し、その条件が最新の監視データと一致したときに通知を受信できます。


## <a name="taxonomy-of-azure-monitor-classic-alerts"></a>Azure Monitor クラシック アラートの分類
Azure では、クラシック アラートとその機能を説明するときに次の用語を使用します。
* **アラート** - 一致したときにアクティブになる条件 (1 つまたは複数の規則または条件) の定義。
* **アクティブ** - クラシック アラートによって定義されている条件と一致するときの状態。
* **解決済み** - クラシック アラートによって定義されている条件と以前は一致していたが、一致しなくなった状態。
* **通知** - クラシック アラートがアクティブになったことに基づいて実行されるアクション。
* **アクション** - 通知の受信側に送信される特定の呼び出し (たとえば、アドレスへの電子メール送信や webhook URL への投稿など)。 通常、通知によって複数のアクションがトリガーされる可能性があります。


## <a name="alerts-on-azure-monitor-data"></a>Azure Monitor データのアラート
Azure Monitor から使用可能なデータのアラートには、メトリック アラート、ほぼリアルタイムのメトリック アラート、アクティビティ ログ アラートの 3 種類があります。

* **クラシック メトリック アラート** - このアラートは、指定したメトリックの値が、割り当てたしきい値を超えたときにトリガーされます。 このアラートで通知が生成されるのは、アラートが "アクティブ化済み" になったとき (しきい値を超え、アラートの条件を満たしたとき) と、"解決済み" になったとき (しきい値を再び超え、条件を満たさなくなったとき) です。 これらは古いメトリック アラートです。 新しいメトリック アラートについては、以下を参照してください。

* **ほぼリアルタイムのメトリック アラート** (新しいアラート エクスペリエンス) - これは、以前のメトリック アラートに比べて機能が強化された、より新しい世代のメトリック アラートです。 これらのアラートは、1 分の頻度で実行できます。また、複数の (現在は 2 つの) メトリックの監視もサポートされます。  このアラートで通知が生成されるのは、アラートが "アクティブ化" されたとき (各メトリックが同時にしきい値を上回り、かつアラートの条件が満たされたとき) と、アラートが "解決された" とき (少なくとも 1 つのメトリックがしきい値を下回り、条件を満たさなくなったとき) です。

* **クラシック アクティビティ ログ アラート** - 割り当てたフィルター条件と一致するアクティビティ ログ イベントが生成されたときにトリガーされるストリーミング ログ アラート。 すべての新規イベントには、アラート エンジンによってフィルター条件が適用されるだけなので、これらのアラートの状態は "アクティブ化済み" のみです。 これらのアラートを使用して、新しいサービス正常性インシデントが発生したとき、またはユーザーまたはアプリケーションがサブスクリプションで操作 (仮想マシンの削除など) を実行したときに通知を受け取ることができます。

Azure Monitor で使用できる診断ログ データの場合、データを Log Analytics にルーティングし、Log Analytics アラートを使用することをお勧めします。 次の図は、Azure Monitor のデータのソースと、そのデータからアラートを生成する方法をまとめたものです。

![アラートについて説明します。](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-classic-alert"></a>Azure Monitor クラシック アラートで通知を受け取る方法
これまで、各サービスの Azure アラートは、それぞれ独自の組み込み通知方法を使用していました。 これからは、Azure Monitor でアクション グループという再利用可能な通知グループを使用できます。 アクション グループには、通知の受信者セット (任意の数の電子メール アドレス、電話番号 (SMS の場合)、または webhook の URL) を指定します。アクション グループを参照するアラートがアクティブになると、すべての受信者がその通知を受け取ります。 そのため、多数のアラート オブジェクト全体で受信者のグループ (たとえば、オン コール エンジニア一覧など) を再利用できます。 現時点では、アクション グループを利用できるのはアクティビティ ログ アラートのみですが、他の Azure アラートの種類もアクション グループの使用に取り組んでいます。

アクション グループは、電子メール アドレスや SMS 番号に加え、webhook URL への投稿による通知をサポートしています。 そのため、たとえば自動化や修復に以下を使用できます。
    - Azure Automation Runbook
    - Azure 関数
    - Azure Logic App
    - サードパーティのサービス

ほぼリアルタイムのメトリック アラートとアクティビティ ログ アラートは、アクション グループを使用します。

[アラート (クラシック)] で使用可能な古いメトリック アラートは、アクション グループを使用しません。 個々のメトリック アラートでは、以下のように通知を構成することができます。
* メール通知をサービス管理者、共同管理者、または指定した追加のメール アドレスに送信する。
* webhook を呼び出す。これにより、追加のオートメーション アクションを実行できます。

## <a name="next-steps"></a>次の手順
アラート ルールとその構成方法については、以下をご覧ください。

* [メトリック](monitoring-overview-metrics.md)の詳細
* [Azure Portal からの従来のメトリック アラート](insights-alerts-portal.md)を構成する
* [従来のメトリック アラートの PowerShell](insights-alerts-powershell.md) を構成する
* [従来のメトリック アラートのコマンド ライン インターフェイス (CLI)](insights-alerts-command-line-interface.md) を構成する
* [従来のメトリック アラートの Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx) を構成する
* [アクティビティ ログ](monitoring-overview-activity-logs.md) の詳細
* [Azure Portal からのアクティビティ ログ アラート](monitoring-activity-log-alerts.md)の構成
* [Resource Manager からのアクティビティ ログ アラート](monitoring-create-activity-log-alerts-with-resource-manager-template.md)の構成
* [アクティビティ ログ アラート webhook スキーマ](monitoring-activity-log-alerts-webhook.md) の確認
* [新しいメトリック アラート](monitoring-near-real-time-metric-alerts.md)の詳細について学習します。
* [サービス通知](monitoring-service-notifications.md) の詳細
* [アクション グループ](monitoring-action-groups.md)の詳細については、こちらをご覧ください。
* [アラート](monitor-alerts-unified-usage.md)を構成する

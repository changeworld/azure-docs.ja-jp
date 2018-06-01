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
ms.date: 05/15/2018
ms.author: robb
ms.openlocfilehash: 039ab7226b4ea7e011e1c0cbb4cac528b5237483
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204007"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Microsoft Azure のクラシック アラートの概要

> [!NOTE]
> この記事では、古いクラシック メトリック アラートの作成方法について説明します。 Azure Monitor では、[新しいほぼリアルタイムのメトリック アラートとアラート エクスペリエンス](monitoring-overview-unified-alerts.md)がサポートされています。 
>

アラートを使用してデータに対する条件を構成し、その条件が最新の監視データと一致したときに通知を受け取ることができます。


## <a name="alerts-on-azure-monitor-data"></a>Azure Monitor データのアラート
メトリック アラートとアクティビティ ログ アラートの 2 つの種類のクラシック アラートが使用可能です。

* **クラシック メトリック アラート** - このアラートは、指定したメトリックの値が、割り当てたしきい値を超えたときにトリガーされます。 このアラートで通知が生成されるのは、アラートが "アクティブ化済み" になったとき (しきい値を超え、アラートの条件を満たしたとき) と、"解決済み" になったとき (しきい値を再び超え、条件を満たさなくなったとき) です。 新しいメトリック アラートについては、以下を参照してください。

* **クラシック アクティビティ ログ アラート** - 割り当てたフィルター条件と一致するアクティビティ ログ イベントが生成されたときにトリガーされるストリーミング ログ アラート。 すべての新規イベントには、アラート エンジンによってフィルター条件が適用されるだけなので、これらのアラートの状態は "アクティブ化済み" のみです。 これらのアラートを使用して、新しいサービス正常性インシデントが発生したとき、またはユーザーまたはアプリケーションがサブスクリプションで操作 (仮想マシンの削除など) を実行したときに通知を受け取ることができます。

Azure Monitor で使用できる診断ログ データの場合、データを Log Analytics (旧称 OMS) に転送し、Log Analytics クエリ アラートを使用することをお勧めします。 Log Analytics では、[新しいアラート メソッド](monitoring-overview-unified-alerts.md)を使用しています。 

次の図は、Azure Monitor のデータのソースと、そのデータからアラートを生成する方法をまとめたものです。

![アラートについて説明します。](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Azure Monitor アラート (クラシック) の分類
Azure では、クラシック アラートとその機能を説明するときに次の用語を使用します。
* **アラート** - 一致したときにアクティブになる条件 (1 つまたは複数の規則または条件) の定義。
* **アクティブ** - クラシック アラートによって定義されている条件と一致したときの状態。
* **解決済み** - クラシック アラートによって定義されている条件と以前は一致していたが、一致しなくなった状態。
* **通知** - クラシック アラートがアクティブになったことに基づいて実行されるアクション。
* **アクション** - 通知の受信側に送信される特定の呼び出し (たとえば、アドレスへの電子メール送信や webhook URL への投稿など)。 通常、通知によって複数のアクションがトリガーされる可能性があります。

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Azure Monitor クラシック アラートから通知を受け取る方法
これまで、各サービスの Azure アラートは、それぞれ独自の組み込み通知方法を使用していました。 

Azure Monitor では、新たに*アクション グループ*という再利用可能な通知グループを使用できるようになりました。 アクション グループは、通知の受信者セット を指定します。アクション グループを参照するアラートがアクティブになると、すべての受信者がその通知を受け取ります。 そのため、多数のアラート オブジェクト全体で受信者のグループ (たとえば、オン コール エンジニア一覧など) を再利用できます。 アクション グループは、電子メール アドレスや SMS 番号、その他の多数のアクションに加え、Webhook URL への投稿による通知をサポートしています。  詳細については、[アクション グループ](monitoring-action-groups.md)に関する記事をご覧ください。 

従来のアクティビティ ログ アラートでは、アクション グループを使用します。

ただし、従来のメトリック アラートでは、アクション グループを使用していません。 代わりに、次のアクションを構成できます。 
* メール通知をサービス管理者、共同管理者、または指定した追加のメール アドレスに送信する。
* webhook を呼び出す。これにより、追加のオートメーション アクションを実行できます。

たとえば、Webhook では、以下を使用して自動化や修復を可能にします。
    - Azure Automation Runbook
    - Azure 関数
    - Azure Logic App
    - サードパーティのサービス

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
* [アクション グループ](monitoring-action-groups.md)の詳細については、こちらをご覧ください。
* [新しいアラート](monitor-alerts-unified-usage.md)を構成する

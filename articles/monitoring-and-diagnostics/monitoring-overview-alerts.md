---
title: Microsoft Azure と Azure Monitor でのクラシック アラートの概要
description: アラートを使用すると、Azure リソースのメトリック、イベント、またはログを監視し、指定した条件が満たされたときに通知を受けることができます。
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: a0abcbdaa7e998413efb717be6e0addc5607ec5c
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114013"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Microsoft Azure のクラシック アラートの概要

> [!NOTE]
> この記事では、古いクラシック メトリック アラートの作成方法について説明します。 Azure Monitor では、[新しいほぼリアルタイムのメトリック アラートと新しいアラート エクスペリエンス](monitoring-overview-unified-alerts.md)がサポートされるようになりました。 
>

アラートを使用すると、データに対する条件を構成し、その条件が最新の監視データと一致したときに通知を受け取ることができます。


## <a name="alerts-on-azure-monitor-data"></a>Azure Monitor データのアラート
メトリック アラートとアクティビティ ログ アラートの 2 つの種類のクラシック アラートが使用可能です。

* **クラシック メトリック アラート**: このアラートは、指定したメトリックの値が、割り当てたしきい値を超えたときにトリガーされます。 アラートでは、"アクティブ化済み" になったとき (しきい値を超えて、アラートの条件に一致したとき) に通知が生成されます。 また、"解決済み" になったとき (もう一度しきい値を超えて、条件が一致しなくなったとき) も、アラートが生成されます。 

* **クラシック アクティビティ ログ アラート**: 割り当てたフィルター条件と一致するアクティビティ ログ イベントが生成されたときに、このストリーミング ログ アラートがトリガーされます。 すべての新規イベントには、アラート エンジンによってフィルター条件が適用されるだけなので、これらのアラートの状態は "アクティブ化済み" のみです。 これらのアラートでは、新しいサービス正常性インシデントが発生したとき、またはユーザーまたはアプリケーションがサブスクリプションで "仮想マシンの削除" などの操作を実行したときに通知を受け取ることができます。

Azure Monitor から利用できる診断ログ データを受信するには、データを Log Analytics (以前の Operations Management Suite) にルーティングして、Log Analytics クエリ アラートを使用します。 現在、Log Analytics では、[新しいアラート メソッド](monitoring-overview-unified-alerts.md)を使用しています。 

次の図では、Azure Monitor のデータのソースをまとめ、このデータのアラートをオフにする方法を提案しています。

![アラートについて説明します。](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Azure Monitor アラート (クラシック) の分類
Azure では、クラシック アラートとその機能を説明するときに次の用語を使用します。
* **アラート** - 一致したときにアクティブになる条件 (1 つまたは複数のルールまたは条件) の定義。
* **アクティブ** - クラシック アラートによって定義されている条件と一致したときに発生する状態。
* **解決済み** - クラシック アラートによって定義されている条件と以前は一致していたが、一致しなくなったときに発生する状態。
* **通知** - クラシック アラートがアクティブになったことに基づいて実行されるアクション。
* **アクション**: 通知の受信者に送信された特定の呼び出し (たとえば、電子メールまたは Webhook URL への投稿)。 通常、通知によって複数のアクションがトリガーされる可能性があります。

## <a name="how-do-i-receive-notifications-from-an-azure-monitor-classic-alert"></a>Azure Monitor クラシック アラートから通知を受け取る方法
これまで、各サービスの Azure アラートは、それぞれ独自の組み込み通知方法を使用していました。 

Azure Monitor では、*アクション グループ*という再利用可能な通知グループを使用できるようになりました。 アクション グループで通知に対する受信者のセットを指定します。 アクション グループを参照するアラートがアクティブになると、すべての受信者が通知を受信します。 この機能によって、多数のアラート オブジェクト全体で受信者のグループ (たとえば、オンコール エンジニア一覧など) を再利用できます。 アクション グループでは、さまざまなメソッドを使用した通知をサポートします。 これらのメソッドには、Webhook URL への投稿、電子メールの送信、SMS メッセージやその他の多くのアクションを含めることができます。 詳細については、「[Azure Portal でのアクション グループの作成および管理](monitoring-action-groups.md)」を参照してください。 

従来のクラシック アクティビティ ログ アラートでは、アクション グループを使用します。

ただし、従来のメトリック アラートでは、アクション グループを使用しません。 代わりに、次のアクションを構成できます。 
* メール通知をサービス管理者、共同管理者、または指定した追加のメール アドレスに送信する。
* webhook を呼び出す。これにより、追加のオートメーション アクションを実行できます。

たとえば、Webhook では、以下のサービスを使用して自動化や修復を可能にします。
- Azure Automation Runbook
- Azure Functions
- Azure Logic App
- サードパーティのサービス

## <a name="next-steps"></a>次の手順
次のドキュメントを使用して、アラート ルールに関する情報と構成する方法を取得します。

* [メトリック](monitoring-overview-metrics.md)の詳細
* [Azure portal を使用してクラシック メトリック アラート](insights-alerts-portal.md)を構成する
* [PowerShell を使用してクラシック メトリック アラート](insights-alerts-powershell.md)を構成する
* [Azure CLI を使用してクラシック メトリック アラート](insights-alerts-command-line-interface.md)を構成する
* [Azure Monitor REST API を使用してクラシック メトリック アラート](https://msdn.microsoft.com/library/azure/dn931945.aspx)を構成する
* [アクティビティ ログ](monitoring-overview-activity-logs.md)の詳細
* [Azure portal を使用してアクティビティ ログ アラート](monitoring-activity-log-alerts.md)を構成する
* [Resource Manager を使用してアクティビティ ログ アラート](monitoring-create-activity-log-alerts-with-resource-manager-template.md)を構成する
* [アクティビティ ログ アラート webhook スキーマ](monitoring-activity-log-alerts-webhook.md) の確認
* [アクション グループ](monitoring-action-groups.md)の詳細を確認する
* [新しいアラート](monitor-alerts-unified-usage.md)を構成する

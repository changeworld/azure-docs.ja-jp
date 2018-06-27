---
title: Azure portal を使用して Azure サービスのアラートを作成する | Microsoft Docs
description: 指定した条件が満たされたときに、電子メール、通知、または Websites URL (webhook) の呼び出し、またはオートメーションをトリガーします。
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/23/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: d0e5512eb3f963898ded6d7155f8b75cfb6ef911
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287431"
---
# <a name="use-the-azure-portal-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>Azure portal を使用して Azure Monitor で Azure サービスのアラートを作成する 

> [!div class="op_single_selector"]
> * [ポータル](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>

> [!NOTE]
> この記事では、古いクラシック メトリック アラートの作成方法について説明します。 Azure Monitor では、[新しいメトリック アラート](monitoring-near-real-time-metric-alerts.md)がサポートされるようになりました。 


この記事では、Azure portal を使用してクラシック Azure メトリック アラートを設定する方法について説明します。 

Azure サービスのメトリックに基づいてアラートを受信したり、Azure で発生したイベントのアラートを受信したりすることができます。

* **メトリック値**: アラートは、指定したメトリックの値が、割り当てたしきい値をいずれかの方向で超えたときにトリガーされます。 つまり、条件を最初に満たしたときと、その条件を満たさなくなったときの両方でトリガーされます。    

* **アクティビティ ログ イベント**: アラートは*すべて*のイベントに対して、または特定のイベントが発生したときにトリガーされます。 [アクティビティ ログのアラート](monitoring-activity-log-alerts.md)について詳しく学習します。

クラシック メトリック アラートがトリガーされたときに実行されるように構成できる処理は次のとおりです。

* サービスの管理者/共同管理者に電子メール通知を送信する。
* 指定した追加の電子メール アドレスに電子メールを送信する。
* Webhook を呼び出す。
* Azure Runbook の実行を開始する (Azure portal からのみ)。

次の場所から、クラシック メトリック アラート ルールを構成したり、その情報を取得したりすることができます。 

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure CLI](insights-alerts-command-line-interface.md)
* [Azure 監視 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Azure ポータルでメトリックにアラート ルールを作成する
1. [ポータル](https://portal.azure.com/)で、監視するリソースを見つけて選択します。

2. **[監視]** セクションで、**[アラート (クラシック)]** を選択します。 テキストとアイコンは、リソースごとに多少異なる場合があります。 **[アラート (クラシック)]** が見つからない場合は、**[アラート]** または **[アラート ルール]** 内で見つかることがあります。

    ![監視](./media/insights-alerts-portal/AlertRulesButton.png)

3. **[メトリック アラートの追加 (クラシック)]** コマンドを選択して、フィールドに入力します。

    ![[アラートの追加]](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. **[名前]** にアラート ルール名を入力します。 次に **[説明]** を選択します。この説明は通知電子メールにも表示されます。

5. 監視する **[メトリック]** を選択します。 次に、メトリックの **[条件]** と **[しきい値]** を選択します。 また、 **[期間]** では、どのくらいの期間メトリック ルールが満たされた後、アラートがトリガーされるかを選択します。 たとえば、期間として "直近 5 分" を使用すると、アラートは 80% を超える CPU を見つけて、その CPU が 5 分間継続して 80% を超えた時点でトリガーされます。 最初のトリガーが発生したら、次のアラートは、CPU が 5 分間継続して 80% を下回ったときにトリガーされます。 CPU のメトリック測定は、1 分ごとに発生します。

6. アラートが発生したときに管理者と共同管理者に電子メール通知が送信されるようにするには、**[メールの所有者]** を選択します。

7. アラートが発生したときに、他の電子メール アドレスにも通知を送信する場合は、**[追加する管理者の電子メール]** フィールドにそのアドレスを入力します。 複数の電子メール アドレスは、*email@contoso.com; email2@contoso.com* のようにセミコロンで区切ります。

8. **[webhook]** フィールドに、アラートが発生したときに呼び出す webhook の有効な URI を入力します。

9. Azure Automation を使用する場合は、アラートが発生したときに実行する Runbook を選択できます。

10. **[OK]** を選択してアラートを作成します。   

数分後にアラートがアクティブになり、前述のようにトリガーされます。

## <a name="manage-your-alerts"></a>アラートの管理
アラートを作成したら、それを選択して次のタスクのいずれかを実行できます。

* メトリックのしきい値と、前日の実際の値を示すグラフを表示する。
* 編集または削除する。
* そのアラートの受信を一時的に停止または再開する必要がある場合に、そのアラートを**無効**または**有効**にする。

## <a name="next-steps"></a>次の手順
* 収集および監視できる情報の種類など、[Azure での監視の概要](monitoring-overview.md)情報を入手します。
* [新しいメトリック アラート](monitoring-near-real-time-metric-alerts.md)に関する詳細情報を確認します。
* [アラートでの webhook の構成](insights-webhooks-alerts.md)に関する詳細情報を確認します。
* [アクティビティ ログ イベントに対するアラートの構成](monitoring-activity-log-alerts.md)に関する詳細情報を確認します。
* [Azure Automation Runbooks](../automation/automation-starting-a-runbook.md) の詳細情報を確認します。
* [診断ログの概要](monitoring-overview-of-diagnostic-logs.md)情報を入手し、サービスに関する詳細な頻度の高いメトリックを収集します。
* [メトリック収集の概要](insights-how-to-customize-monitoring.md)情報を入手して、サービスの可用性と応答性を確認します。

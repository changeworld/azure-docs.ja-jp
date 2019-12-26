---
title: アラートの構成 - Hyperscale (Citus) - Azure Database for PostgreSQL
description: この記事では、Azure Database for PostgreSQL の Hyperscale (Citus) でのメトリック アラートを構成してアクセスする方法について説明します。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 68a830f344023967f07ab809d67833f99e4e2958
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977609"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Azure portal を使用して Azure Database for PostgreSQL でのメトリックに対するアラートを設定する - Hyperscale (Citus)

この記事では、Azure Portal を使用して Azure Database for PostgreSQL のアラートを設定する方法について説明します。 お使いの Azure のサービスの監視メトリックに基づいてアラートを受け取ることができます。

指定したメトリックの値がしきい値を超えた場合に、アラートがトリガーされるように設定します。 条件が最初に一致したときにアラートがトリガーされ、その後も引き続きトリガーが行われます。

アラートがトリガーされたときに実行されるように構成できるアクションは次のとおりです。
* サービスの管理者および共同管理者に電子メール通知を送信する。
* 指定した追加の電子メール アドレスに電子メールを送信する。
* Webhook を呼び出す。

アラート ルールを構成したり、その情報を取得したりするには、以下を使用します。
* [Azure Portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure 監視 REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Azure Portal でメトリックのアラート ルールを作成する
1. [Azure Portal](https://portal.azure.com/) で、監視する Azure Database for PostgreSQL サーバーを選択します。

2. 次のように、サイドバーの **[監視]** セクションで、 **[アラート]** を選択します。

   ![アラート ルールを選択する](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. **[新しいアラート ルール]** (+ アイコン) を選択します。

4. 以下のように、 **[ルールの作成]** ページが開きます。 必要な情報を入力します。

   ![メトリック アラート フォームを追加する](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. **[条件]** セクション内で、 **[追加]** を選択します。

6. アラート通知のシグナルの一覧からメトリックを選択します。 この例では、[ストレージの割合] を選択します。
   
   ![メトリックを選択する](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. 次のアラート ロジックを構成します。

    * **演算子** (例: "より大きい")
    * **しきい値** (例: 85 パーセント)
    * **[時間の集計]** 、アラートがトリガーされる前にメトリック ルールが確実に満たされるまでの期間 (例: "直近 30 分")
    * **[評価の頻度]** (例: "1 分")
   
   完了したら、 **[完了]** を選択します。

   ![メトリックを選択する](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. **[アクション グループ]** セクション内で **[新規作成]** を選択して、アラートの通知を受信する新しいグループを作成します。

9. [アクション グループの追加] フォームに、名前、短い名前、サブスクリプション、リソース グループを入力します。

    ![アクション グループ](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. アクションの種類で、 **[電子メール/SMS/プッシュ/音声]** を構成します。
    
    [電子メールの Azure Resource Manager のロール] を選択して、サブスクリプション所有者、共同作成者、および閲覧者に通知を送信します。
   
    完了したら、 **[OK]** を選択します。

    ![アクション グループ](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. [アラート ルール名]、[説明]、[重大度] を指定します。

    ![アクション グループ](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. **[アラート ルールの作成]** を選択して、アラートを作成します。

    数分後にアラートがアクティブになり、前述のようにトリガーされます。

## <a name="manage-your-alerts"></a>アラートの管理

アラートを作成したら、それを選択して次のアクションを実行できます。

* このアラートに関連するメトリックのしきい値と、前日の実際の値を示すグラフを表示する。
* アラート ルールを**編集**または**削除**する。
* アラートを**無効**にしてアラートを一時的に停止する、または**有効**にして通知の受け取りを再開する。

## <a name="next-steps"></a>次の手順
* [アラートでの webhook の構成](../azure-monitor/platform/alerts-webhooks.md)に関する詳細情報を確認します。
* [メトリック収集の概要](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) 情報を入手して、サービスの可用性と応答性を確認します。

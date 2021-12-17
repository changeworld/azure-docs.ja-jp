---
title: チュートリアル - Azure 仮想マシンがダウンしたときのアラート
description: 仮想マシンの利用不可状態をプロアクティブに通知するアラート ルールを Azure Monitor で作成する
ms.service: azure-monitor
ms.topic: article
ms.custom: subject-monitoring
ms.date: 11/04/2021
ms.openlocfilehash: af60844e6aced51989cbee07a787deba01a379f9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350139"
---
# <a name="tutorial-create-alert-when-azure-virtual-machine-is-unavailable"></a>チュートリアル: Azure 仮想マシンが利用不可状態になったときのアラートを作成する
仮想マシンに関して最も一般的なアラート状態は、仮想マシンが実行されているかどうかです。 Azure Monitor で VM insights による仮想マシン監視を有効にすると、Azure Monitor に 1 分間隔でハートビートが送信されます。 ハートビートが検出されなくなった場合にアラートを送信するログ クエリ アラート ルールを作成できます。 この方法では、仮想マシンが実行されていない場合だけでなく、応答しない場合にもアラートが送信されます。


このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * Azure Monitor の VM insights によって収集された仮想マシンのログ データを確認する。
> * 仮想マシンが利用不可状態になった場合にプロアクティブに通知するアラート ルールをログ データから作成する。

## <a name="prerequisites"></a>必須コンポーネント
このチュートリアルを完了するには、以下が必要です。 

- 監視する Azure 仮想マシン。
- 仮想マシンに対して VM insights を使用した監視が有効になっていること。 「[チュートリアル: Azure 仮想マシンに対する監視を有効にする](tutorial-monitor-vm-enable.md)」を参照してください。



## <a name="create-a-heartbeat-query"></a>ハートビート クエリを作成する
ログ クエリ アラート ルールは、さまざまな方法で作成できます。 このチュートリアルでは、 **[Map]\(マップ\)** ビューの **[Logs Events]\(ログ イベント\)** タブを出発点とします。 ここには、仮想マシンに関して収集されたログ データの概要が表示されます。 

:::image type="content" source="media/tutorial-monitor-vm/map-logs.png" lightbox="media/tutorial-monitor-vm/map-logs.png" alt-text="[Map]\(マップ\) ビューと [log events]\(ログ イベント\) タブ":::

**[ハートビート]** をクリックします。 仮想マシンから収集されたログ データを分析するための主要なツールである Log Analytics が開き、ハートビート イベントを照会する簡単なクエリが表示されます。 **[TimeGenerated]** をクリックすると、その列を基準に並べ替えが実行され、ハートビートが 1 分に 1 回作成されていることがわかります。

:::image type="content" source="media/tutorial-monitor-vm/log-query-heartbeat.png" lightbox="media/tutorial-monitor-vm/log-query-heartbeat.png" alt-text="ハートビート イベントを含むログ クエリ アラート。":::


アラートのために取得すべきレコードは、過去 5 分間のハートビート レコードのみです。 レコードがまったく返されない場合に、仮想マシンはダウンしていると考えられます。

過去 5 分間に作成されたレコードのみとなるように結果をフィルタリングするための行をクエリに追加します。 ここでは、現在の時刻から特定の期間を減算する [ago 関数](/azure/data-explorer/kusto/query/agofunction)を使用しています。

```
Heartbeat
| where Computer == 'computer-name'
| where TimeGenerated > ago(5m)
```

**[実行]** をクリックしてこのクエリの結果を確認すると、過去 5 分間のハートビートだけが含まれていることがわかります。

:::image type="content" source="media/tutorial-monitor-vm/log-query-heartbeat-time-filter.png" lightbox="media/tutorial-monitor-vm/log-query-heartbeat-time-filter.png" alt-text="時間フィルターを使用したハートビート イベントによるログ クエリ アラート。":::

## <a name="create-alert-rule"></a>アラート ルールの作成
ログ クエリが完成したら、クエリからレコードが返されなかった場合にアラートを送信するアラート ルールを作成できます。 過去 5 分間に返されたハートビート レコードが 0 件である場合、その間、マシンは無応答状態だったと考えることができます。 

現在のクエリからルールを作成するには、 **[新しいアラート ルール]** をクリックします。

:::image type="content" source="media/tutorial-monitor-vm/new-alert-rule.png" lightbox="media/tutorial-monitor-vm/new-alert-rule.png" alt-text="新しいアラート ルール。":::


アラート ルールには、**ログ クエリ** が既に入力されています。 やりたいことは、クエリから返されたテーブルの行数をカウントすることなので、 **[測定]** も既に適切な状態になっています。 行数が 0 である場合に、アラートを作成することになります。

:::image type="content" source="media/tutorial-monitor-vm/alert-rule-01.png" lightbox="media/tutorial-monitor-vm/alert-rule-01.png" alt-text="アラート ルールの条件 1。":::

下へスクロールして **[アラート ロジック]** に移動し、 **[演算子]** を **[等しい]** に変更して、 **[しきい値]** に「**0**」を指定します。 これはレコードが返されなかったとき、つまりクエリから返されたレコード数がゼロのときにアラートを作成する、という意味になります。

:::image type="content" source="media/tutorial-monitor-vm/alert-rule-02.png" lightbox="media/tutorial-monitor-vm/alert-rule-02.png" alt-text="アラート ルールの条件 2。":::

## <a name="configure-action-group"></a>アクション グループを構成する
アラート ルールには、 **[アクション]** ページから 1 つまたは複数の[アクション グループ](../alerts/action-groups.md)を追加できます。 アクション グループは、携帯ショートメール (SMS) メッセージやメールの送信など、アラートが発生したときに実行される一連のアクションを定義するものです。

既にアクション グループがある場合は、 **[アクション グループの追加]** をクリックして既存のグループをアラート ルールに追加します。

:::image type="content" source="media/tutorial-monitor-vm/alert-rule-actions.png" lightbox="media/tutorial-monitor-vm/alert-rule-actions.png" alt-text="アラート ルールの [アクション グループの追加]。":::

選択すべきアクション グループがまだサブスクリプションにない場合は、 **[アクション グループの作成]** をクリックして新たに作成します。 アクション グループの **サブスクリプション** と **リソース グループ** を選択し、ポータルに表示される **アクション グループ名** と、メールや SMS 通知に記載される **表示名** を指定します。

:::image type="content" source="media/tutorial-monitor-vm/action-group-basics.png" lightbox="./media/tutorial-monitor-vm/action-group-basics.png" alt-text="アクション グループの [基本]":::

**[通知]** を選択し、アラートが発生したときに適切な人員に通知する方法を追加します。複数の方法を追加することもできます。

:::image type="content" source="media/tutorial-monitor-vm/action-group-notifications.png" lightbox="./media/tutorial-monitor-vm/action-group-notifications.png" alt-text="アクション グループの [通知]":::



## <a name="configure-details"></a>詳細を構成する
**[詳細]** ページでは、アラート ルールに関してさまざまな設定を構成できます。

- アラート ルールの保存先となる **サブスクリプション** と **リソース グループ**。 監視対象のリソースと同じリソース グループである必要はありません。
- アラートの **重大度**。 相対的な重要度が近いアラートをグループ化できます。 応答しない仮想マシンには、**エラー** という重大度が適しています。
- **[Enable alert upon creation]\(作成時にアラートを有効化\)** チェック ボックスはオンのままにします。
- **[アラートを自動的に解決する]** チェック ボックスはオンのままにします。 これにより、仮想マシンがオンラインに復帰してハートビート レコードが再び検出されるとアラートが自動的に解決されます。

:::image type="content" source="media/tutorial-monitor-vm/alert-rule-details.png" lightbox="media/tutorial-monitor-vm/alert-rule-details.png" alt-text="アラート ルールの詳細。":::

**[確認と作成]** をクリックすると、アラート ルールが作成されます。

## <a name="view-the-alert"></a>アラートを表示する
アラート ルールをテストするために、仮想マシンを停止します。 アクション グループで通知を構成した場合、数分以内に通知が届きます。 アラートは、仮想マシンの **[アラート]** ページに表示される概要でも確認できます。

:::image type="content" source="media/tutorial-monitor-vm/alerts-summary.png" lightbox="media/tutorial-monitor-vm/alerts-summary.png" alt-text="アラートの概要":::

**[重大度]** をクリックすると、それらのアラートが一覧表示されます。 アラートそのものをクリックすると、その詳細が表示されます。

:::image type="content" source="media/tutorial-monitor-vm/alerts-summary.png" lightbox="media/tutorial-monitor-vm/alerts-summary.png" alt-text="アラート一覧":::

## <a name="next-steps"></a>次の手順
ログ データからアラートを作成する方法がわかったら、データ収集ルールを使用して、仮想マシンからさらにログとパフォーマンス データを収集しましょう。

> [!div class="nextstepaction"]
> [Azure 仮想マシンからゲストのログとメトリックを収集する](tutorial-monitor-vm-guest.md)


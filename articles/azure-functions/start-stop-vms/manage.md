---
title: Start/Stop VMs v2 (プレビュー) の管理
description: この記事では、Start/Stop VMs v2 (プレビュー) 機能によって管理されている Azure VM の状態を監視し、その他の管理タスクを実行する方法について説明します。
services: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.topic: conceptual
ms.openlocfilehash: 1bb5a7bf62bd16274a168ecb8b3147c07afc8860
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130175866"
---
# <a name="how-to-manage-startstop-vms-v2-preview"></a>Start/Stop VMs v2 (プレビュー) を管理する方法

## <a name="azure-dashboard"></a>Azure ダッシュボード

Start/Stop VMs v2 (プレビュー) には、管理スコープおよび VM に対する最近の操作を把握するために役立つ[ダッシュボード](../../azure-monitor/best-practices-analysis.md#azure-dashboards)が含まれています。 これは、Azure VM で実行される各操作の状態をすばやく簡単に確認する方法です。 各タイルの視覚化はログ クエリに基づいていて、クエリを確認するには、タイルの右隅にある **[ログ ブレードで開く]** オプションを選択します。 これにより Azure portal の [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md#starting-log-analytics) ツールが開きます。ここから、クエリを評価し、カスタム [ログ アラート](../../azure-monitor/alerts/alerts-log.md)やカスタム [ブック](../../azure-monitor/visualize/workbooks-overview.md)などのニーズに合わせて変更することができます。

特定の視覚エフェクトの **[更新]** アイコンをクリックするか、ダッシュボード全体を更新することにより、オンデマンドで手動更新オプションを使って、ダッシュボード内の各タイルに表示されるログ データが 1 時間ごとに更新されます。

ログベースのダッシュボードの使用の詳細については、この[チュートリアル](../../azure-monitor/visualize/tutorial-logs-dashboards.md)を参照してください。

## <a name="configure-email-notifications"></a>電子メール通知の構成

Start/Stop VMs v2 (プレビュー) のデプロイ後に電子メール通知を変更する場合、デプロイ時に作成されたアクション グループを変更できます。

1. Azure portal で、 **[監視]** 、 **[通知]** の順に移動します。 **[アクションの管理]** を選択します。

1. **[アクションの管理]** ページで、**StartStopV2_VM_Notication** というアクション グループを選択します。

    :::image type="content" source="media/manage/alerts-action-groups.png" alt-text="[アクション グループ] ページのスクリーンショット。":::

1. **StartStopV2_VM_Notification** のページで、メール/SMS/プッシュ/音声の通知オプションを変更できます。 このアクション グループに対して、他のアクションを追加するか既存の構成を更新してから、 **[OK]** をクリックして変更を保存します。

    :::image type="content" source="media/manage/email-notification-type-example.png" alt-text="更新されたメール アドレスの例を示す [電子メール/SMS/プッシュ/音声] ページのスクリーンショット。":::

    アクション グループの詳細については、[アクション グループ](../../azure-monitor/alerts/action-groups.md)に関する記事をご覧ください。

この機能によって仮想マシンがシャットダウンされたときに送信されるメールの例を次のスクリーンショットに示します。

:::image type="content" source="media/manage/email-notification-example.png" alt-text="機能によって仮想マシンがシャットダウンされたときに送信されるメールの例のスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

VM の管理中に発生した問題への対応については、[Start/Stop VMs v2 (プレビュー) の問題のトラブルシューティング](troubleshoot.md)に関する記事を参照してください。

---
title: Azure Activity ログと Azure Service Health で、アプリのライフサイクル イベントを監視する
description: Azure Activity ログと Azure Service Health で、アプリのライフサイクル イベントを監視してアラートを設定します。
author: karlerickson
ms.author: shiqiu
ms.service: spring-cloud
ms.topic: how-to
ms.date: 08/19/2021
ms.custom: devx-track-java
ms.openlocfilehash: 594c8f48a51b386c687feda5d46a2423dd4e973b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131064033"
---
# <a name="monitor-app-lifecycle-events-using-azure-activity-log-and-azure-service-health"></a>Azure Activity ログと Azure Service Health で、アプリのライフサイクル イベントを監視する

この記事では、Azure Activity ログと Azure Service Health でアプリのライフサイクル イベントを監視し、アラートを設定する方法を説明します。

Azure Spring Cloud では、アプリケーションの状態と正常性を監視する組み込みツールを提供しています。 アプリのライフサイクル イベントは、アプリケーションに対する変更を把握し、必要な対応を行うのに役立ちます。 

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- Azure Spring Cloud サービスのインスタンスをデプロイし、少なくとも 1 つのアプリケーションをサービスのインスタンスに作成済みである。 詳細については、「 [クイックスタート: Azure Spring Cloud での最初の Spring Boot アプリのデプロイ](quickstart.md)」を参照してください。 

## <a name="monitor-app-lifecycle-events-triggered-by-users-in-azure-activity-logs"></a>ユーザーのトリガーにより発生するアプリのライフサイクル イベントを Azure Activity ログで監視する

[Azure Activity ログ](../azure-monitor/essentials/activity-log.md)には、サブスクリプションのリソース上で実行する操作によって発生するリソース イベントが記録されます。 次に示すアプリケーションのライフサイクル イベントの詳細 (起動、終了、再起動) が、Azure Activity ログに記録されます。

- 操作が発生した時間
- 操作の状態
- アプリ起動時に作成するインスタンス
- アプリ終了時に削除するインスタンス
- アプリ再起動時に削除、作成するインスタンス

たとえば、Azure portal の **アクティビティ ログ** の詳細ページで、アプリを再起動するときに操作を受けるインスタンスが分かります。

:::image type="content" source="media/monitor-app-lifecycle-events/activity-log-restart-detail.png" lightbox="media/monitor-app-lifecycle-events/activity-log-restart-detail.png" alt-text="Azure portal のアクティビティ ログの再起動の詳細のスクリーンショット":::

## <a name="monitor-app-lifecycle-events-in-azure-service-health"></a>Azure Service Health でアプリのライフサイクル イベントを監視する

[Azure Resource Health](../service-health/resource-health-overview.md) は、サービスの可用性に影響し得る問題を分析し、それに関するサポートを受けるのに役立ちます。 こうした問題には、サービスのインシデント、計画メンテナンスの期間、地域的な障害などがあります。 アプリケーションの再起動イベントは Azure Service Health に追加されます。 そこには、予期しないインシデント (計画外のアプリのクラッシュなど) とスケジュールしたアクション (計画メンテナンスなど) の両方が含まれます。

### <a name="monitor-unplanned-app-lifecycle-events"></a>計画外のアプリのライフサイクル イベントを監視する

計画外のイベントが原因でアプリが再起動されたときは、Azure Spring Cloud のインスタンスによって、Azure portal の **[Resource health]\(リソースの正常性\)** セクションに **degraded** (悪化) という状態が表示されます。 degraded は “引き続き使用はできるが、リソースによってパフォーマンスの潜在的な低下が見つかった” ことを意味します。 計画外のイベントの例には、アプリのクラッシュ、正常性チェックのエラー、システムの障害などがあります。

:::image type="content" source="media/monitor-app-lifecycle-events/resource-health-detail.png" alt-text="リソースの正常性ペインのスクリーンショット":::

最新の状態、根本原因、影響を受けたインスタンスを正常性の履歴ページで確認できます。

:::image type="content" source="media/monitor-app-lifecycle-events/unplanned-app-lifecycle-event-details.png" lightbox="media/monitor-app-lifecycle-events/unplanned-app-lifecycle-event-details.png" alt-text="計画外のアプリのライフサイクル イベントのログの例のスクリーンショット":::


### <a name="monitor-planned-app-lifecycle-events"></a>アプリのライフサイクル イベントの計画を監視する

プラットフォームのメンテナンス時にアプリが再起動する場合があります。 Azure Service Health の **[Planned mainenance]\(計画メンテナンス\)** ページで事前にメンテナンス通知を受け取ることができます。

:::image type="content" source="media/monitor-app-lifecycle-events/planned-maintenance-notification.png" lightbox="media/monitor-app-lifecycle-events/planned-maintenance-notification.png" alt-text="計画メンテナンスの通知の例のスクリーンショット":::

プラットフォームのメンテナンス実行時には、Azure Spring Cloud のインスタンスにより、**degraded** (悪化) の状態も表示されます。 プラットフォームのメンテナンス時に再起動が必要な場合は、Azure Spring Cloud によって、アプリケーションの差分更新を行うローリング アップデートを実行します。 ローリング アップデートでは、ダウンタイムなしでワークロードを更新します。 正常性の履歴ページで最新の状態を確認できます。

:::image type="content" source="media/monitor-app-lifecycle-events/planned-maintenance-in-progress.png" lightbox="media/monitor-app-lifecycle-events/planned-maintenance-in-progress.png" alt-text="実行中の計画メンテナンスのログの例のスクリーンショット":::

>[!NOTE]
> 現時点では、Azure Spring Cloud は、2-4 か月ごとに基になる Kubernetes バージョンをアップグレードする定期的なメンテナンスを1つ実行します。 詳細なメンテナンスタイムラインについては、[Azure Service Health] ページで通知を確認してください。

## <a name="set-up-alerts"></a>アラートを設定する

アプリのライフサイクル イベントに対するアラートを設定できます。 サービスの正常性通知も Azure のアクティビティ ログに保存します。 アクティビティ ログには大量の情報を保存するので、サービスの正常性通知のアラートを閲覧、設定しやくするために、専用のユーザー インターフェイスを用意しています。

アラート設定の全体的な手順は次のとおりです。 

1. トリガーによるアラート発生時に実行するアクションを設定するアクション グループをセット アップします。 アクションの種類には、例として、音声通話発信、SMS 送信、メール送信や、トリガーを使用したさまざまな自動アクションがあります。 ユーザーの要件に応じて、さまざまなアラートで同じアクション グループを使用することも、異なるアクション グループを使用することもあります。
2. アラート ルールを設定します。 アラートでは、アクション グループを使用して、特定のアプリのライフサイクル イベントに対するアラートがトリガーにより発生したことを、ユーザーに通知します。

### <a name="set-up-alerts-on-activity-log"></a>Activity log (アクティビティ ログ) でアラートを設定する

次のステップに従って、Azure portal で、アクティビティ ログ アラート ルールを作成します。

1. **[Activity log]\(アクティビティ ログ\)** に移動し、アクティビティ ログの詳細ページを開き、 **[New alert rule]\(新しいアラート ルール\)** をクリックします。

   :::image type="content" source="media/monitor-app-lifecycle-events/activity-log-alert.png" lightbox="media/monitor-app-lifecycle-events/activity-log-alert.png" alt-text="アクティビティ ログ アラートのスクリーンショット":::

2. アラートの **[Scope]\(範囲\)** を選択します。

3. アラートの **[Condition]\(条件\)** を指定します。

   :::image type="content" source="media/monitor-app-lifecycle-events/activity-log-alert-condition.png" lightbox="media/monitor-app-lifecycle-events/activity-log-alert-condition.png" alt-text="アクティビティ ログ アラートの条件のスクリーンショット":::

4. **[Actions]\(アクション\)** をクリックし、 **[Alert rule details]\(アラート ルールの詳細\)** を追加します。

5. **[アラート ルールの作成]** を選択します。

### <a name="set-up-alerts-to-monitor-app-lifecycle-events-in-azure-service-health"></a>Azure Service Health で、アプリのライフサイクル イベントを監視するアラートを設定する

次のステップに従って、Azure portal で、サービスの正常性通知のアラート ルールを作成します。

1. **[Service Health]\(サービスの正常性\)** の **[Resource health]\(リソースの正常性\)** に移動し、 **[Add resource health alert]\(リソースの正常性のアラートを追加\)** をクリックします。

   :::image type="content" source="media/monitor-app-lifecycle-events/add-resource-health-alert.png" alt-text="リソースの正常性ペインのスクリーンショット。[Add resource health alert]\(リソースの正常性のアラートを追加\) ボタンが表示されている。":::

2. アラート対象の **リソース** を選択します。

   :::image type="content" source="media/monitor-app-lifecycle-events/resource-health-alert-target.png" alt-text="リソースの正常性のアラート ターゲットのスクリーンショット":::

3. **[Alert condition]\(アラートの条件\)** を指定します。

   :::image type="content" source="media/monitor-app-lifecycle-events/resource-health-alert-condition.png" alt-text="リソースの正常性のアラートの条件のスクリーンショット":::

4. **[Actions]\(アクション\)** をクリックして **[Alert rule details]\(アラート ルールの詳細\)** を追加します。

5. **[アラート ルールの作成]** を選択します。

### <a name="set-up-alerts-to-monitor-the-planned-maintenance-notification"></a>計画メンテナンスの通知を監視するアラートを設定する

次のステップに従って、Azure portal で、計画メンテナンスの通知のアラート ルールを作成します。

1. **[Service Health]\(サービスの正常性\)** の **[Health alerts]\(正常性のアラート\)** に移動し、 **[Add service health alert]\(サービスの正常性のアラートを追加\)** をクリックします。

   :::image type="content" source="media/monitor-app-lifecycle-events/add-service-health-alert.png" alt-text="正常性のアラート ペインのスクリーンショット。[Add service health alert]\(サービスの正常性のアラートを追加\) が強調表示されている。":::

2. **[Subscription]\(サブスクリプション\)** 、 **[Service(s)]\(サービス\)** 、 **[Region(s)]\(リージョン\)** 、 **[Event type]\(イベントの種類\)** 、 **[Actions]\(アクション\)** 、 **[Alert rule details]\(アラート ルールの詳細\)** に値を入力します。

   :::image type="content" source="media/monitor-app-lifecycle-events/add-service-health-alert-details.png" lightbox="media/monitor-app-lifecycle-events/add-service-health-alert-details.png" alt-text="[Service Health]\(サービスの正常性\) の [Create alert rule]\(アラート ルールの作成\) ペインのスクリーンショット":::

3. **[アラート ルールの作成]** を選択します。

## <a name="next-steps"></a>次のステップ

[Azure Spring Cloud での問題の自己診断と解決](how-to-self-diagnose-solve.md)

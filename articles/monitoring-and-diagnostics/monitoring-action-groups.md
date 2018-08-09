---
title: Azure Portal でのアクション グループの作成および管理
description: Azure Portal でアクション グループを作成および管理する方法について説明します。
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/1/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 091a097fc9fafd5bdc6a2521f4fa2a1b6b77ba4c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422555"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Azure Portal でのアクション グループの作成および管理
## <a name="overview"></a>概要 ##
アクション グループは、ユーザーによって定義された通知設定のコレクションです。 Azure Monitor と Service Health のアラートは、アラートがトリガーされたときに特定のアクション グループを使用するように構成されます。 ユーザーの要件に応じて、さまざまなアラートで同じアクション グループを使用することも、異なるアクション グループを使用することもあります。

この記事では、Azure Portal でアクション グループを作成および管理する方法について説明します。

各アクションは次のプロパティで構成されます。

* **名前**: アクション グループ内の一意識別子。  
* **アクションの種類**: 音声通話または SMS の送信、電子メールの送信、webhook の呼び出し、ITSM ツールへのデータの送信、ロジック アプリの呼び出し、Azure アプリへのプッシュ通知、または Automation Runbook の実行。
* **詳細**: 対応する電話番号、メール アドレス、webhook の URI、または ITSM 接続の詳細。

Azure Resource Manager テンプレートを使用したアクション グループの構成に関する詳細については、「[アクション グループの Resource Manager テンプレート](monitoring-create-action-group-with-resource-manager-template.md)」を参照してください。

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Azure Portal を使用したアクション グループの作成 ##
1. [ポータル](https://portal.azure.com)で、**[モニター]** を選択します。 **[モニター]** ブレードでは、すべての監視設定とデータが 1 つのビューにまとめられています。

    ![[モニター] サービス](./media/monitoring-action-groups/home-monitor.png)
1. **[設定]** セクションで **[アクション グループ]** を選択します。

    ![[アクション グループ] タブ](./media/monitoring-action-groups/action-groups-blade.png)
1. **[アクション グループの追加]** を選択し、フィールドに入力します。

    ![[アクション グループの追加] コマンド](./media/monitoring-action-groups/add-action-group.png)
1. **[アクション グループ名]** ボックスおよび **[短い名前]** ボックスに名前を入力します。 短い名前は、通知がこのグループを使用して送信されるときに長い名前の代わりに使用されます。

      ![[アクション グループの追加] ダイアログ ボックス](./media/monitoring-action-groups/action-group-define.png)

1. **[サブスクリプション]** ボックスには、現在のサブスクリプションが自動入力されます。 このサブスクリプションにアクション グループが保存されます。

1. アクション グループが保存される **[リソース グループ]** を選択します。

1. 次に、各アクションを指定して、アクションの一覧を定義します。

    a. **[名前]**: このアクションの一意識別子を入力します。

    b. **[アクションの種類]**: 電子メール/SMS/プッシュ/音声、ロジック アプリ、webhook、ITSM、または Automation Runbook を選択します。

    c. **[詳細]**: アクションの種類に基づいて、電話番号、メール アドレス、webhook の URI、Azure アプリ、ITSM 接続、Automation Runbook を入力します。 ITSM アクションの場合は、さらに ITSM ツールで必要な **[作業項目]** および他のフィールドを指定します。

1. **[OK]** を選択して、アクション グループを作成します。

## <a name="action-specific-information"></a>アクション固有の情報
<dl>
<dt>Azure アプリのプッシュ</dt>
<dd>アクション グループには、最大 10 個の Azure アプリのアクションがあります。</dd>
<dd>現時点では Azure アプリのアクションは ServiceHealth アラートのみをサポートします。 その他のアラート日時は無視されます。 [サービスの正常性通知が投稿されるたびにアラートを設定](monitoring-activity-log-alerts-on-service-notifications.md)する方法を見る。</dd>

<dt>電子メール</dt>
<dd>電子メールは、次の電子メール アドレスから送信されます。 電子メールのフィルタリングが適切に構成されていることを確認してください

    - azure-noreply@microsoft.com
    - azureemail-noreply@microsoft.com
    - alerts-noreply@mail.windowsazure.com
    
</dd>
<dd>1 つのアクション グループには、電子メールに関するアクションが最大 1,000 個あります</dd>
<dd>[レート制限情報](./monitoring-alerts-rate-limiting.md)の記事を見る</dd>

<dt>ITSM</dt>
<dd>アクション グループには、最大 10 個の ITSM アクションがあります。</dd>
<dd>ITSM アクションには ITSM 接続が必要です。 [ITSM 接続](../log-analytics/log-analytics-itsmc-overview.md)の作成方法を確認してください。</dd>

<dt>ロジック アプリ</dt>
<dd>アクション グループには、最大 10 個のロジック アプリのアクションがあります。</dd>

<dt>Runbook</dt>
<dd>アクション グループには、最大 10 個の Runbook アクションがあります。</dd>

<dt>SMS</dt>
<dd>アクション グループには、最大 10 個の SMS アクションがあります。</dd>
<dd>[レート制限情報](./monitoring-alerts-rate-limiting.md)の記事を見る</dd>
<dd>[SMS アラート動作](monitoring-sms-alert-behavior.md)の記事を見る</dd>

<dt>音声</dt>
<dd>アクション グループには、最大 10 個の音声アクションがあります。</dd>
<dd>[レート制限情報](./monitoring-alerts-rate-limiting.md)の記事を見る</dd>

<dt>webhook</dt>
<dd>アクション グループには、最大 10 個の webhook アクションがあります。
<dd>再試行ロジック - 応答のタイムアウト期間は 10 秒です。 HTTP 状態コードの 408、429、503、504 が返されるか、または HTTP エンドポイントが応答しない場合、webhook の呼び出しが最大 2 回再試行されます。 1 回目の再試行は 10 秒後に実行されます。 2 回目となる最後の再試行は 100 秒後に実行されます。</dd>
</dl>

## <a name="manage-your-action-groups"></a>アクション グループの管理 ##
アクション グループを作成すると、**[モニター]** ブレードの **[アクション グループ]** セクションに表示されます。 次の操作を行うために管理するアクション グループを選択します。

* アクションの追加、編集、または削除。
* アクション グループの削除。

## <a name="next-steps"></a>次の手順 ##
* 詳細については、「[SMS アラート動作](monitoring-sms-alert-behavior.md)」を参照してください。  
* [アクティビティ ログ アラートに対する webhook スキーマについて理解](monitoring-activity-log-alerts-webhook.md)します。  
* [ITSM コネクタ](../log-analytics/log-analytics-itsmc-overview.md)について学習します。
* アラートの[レート制限](monitoring-alerts-rate-limiting.md)について学習します。
* [アクティビティ ログ アラートの概要](monitoring-overview-alerts.md)を把握し、アラートを受信する方法について学習します。  
* [サービスの正常性通知が投稿されるたびにアラートを設定](monitoring-activity-log-alerts-on-service-notifications.md)する方法について学習します。

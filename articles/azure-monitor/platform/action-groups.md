---
title: Azure Portal でのアクション グループの作成および管理
description: Azure Portal でアクション グループを作成および管理する方法について説明します。
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 2b427669fe692704343d5a3c2096df92cffb8d76
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53583994"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Azure Portal でのアクション グループの作成および管理
## <a name="overview"></a>概要 ##
アクション グループは、Azure サブスクリプションの所有者によって定義された通知設定のコレクションです。 Azure Monitor および Service Health のアラートでは、アクション グループを使用して、アラートがトリガーされたことをユーザーに通知します。 ユーザーの要件に応じて、さまざまなアラートで同じアクション グループを使用することも、異なるアクション グループを使用することもあります。

電子メールまたは SMS でユーザーに通知するようアクションが構成されている場合、ユーザーは自分がアクション グループに追加されたことを示す確認メッセージを受け取ります。

この記事では、Azure Portal でアクション グループを作成および管理する方法について説明します。

各アクションは次のプロパティで構成されます。

* **名前**: アクション グループ内の一意識別子。  
* **アクションの種類**: 実行するアクション。 たとえば、音声通話、SMS、電子メールの送信やさまざまな種類の自動化されたアクションのトリガーなどです。 この記事の後半の種類を参照してください。 
* **詳細**: *アクションの種類*によって異なる対応する詳細。 

Azure Resource Manager テンプレートを使用したアクション グループの構成に関する詳細については、「[アクション グループの Resource Manager テンプレート](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)」を参照してください。

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Azure Portal を使用したアクション グループの作成 ##
1. [ポータル](https://portal.azure.com)で、**[モニター]** を選択します。 **[モニター]** ブレードでは、すべての監視設定とデータが 1 つのビューにまとめられています。

    ![[モニター] サービス](./media/action-groups/home-monitor.png)
1. **[アラート]**、**[アクション グループの管理]** の順に選択します。

    ![[アクション グループの管理] ボタン](./media/action-groups/manage-action-groups.png)
1. **[アクション グループの追加]** を選択し、フィールドに入力します。

    ![[アクション グループの追加] コマンド](./media/action-groups/add-action-group.png)
1. **[アクション グループ名]** ボックスおよび **[短い名前]** ボックスに名前を入力します。 短い名前は、通知がこのグループを使用して送信されるときに長い名前の代わりに使用されます。

      ![[アクション グループの追加] ダイアログ ボックス](./media/action-groups/action-group-define.png)

1. **[サブスクリプション]** ボックスには、現在のサブスクリプションが自動入力されます。 このサブスクリプションにアクション グループが保存されます。

1. アクション グループが保存される **[リソース グループ]** を選択します。

1. 次に、各アクションを指定して、アクションの一覧を定義します。

    a. **[名前]**: このアクションの一意識別子を入力します。

    b. **[アクションの種類]**: 電子メール/SMS/プッシュ/音声、ロジック アプリ、Webhook、ITSM、または Automation Runbook を選択します。

    c. **[詳細]**: アクションの種類に基づいて、電話番号、メール アドレス、Webhook の URI、Azure アプリ、ITSM 接続、または Automation Runbook を入力します。 ITSM アクションの場合は、さらに ITSM ツールで必要な **[作業項目]** および他のフィールドを指定します。

1. **[OK]** を選択して、アクション グループを作成します。

## <a name="manage-your-action-groups"></a>アクション グループの管理 ##
アクション グループを作成すると、**[モニター]** ブレードの **[アクション グループ]** セクションに表示されます。 次の操作を行うために管理するアクション グループを選択します。

* アクションの追加、編集、または削除。
* アクション グループの削除。

## <a name="action-specific-information"></a>アクション固有の情報
**Azure アプリのプッシュ** - アクション グループには、最大 10 個の Azure アプリのアクションがあります。 現時点では Azure アプリのアクションは ServiceHealth アラートのみをサポートします。 その他のアラート日時は無視されます。 [サービスの正常性通知が投稿されるたびにアラートを設定](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)する方法を見る。

**電子メール** - 電子メールは、次の電子メール アドレスから送信されます。 電子メールのフィルタリングが適切に構成されていることを確認してください
   - azure-noreply@microsoft.com
   - azureemail-noreply@microsoft.com
   - alerts-noreply@mail.windowsazure.com

1 つのアクション グループには、電子メールに関するアクションが最大 1,000 個あります。 [レート制限情報](./../../azure-monitor/platform/alerts-rate-limiting.md)の記事を見る

**ITSM** - アクション グループには、最大 10 個の ITSM アクションがあります。ITSM アクションには ITSM 接続が必要です。 [ITSM 接続](../../azure-monitor/platform/itsmc-overview.md)の作成方法を確認してください。

**ロジック アプリ** - アクション グループには、最大 10 個のロジック アプリのアクションがあります

**Function App** - アクションとして構成された Function App の関数キーは関数の API から読み込まれます。現在、これにアプリ設定 "AzureWebJobsSecretStorageType" を "files" に構成するには v2 の関数アプリが必要です。詳細については、[Functions V2 でのキーの管理の変更]( https://aka.ms/funcsecrets)に関するページを参照してください。

**Runbook** - アクション グループには、最大 10 個の Runbook アクションがあります。Runbook ペイロードの制限については、「[Azure サブスクリプション サービスの制限](../../azure-subscription-service-limits.md)」をご覧ください

**SMS** -アクション グループには、最大 10 個の SMS アクションがあります。[レート制限情報](./../../azure-monitor/platform/alerts-rate-limiting.md)の記事を参照してください。[SMS アラート動作](../../azure-monitor/platform/alerts-sms-behavior.md)の記事を参照してください

**音声** - アクション グループには、最大 10 個の音声アクションがあります</dd>
[レート制限情報](./../../azure-monitor/platform/alerts-rate-limiting.md)の記事を見る</dd>

**Webhook** - アクション グループには、最大 10 個の webhook アクションがあります。 再試行ロジック - 応答のタイムアウト期間は 10 秒です。 Webhook の呼び出しが最大 2 回再試行されるのは、HTTP 状態コードの 408、429、503、504 が返されるか、または HTTP エンドポイントが応答しない場合です。 1 回目の再試行は 10 秒後に実行されます。 2 回目となる最後の再試行は 100 秒後に実行されます。

発信元 IP アドレスの範囲
    - 13.106.57.181
    - 13.106.54.3
    - 13.106.54.19
    - 13.106.38.142
    - 13.106.38.148
    - 13.106.57.196

これらの IP アドレスへの変更に関する更新情報を受け取るには、アクション グループ サービスに関する情報の通知を監視する[サービス正常性アラート](./../../azure-monitor/platform/service-notifications.md)を構成することをお勧めします。


## <a name="next-steps"></a>次の手順 ##
* 詳細については、「[SMS アラート動作](../../azure-monitor/platform/alerts-sms-behavior.md)」を参照してください。  
* [アクティビティ ログ アラートに対する webhook スキーマについて理解](../../azure-monitor/platform/activity-log-alerts-webhook.md)します。  
* [ITSM コネクタ](../../azure-monitor/platform/itsmc-overview.md)について学習します。
* アラートの[レート制限](../../azure-monitor/platform/alerts-rate-limiting.md)について学習します。
* [アクティビティ ログ アラートの概要](../../azure-monitor/platform/alerts-overview.md)を把握し、アラートを受信する方法について学習します。  
* [サービスの正常性通知が投稿されるたびにアラートを設定](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)する方法について学習します。

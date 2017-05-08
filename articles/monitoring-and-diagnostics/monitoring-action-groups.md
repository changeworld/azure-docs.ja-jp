---
title: "Azure Portal でのアクション グループの作成および管理 | Microsoft Docs"
description: 
author: anirudhcavale
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 9a290422bf897397942fccf2e3733d0709701e91
ms.lasthandoff: 03/31/2017


---
# <a name="create-and-manage-action-groups-in-azure-portal"></a>Azure Portal でのアクション グループの作成および管理
## <a name="overview"></a>概要 ##
この記事では、Azure Portal でアクション グループを作成および管理する方法について説明します。

アクション グループを使用して、受信者のリストを構成できます。 これらのグループはアクティビティ ログ アラートを定義するときに活用できます。また、アクティビティ ログ アラートがトリガーされるときに特定のアクション グループに通知が送信されます。

アクション グループには、最大 10 個のアクションの種類を設定できます。 アクションは、次の組み合わせによって定義されます。

**名前:** アクション グループ内の一意の識別子。  
**アクションの種類:**実行されるアクションを定義します。 オプションは、SMS の送信、電子メールの送信、または Webhook の呼び出しです。  
**詳細:** アクションの種類に基づいて、対応する電話番号、電子メール アドレス、または webhook の URI を入力する必要があります。

サービス正常性通知アラートに関する情報を設定し取得するには、次のいずれかを使用します。
* [Azure ポータル](monitoring-action-groups.md)
- [Resource Manager テンプレート](monitoring-create-action-group-with-resource-manager-template.md)

## <a name="creating-an-action-group-for-the-azure-portal"></a>Azure Portal のアクション グループの作成 ##
1.    [ポータル](https://portal.azure.com)で **[モニター]** サービスに移動します。

    ![監視](./media/monitoring-action-groups/home-monitor.png)
2.    **[モニター]** オプションをクリックして、[モニター] ブレードを開きます。 このブレードは、すべての監視設定とデータが 1 つにまとめられた統合ビューです。 最初に開くのは **[アクティビティ ログ]** セクションです。

3.    次に、**[アクション グループ]** セクションをクリックします。

    ![アクション グループ](./media/monitoring-action-groups/action-groups-blade.png)
4.    [アクション グループの**追加**] コマンドをクリックして、フィールドに入力します。

    ![アクション グループの追加](./media/monitoring-action-groups/add-action-group.png)
5.    アクション グループの **[名前]** と **[短い名前]** を入力します。短い名前は、このグループに送信される通知に表示されます。

      ![アクション グループの定義](./media/monitoring-action-groups/action-group-define.png)

6.    **[サブスクリプション]** は、アクション グループが保存される場所です。 現在操作を行っているサブスクリプションに自動的に入力されます。

7.    このアクションの **[リソース グループ]** を選択します。

8.    次の組み合わせによってアクションの一覧を定義します。
  1. **名前:** アクション グループ内の一意の識別子。
  2. **アクションの種類:**実行されるアクションを定義します。 オプションは、SMS の送信、電子メールの送信、または Webhook の呼び出しです。
  3. **詳細:** アクションの種類に基づいて、対応する電話番号、電子メール アドレス、または webhook の URI を入力する必要があります。

9.    完了したら **[OK]** を選択して、アクション グループを作成します。

## <a name="managing-your-action-groups"></a>アクション グループの管理 ##
アクション グループを作成すると、[モニター] サービスの [アクション グループ] セクションに表示されます。 管理するアクション グループを選択して、次の操作を実行できます。
* 受信者の追加、編集、または削除。
-    アクション グループの削除。

## <a name="next-steps"></a>次のステップ: ##
詳細については、「[SMS アラート動作](monitoring-sms-alert-behavior.md)」を参照してください  
[アクティビティ ログ アラートに対する webhook スキーマについて理解します。](monitoring-activity-log-alerts-webhook.md)  
アラートの[レート制限](monitoring-alerts-rate-limiting.md)について学習します。  
[アクティビティ ログ アラートの概要](monitoring-overview-alerts.md)を把握し、アラートを生成する方法について学習してください  
[サービスの正常性通知が投稿されるたびにアラートを設定](monitoring-activity-log-alerts-on-service-notifications.md)する方法


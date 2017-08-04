---
title: "サービス通知でアクティビティ ログ アラートを受け取る | Microsoft Docs"
description: "Azure サービスが発生したときに、SMS、電子メール、または Webhook で通知を受け取ります。"
author: johnkemnetz
manager: orenr
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
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 202e7ec116e5e49beaad8c2d570a3659236e9b0f
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="create-activity-log-alerts-on-service-notifications"></a>サービス通知のアクティビティ ログ アラートを作成する | Microsoft Docs
## <a name="overview"></a>概要
この記事では、Azure Portal を使用してサービスの正常性通知を行うアクティビティ ログ アラートを設定する方法について説明します。  

Azure でサービス正常性通知を Azure サブスクリプションに送信するときに、アラートを受け取ることができます。  
次の情報に基づくアラートを構成できます。
- サービス正常性通知のクラス (インシデント、メンテナンス、情報など)
- 影響を受けたサービス
- 影響を受けたリージョン
- 通知の状態 (アクティブ対解決済み)
- 通知レベル (詳細、警告、エラー)

次の方法でアラートを送信するユーザーを構成することもできます。
- 既存のアクション グループを選択する
- 新しいアクション グループを作成する (将来のアラートで使用できます)

アクション グループの詳細については、[こちら](monitoring-action-groups.md)をご覧ください。

Azure Resource Manager テンプレートを使用したサービス正常性通知アラートの構成に関する詳細については、[Resource Manager テンプレート](monitoring-create-activity-log-alerts-with-resource-manager-template.md)に関するページを参照してください。

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-with-the-azure-portal"></a>Azure Portal で新しいアクション グループのサービス正常性通知に関するアラートを作成する
1.  [ポータル](https://portal.azure.com)で **[監視]** サービスに移動します。

    ![監視](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)

2.  **[監視]** オプションをクリックして、[監視] ブレードを開きます。 最初に開くのは **[アクティビティ ログ]** セクションです。

3.  次に、**[アラート]** セクションをクリックします。

    ![アラート](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)

4.  **[アクティビティ ログ アラートの追加]** コマンドを選択し、フィールドに入力します。

    ![アラートの追加](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)

5.  アクティビティ ログ アラートの **[名前]** を入力し、**[説明]** を指定します。

    ![アラートの追加 - 新しいアクション グループ](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group.png)

6.  **[サブスクリプション]** は、アクティビティ ログ アラートが保存される場所です。 現在操作を行っているサブスクリプションに自動的に入力されます。 これは、アラート リソースがデプロイされ、監視されるサブスクリプションです。

7.  このアラートが **[サブスクリプション]** で関連付けられる **[リソース グループ]** を選択します。

8.  **[イベント カテゴリ]** で、[サービスの正常性] オプションを選択します。 通知を受け取るサービス正常性通知の **[サービス]、[リージョン]、[種類]、[状態]**、および **[レベル]** を選択します。

9.  **[新規]** アクション グループを作成し、その **[名前]** と **[短い名前]** を指定します。短い名前は、このアラートが有効になったときに送信される通知に表示されます。

10. 次に、受信者についての次の情報を入力して、受信者の一覧を定義します。

    a. **名前:** 受信者の名前、エイリアス、または識別子。

    b. **アクションの種類:** SMS、電子メール、または Webhook など、受信者と連絡を取る方法を選択します。

    c. **詳細:** 選択したアクションの種類の選択に基づいて、電話番号、電子メール アドレス、または Webhook の URI を入力します。

11. 完了したら **[OK]** を選択して、アラートを作成します。

数分後にアラートがアクティブになり、前述のようにトリガーされます。

アクティビティ ログ アラートの Webhook スキーマの詳細については、[ここをクリック](monitoring-activity-log-alerts-webhook.md)してください。

>[!NOTE]
>上記の手順で定義されたアクション グループは、将来のすべてのアラートの定義で既存のアクション グループとして再利用が可能です。
>
>

## <a name="create-an-alert-on-a-service-health-notification-using-an-existing-action-group-with-the-azure-portal"></a>Azure Portal で既存のアクション グループを使用して、サービス正常性通知に関するアラートを作成する
1.  [ポータル](https://portal.azure.com)で **[モニター]** サービスに移動します。

    ![監視](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)
2.  **[監視]** オプションをクリックして、[監視] ブレードを開きます。 最初に開くのは **[アクティビティ ログ]** セクションです。

3.  次に、**[アラート]** セクションをクリックします。

    ![アラート](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)
4.  **[アクティビティ ログ アラートの追加]** コマンドを選択し、フィールドに入力します。

    ![アラートの追加](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)
5.  アクティビティ ログ アラート の **[名前]** を入力し、**[説明]** を選択します。

    ![アラートの追加 - 既存のアクション グループ](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-existing-action-group.png)
6.  **[サブスクリプション]** は、アクティビティ ログ アラートが保存される場所です。 現在操作を行っているサブスクリプションに自動的に入力されます。 これは、アラート リソースがデプロイされ、監視されるサブスクリプションです。

7.  このアラートが **[サブスクリプション]** で関連付けられる **[リソース グループ]** を選択します。

8.  **[イベント カテゴリ]** で、[サービスの正常性] オプションを選択します。 通知を受け取るサービス正常性通知の **[サービス]、[リージョン]、[種類]、[状態]**、および **[レベル]** を選択します。

9.  **[通知手段]** で、**[既存のアクション グループ]** を選択します。 適切なアクション グループを選択します。

10. 完了したら **[OK]** を選択して、アラートを作成します。

数分後にアラートがアクティブになり、前述のようにトリガーされます。

## <a name="managing-your-alerts"></a>アラートの管理

アラートを作成すると、[監視] サービスの [アラート] セクションにアラートが表示されます。 管理するアラートを選択して、次の操作を実行できます。
* **編集**する。
* **削除**する。
* **無効**または**有効**にしてそのアラートの通知受信を一時的に停止または再開する。

## <a name="next-steps"></a>次のステップ:
- [サービス正常性の通知](monitoring-service-notifications.md)を参照する
- [通知のレート制限](monitoring-alerts-rate-limiting.md)を参照する
- [アクティビティ ログ アラート webhook スキーマ](monitoring-activity-log-alerts-webhook.md) の確認
- [アクティビティ ログ アラートの概要](monitoring-overview-alerts.md)を把握し、アラートを生成する方法について学習してください  
- [アクション グループ](monitoring-action-groups.md)の詳細を確認する


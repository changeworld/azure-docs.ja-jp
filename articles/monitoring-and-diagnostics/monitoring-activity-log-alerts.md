---
title: "アクティビティ ログ アラートの作成 | Microsoft Docs"
description: "アクティビティ ログで特定のイベントが発生した場合に、SMS、webhook、および電子メールで通知を受け取ります。"
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
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: cbdb1f866c51f2a2622d14e1987660e34cb1b5ff
ms.lasthandoff: 04/03/2017


---
# <a name="create-activity-log-alerts"></a>アクティビティ ログ アラートの作成

## <a name="overview"></a>概要
アクティビティ ログ アラートは、Azure Portal または Azure Resource Manager (ARM) を使用して管理可能な Azure リソースです。 この記事では、Azure Portal を使用してアクティビティ ログのイベントにアラートを設定する方法について説明します。

サブスクリプション内のリソースで実行された操作や、リソースの正常性に影響を与える可能性のあるサービスの正常性イベントについてアラートを受け取ることができます。 アクティビティ ログ アラートでは、アラートがデプロイされている特定のサブスクリプションに対するアクティビティ ログ イベントを監視します。

次の情報に基づいてアラートを設定できます。
* イベント カテゴリ ([サービス正常性イベントについてはここをクリック](monitoring-activity-log-alerts-on-service-notifications.md))
- リソース グループ
- リソース
- リソースの種類
- 操作の名前
- 通知レベル (詳細、情報、警告、エラー、重大)
- [Status]
- イベントの開始者 (アクター)

次の方法でアラートを送信するユーザーを設定することもできます。
* 既存のアクション グループを選択する
- 将来のアラート後で使用できる新しいアクション グループを作成する

アクション グループの詳細については、[こちら](monitoring-action-groups.md)をご覧ください。

サービス正常性通知アラートに関する情報を設定し取得するには、次のいずれかを使用します。
* [Azure ポータル](monitoring-activity-log-alerts.md)
- [Resource Manager テンプレート](monitoring-create-activity-log-alerts-with-resource-manager-template.md)

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-with-the-azure-portal"></a>Azure Portal での新しいアクション グループを使用したアクティビティ ログ イベントのアラートの作成
1.    [ポータル](https://portal.azure.com)で **[モニター]** サービスに移動します。

    ![監視](./media/monitoring-activity-log-alerts/home-monitor.png)
2.    **[モニター]** オプションをクリックして、[モニター] ブレードを開きます。 最初に開くのは **[アクティビティ ログ]** セクションです。

3.    次に、**[アラート]** セクションをクリックします。

    ![アラート](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.    **[アクティビティ ログ アラートの追加]** コマンドを選択し、フィールドに入力します。

5.    アクティビティ ログ アラートに**名前を付けて**、**[説明]** を選択します。 この内容は、アラートが発生したときに送信される通知に表示されます。

    ![アラートの追加](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

6.    **[サブスクリプション]** が、現在操作を行っているサブスクリプションに自動的に入力されます。 これは、アラート リソースがデプロイされ、監視されるサブスクリプションです。

    ![アラートの追加-新しいアクション グループ](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

7.    このアラートが **[サブスクリプション]** で関連付けられる **[リソース グループ]** を選択します。

8.    **[イベント カテゴリ]**、**[リソース グループ]**、**[リソース]**、**[リソースの種類]**、**[操作の名前]**、**[レベル]**、**[状態]**、および **[イベントの開始者 (アクター)]** の各値を入力し、このアラートを監視するイベントを識別します。

9.    **[新規]** アクション グループを作成し、**[名前]** および **[短い名前]** を指定します。短い名前は、このアラートが有効になったときに送信される通知に表示されます。

10.    次に、受信者についての次の情報を入力して、受信者の一覧を定義します。

    a. **名前:** 受信者の名前、エイリアス、または識別子。

    b. **アクションの種類:** SMS、電子メール、または Webhook など、受信者と連絡を取る方法を選択します。

    c. **詳細:** 選択したアクションの種類の選択に基づき、電話番号、電子メール アドレスまたは webhook の URI を入力します。

11.    完了したら **[OK]** を選択して、アラートを作成します。

数分後にアラートがアクティブになり、前述のようにトリガーされます。

webhook スキーマでのアクティビティ ログ アラートの詳細については、[ここをクリック](monitoring-activity-log-alerts-webhook.md)してください。

>[!NOTE]
>上記の手順で定義されたアクション グループは、将来のすべてのアラートの定義で既存のアクション グループとして再利用が可能です。
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-with-the-azure-portal"></a>Azure Portal での既存のアクション グループのアクティビティ ログ イベントに対するアラートの作成
1.    [ポータル](https://portal.azure.com)で **[モニター]** サービスに移動します。

    ![監視](./media/monitoring-activity-log-alerts/home-monitor.png)
2.    **[モニター]** オプションをクリックして、[モニター] ブレードを開きます。 最初に開くのは **[アクティビティ ログ]** セクションです。

3.    次に、**[アラート]** セクションをクリックします。

    ![アラート](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.    **[アクティビティ ログ アラートの追加]** コマンドを選択し、フィールドに入力します。

5.    アクティビティ ログ アラートに**名前を付けて**、**[説明]** を選択します。 この内容は、アラートが発生したときに送信される通知に表示されます。

    ![アラートの追加](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)
6.    **[サブスクリプション]** が、現在操作を行っているサブスクリプションに自動的に入力されます。

    ![アラートの追加 - 既存のアクション グループ](./media/monitoring-activity-log-alerts/activity-log-alert-existing-action-group.png)
7.    このアラートの **[リソース グループ]** を選択します。

8.    **[イベント カテゴリ]**、**[リソース グループ]**、**[リソース]**、**[リソースの種類]**、**[操作の名前]**、**[レベル]**、**[状態]**、および **[イベントの開始者 (アクター)]** の各値を入力し、このアラートを適用するイベントの範囲を指定します。

9.    **[通知手段]** で、**[既存のアクション グループ]** を選択します。 個別のアクション グループを選択します。

10.    完了したら **[OK]** を選択して、アラートを作成します。

数分後にアラートがアクティブになり、前述のようにトリガーされます。

## <a name="managing-your-alerts"></a>アラートの管理

アラートを作成すると、[モニター] サービスの [アラート] セクションにアラートが表示されます。 管理するアラートを選択して、次の操作を実行できます。
* アラートを**編集**する。
* アラートを**削除**する。
* アラートを**無効**または**有効**にしてそのアラートの通知受信を一時的に停止または再開する。

## <a name="next-steps"></a>次のステップ:
[アラートの概要](monitoring-overview-alerts.md)について把握します。  
[アクティビティ ログ アラートについての webhook スキーマ](monitoring-activity-log-alerts-webhook.md)を確認します。[アクション グループ](monitoring-action-groups.md)の詳細について学習します。  
[サービス正常性の通知](monitoring-service-notifications.md)について学習します。


---
title: "クラウド アプリとクラウド サービスの間の初めてのワークフローの作成 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps でワークフローを作成および実行して、システム統合シナリオとエンタープライズ アプリケーション統合 (EAI) シナリオのためのビジネス プロセスを自動化します。"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
keywords: "ワークフロー, クラウド アプリ, クラウド サービス, ビジネス プロセス, システム統合, エンタープライズ アプリケーション統合, EAI"
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/31/2017
ms.author: jehollan; estfan; LADocs
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6634f0ee88e68f2fcb09fd7534a88677e8efa029
ms.lasthandoff: 04/03/2017

---

# <a name="create-your-first-logic-app-workflow-to-automate-processes-between-cloud-apps-and-cloud-services"></a>クラウド アプリとクラウド サービスの間のプロセスを自動化する初めてのロジック アプリ ワークフローの作成

[Azure Logic Apps](logic-apps-what-are-logic-apps.md) でワークフローを作成して実行すると、コードを記述することなく、ビジネス プロセスをより簡単かつ迅速に自動化できます。 この最初の例では、Web サイトの新しいコンテンツの RSS フィードをチェックする基本的なロジック アプリ ワークフローを作成する方法を説明します。 このロジック アプリでは、Web サイトのフィードに新しい項目が出現したときに Outlook または Gmail アカウントから電子メールを送信します。

ロジック アプリを作成して実行するには、次のものが必要です。

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントで作業を開始](https://azure.microsoft.com/free/)できます。 また、[従量課金のサブスクリプションにサインアップ](https://azure.microsoft.com/pricing/purchase-options/)することもできます。

  Azure サブスクリプションは、ロジック アプリの使用に対する課金に使用されます。 Azure Logic Apps の[使用量の測定](../logic-apps/logic-apps-pricing.md)と[価格](https://azure.microsoft.com/pricing/details/logic-apps)に関するページを参照してください。

また、この例では、次のものも必要になります。

* Outlook.com、Office 365 Outlook、または Gmail アカウント

    > [!TIP]
    > 個人用の [Microsoft アカウント](https://account.microsoft.com/account)を取得している場合は、Outlook.com アカウントがあります。 それ以外で Azure の職場または学校アカウントを取得している場合は、**Office 365 Outlook** アカウントがあります。

* Web サイトの RSS フィードへのリンク。 この例では、[MSDN Channel 9 Web サイト](https://channel9.msdn.com/) (`https://s.ch9.ms/Feeds/RSS`) の RSS フィードを使用します。

## <a name="add-a-trigger-that-starts-your-workflow"></a>ワークフローを開始するトリガーの追加

"[*トリガー*](./logic-apps-what-are-logic-apps.md#logic-app-concepts)" は、ロジック アプリのワークフローを開始するイベントで、ロジック アプリで必要となる最初の項目です。

1. [Azure Portal](https://portal.azure.com "Azure Portal") にサインインします。

2. 次の図に示すように、左側のメニューから **[新規]** > **[Enterprise Integration]** > **[Logic App]** の順に選択します。

     ![Azure Portal、[新規]、[Enterprise Integration]、[Logic App]](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

   > [!TIP]
   > または、**[新規]** を選択し、検索ボックスに「`logic app`」と入力して Enter キーを押します。 次に、**[Logic App]** > **[作成]** の順に選択します。

3. ロジック アプリに名前を付け、Azure サブスクリプションを選択します。 次に、Azure リソース グループを作成するか選択します。Azure リソース グループは、関連性のある Azure リソースを整理および管理するのに役立ちます。 最後に、ロジック アプリをホストするデータ センターの場所を選択します。 準備ができたら、**[ダッシュボードにピン留めする]**、**[作成]** の順に選択します。

     ![ロジック アプリの詳細](media/logic-apps-create-a-logic-app/logic-app-settings.png)

   > [!NOTE]
   > **[ダッシュボードにピン留めする]** を選択した場合、ロジック アプリは、デプロイ後に Azure ダッシュボードに表示され、自動的に開かれるようになります。 目的のロジック アプリがダッシュボードに表示されない場合は、**[すべてのリソース]** タイルの **[See More (もっと見る)]** を選択し、ロジック アプリを選択します。 または、左側のメニューの **[その他のサービス]** を選択します。 **[Enterprise Integration]** の **[Logic Apps]** を選択し、ロジック アプリを選択します。

4. ロジック アプリを初めて開くと、作業を開始するために使用できるテンプレートがロジック アプリ デザイナーに表示されます。 ここでは、ロジック アプリを最初から作成するため、**[空のロジック アプリ]** を選択します。

    ロジック アプリ デザイナーが開き、ロジック アプリで使用できるサービスと "*トリガー*" が表示されます。

5. 検索ボックスに「`RSS`」と入力し、**[RSS - フィード項目が発行される場合]** トリガーを選択します。 

    ![RSS のトリガー](media/logic-apps-create-a-logic-app/rss-trigger.png)

6. 追跡する Web サイトの RSS フィードのリンクを入力します。 

     **頻度**と**間隔**を変更することもできます。 
     これらの設定によって、ロジック アプリで新しい項目をチェックする頻度が決まります (その期間中に見つかったすべての項目が返されます)。

     この例では、MSDN Channel 9 Web サイトに投稿された新しい項目を毎日チェックするように設定します。

     ![RSS フィード、頻度、および間隔を指定してトリガーを設定する](media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

7. 今までの作業内容を保存します  (デザイナーのコマンド バーで、**[保存]** を選択します)。

   ![ロジック アプリを保存する](media/logic-apps-create-a-logic-app/save-logic-app.png)

   保存すると、ロジック アプリがライブ状態となります。しかし、現時点のロジック アプリは、指定された RSS フィードの新しい項目をチェックする機能しか備えていません。 
   このサンプルをさらに実用的なロジック アプリにするには、トリガーが開始された後に実行されるアクションを追加します。

## <a name="add-an-action-that-responds-to-your-trigger"></a>トリガーに応答するアクションの追加

"[*アクション*](./logic-apps-what-are-logic-apps.md#logic-app-concepts)" は、ロジック アプリのワークフローによって実行されるタスクです。 ロジック アプリにトリガーを追加した後は、そのトリガーによって生成されたデータに対する操作を実行するアクションを追加できます。 この例では、Web サイトの RSS フィードに新しい項目が出現したときに電子メールを送信するアクションを追加します。

1. 次の図に示すように、デザイナーで、トリガーの下の **[新しいステップ]** > **[アクションの追加]** を順に選択します。

   ![アクションを追加する](media/logic-apps-create-a-logic-app/add-new-action.png)

   デザイナーに、[使用可能なコネクタ](../connectors/apis-list.md)が表示されます。ここで、トリガーが開始されたときに実行するアクションを選択します。

2. ご自分の電子メール アカウントに応じて、Outlook 用または Gmail 用の手順に従ってください。

   * Outlook アカウントから電子メールを送信するには、検索ボックスに「`outlook`」と入力します。 **[サービス]** で、**[Outlook.com]** (個人の Microsoft アカウントの場合) または **[Office 365 Outlook]** (Azure の職場または学校アカウントの場合) を選択します。 
   **[アクション]** で、**[電子メールの送信]** を選択します。

       ![Outlook 用に [電子メールの送信] アクションを選択する](media/logic-apps-create-a-logic-app/actions.png)

   * Gmail アカウントから電子メールを送信するには、検索ボックスに「`gmail`」と入力します。 
   **[アクション]** で、**[Send email (電子メールの送信)]** を選択します。

       ![[Gmail - Send email (Gmail - 電子メールの送信)] を選択する](media/logic-apps-create-a-logic-app/actions-gmail.png)

3. 資格情報の入力を求められたら、該当する電子メール アカウントのユーザー名とパスワードを使ってサインインします。 

4. このアクションの詳細 (送信先の電子メール アドレスなど) を指定し、電子メールに含めるデータのパラメーターを選択します。次に例を示します。

   ![電子メールに含めるデータを選択する](media/logic-apps-create-a-logic-app/rss-action-setup.png)

    Outlook を選択した場合、ロジック アプリはこの例のようになります。

    ![完成したロジック アプリ](media/logic-apps-create-a-logic-app/save-run-complete-logic-app.png)

5.    変更を保存します。 (デザイナーのコマンド バーで、**[保存]** を選択します)。

6. この段階で、手動でロジック アプリを実行してテストすることができます。 デザイナーのコマンド バーで、**[実行]** を選択します。 または、ロジック アプリが設定されたスケジュールに基づいて指定の RSS フィードをチェックするまで待ってもかまいません。

   ロジック アプリによって新しい項目が検出されると、選択されたデータを含む電子メールが送信されます。 
   新しい項目が見つからない場合、ロジック アプリは、電子メールの送信アクションをスキップします。

7. ロジック アプリの実行とトリガーの履歴を監視および確認するには、ロジック アプリのメニューの **[概要]** を選択します。

   ![ロジック アプリの実行とトリガーの履歴を監視および表示する](media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > 必要なデータが見つからない場合は、コマンド バーで **[最新の情報に更新]** を選択します。

   ロジック アプリの状態や実行とトリガーの履歴の詳細を確認したり、ロジック アプリを診断したりする場合は、[ロジック アプリのトラブルシューティング](logic-apps-diagnosing-failures.md)に関するページを参照してください。

      > [!NOTE]
      > ロジック アプリは、停止するまで実行され続けます。 今の時点でアプリを停止するには、ロジック アプリのメニューの **[概要]** を選択します。 コマンド バーで **[無効]** を選択します。

これで、初めての基本的なロジック アプリを設定し、実行することができました。 また、プロセスを自動化するワークフローを簡単に作成でき、コードをまったく記述することなくクラウド アプリとクラウド サービスを統合できることを学習しました。

## <a name="manage-your-logic-app"></a>ロジック アプリの管理

アプリを管理するために、ロジック アプリの状態チェック、編集、履歴の表示、停止、削除などのタスクを実行できます。

1. [Azure Portal](https://portal.azure.com "Azure Portal") にサインインします。

2. 左側のメニューの **[その他のサービス]** を選択します。 **[Enterprise Integration]** の **[Logic Apps]** を選択します。 自分のロジック アプリを選択します。 

   ロジック アプリのメニューを使用して、次のようなロジック アプリ管理タスクを実行できます。

   |タスク|手順| 
   |:---|:---| 
   | アプリの状態、実行履歴、一般情報を表示する| **[概要]** を選択します。| 
   | アプリを編集する | **[ロジック アプリ デザイナー]** を選択します。 | 
   | アプリのワークフローの JSON 定義を表示する | **[ロジック アプリ コード ビュー]** を選択します。 | 
   | ロジック アプリで実行された操作を表示する | **[アクティビティ ログ]** を選択します。 | 
   | ロジック アプリの以前のバージョンを表示する | **[バージョン]** を選択します。 | 
   | アプリを一時的に停止する | **[概要]** を選択し、コマンド バーで **[無効化]** を選択します。 | 
   | アプリを削除する | **[概要]** を選択し、コマンド バーで **[削除]** を選択します。 ロジック アプリの名前を入力し、**[削除]** を選択します。 | 

## <a name="get-help"></a>問い合わせ

[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)では、質問の投稿や質問への回答を行うことができるほか、他の Azure Logic Apps ユーザーがどのようなことを行っているかがわかります。

[Azure Logic Apps ユーザー フィードバック サイト](http://aka.ms/logicapps-wish)でアイデアへの投票やアイデアの投稿を行って、Azure Logic Apps とコネクタの改善にご協力ください。

## <a name="next-steps"></a>次のステップ

*  [条件を追加してワークフローを実行する](../logic-apps/logic-apps-use-logic-app-features.md)
*     [ロジック アプリ テンプレート](../logic-apps/logic-apps-use-logic-app-templates.md)
*  [Azure Resource Manager テンプレートを使用したロジック アプリの作成](../logic-apps/logic-apps-arm-provision.md)


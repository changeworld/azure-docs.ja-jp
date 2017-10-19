---
title: "システムとクラウド サービスの間の初めての自動化されたワークフローの作成 - Azure Logic Apps | Microsoft Docs"
description: "ロジック アプリを作成して実行することにより、システム統合シナリオと Enterprise Application Integration (EAI) シナリオのためのビジネス プロセスとワークフローを自動化します。"
author: ecfan
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
ms.date: 09/18/2017
ms.author: LADocs; estfan
ms.openlocfilehash: d62255ba6e3d5bdfbd792a47f3a92d4c88876742
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-logic-app-for-automating-workflows-and-processes-through-the-azure-portal"></a>ワークフローとプロセスを自動化するための最初のロジック アプリを Azure Portal を使用して作成する

自動化されたワークフローを [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md) で構築および実行することにより、コードを記述しなくてもシステムとサービスを統合することができます。 ワークフローを使用して簡単にタスクを自動化できることを示すため、このチュートリアルでは、Web サイト上の新しいコンテンツについて RSS フィードを確認し、フィードの新しい項目ごとに電子メールを送信する基本的なロジック アプリを作成します。 

![概要 - 最初のロジック アプリの例](./media/logic-apps-create-a-logic-app/logic-app-overview.png)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 空のロジック アプリを作成する。
> * RSS フィード項目が発行されたときにロジック アプリを開始するためのトリガーを追加する。
> * その RSS フィード項目の詳細が記載された電子メールを送信するためのアクションを追加する。
> * ロジック アプリを実行してテストする。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントで作業を開始](https://azure.microsoft.com/free/)できます。 また、[従量課金のサブスクリプションにサインアップ](https://azure.microsoft.com/pricing/purchase-options/)することもできます。

* [Azure Logic Apps がサポートする電子メール プロバイダー](../connectors/apis-list.md)からの通知の送信先となる電子メール アカウント。 たとえば、Office 365 Outlook、Outlook.com、Gmail などのサポートされているプロバイダーを使用できます。 このチュートリアルでは、Office 365 Outlook を使用します。

  > [!TIP]
  > 個人用の [Microsoft アカウント](https://account.microsoft.com/account)を取得している場合は、Outlook.com アカウントがあります。 それ以外で Azure の職場または学校アカウントを取得している場合は、Office 365 Outlook アカウントがあります。

* Web サイトの RSS フィードへのリンク。 この例では、[CNN.com Web サイトのトップ記事の RSS フィード](http://rss.cnn.com/rss/cnn_topstories.rss) (`http://rss.cnn.com/rss/cnn_topstories.rss`) を使用します。

## <a name="1-create-a-blank-logic-app"></a>1.空のロジック アプリを作成する 

1. [Azure Portal](https://portal.azure.com "Azure Portal") にサインインします。

2. Azure のメイン メニューで、**[新規]** > **[Enterprise Integration]\(エンタープライズ統合\)** > **[Logic App]\(ロジック アプリ\)** の順に選択します。

   ![Azure Portal、[新規]、[Enterprise Integration]、[Logic App]](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. 次の表に指定されている設定を使用して、ロジック アプリを作成します。

   ![ロジック アプリの詳細を指定する](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | 設定 | 推奨値 | Description | 
   | ------- | --------------- | ----------- | 
   | **名前** | <*ロジック アプリ名*> | 一意のロジック アプリ名を指定します。 | 
   | **サブスクリプション** | <*Azure サブスクリプション*> | 使用する Azure サブスクリプションを選択します。 | 
   | **[リソース グループ]** | <*Azure リソース グループ*> | Azure リソース グループを作成するか選択します。Azure リソース グループは、関連する Azure リソースの整理および管理に役立ちます。 | 
   | **場所** | <*Azure リージョン*> | ロジック アプリをデプロイするデータセンターのリージョンを選択します。 | 
   |||| 

4. 準備ができたら、**[ダッシュボードにピン留めする]**、**[作成]** の順に選択します。

   これで、ロジック アプリの Azure リソースが作成されました。 
   Azure によってロジック アプリがデプロイされたら、すばやく開始できるように Logic Apps デザイナーによって一般的なパターンのテンプレートが表示されます。

   > [!NOTE] 
   > **[ダッシュボードにピン留めする]** を選択した場合、ロジック アプリはデプロイ後に Azure ダッシュボードに表示され、Logic Apps デザイナーで自動的に開きます。 選択しなかった場合は、手動でロジック アプリを探して開くことができます。

5. ここで、**[テンプレート]** の **[空のロジック アプリ]** を選択すると、ロジック アプリを最初から作成できます。

   ![ロジック アプリ テンプレートを選択する](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   Logic Apps デザイナーには、使用可能な "[*コネクタ*](../connectors/apis-list.md)" とその "[*トリガー*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)" が表示されています。これらは、ロジック アプリ ワークフローを開始するために使用します。

   ![ロジック アプリのトリガー](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="2-add-a-trigger-for-starting-the-workflow"></a>2.ワークフローを開始するためのトリガーを追加する

各ロジック アプリは、"[*トリガー*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)" によって開始する必要があります。 トリガーは、特定のイベントが発生した場合、または設定した条件を新しいデータが満たしている場合に起動されます。 その後、Logic Apps エンジンにより、ワークフローを実行するためのロジック アプリ インスタンスが作成されます。 トリガーが起動するたびに、ロジック アプリ ワークフローを実行するインスタンスが個々に作成されます。

1. 検索ボックスに、フィルターとして「rss」と入力します。 **[RSS - フィード項目が発行される場合]** トリガーを選択します。 

   ![[RSS - フィード項目が発行される場合] トリガーを選択する](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. 追跡する Web サイトの RSS フィードのリンクを指定します (例: `http://rss.cnn.com/rss/cnn_topstories.rss`)。 繰り返し実行する間隔と頻度を設定します。 この例では、フィードを毎日確認するように、これらのプロパティを設定します。 

   ![RSS フィード、頻度、および間隔を指定してトリガーを設定する](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

3. 今までの作業内容を保存します  デザイナーのツール バーで、**[保存]** を選択します。
トリガーの詳細を折りたたんで非表示にするには、トリガーのタイトル バーを選択します。

   ![ロジック アプリを保存する](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   これでロジック アプリは利用可能になりますが、アクションをワークフローに追加するまでは、RSS フィードの新しい項目を確認する以外に何も行いません。 

## <a name="3-add-an-action-that-responds-to-the-trigger"></a>3.トリガーに応答するアクションを追加する

ここで、"[*アクション*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)" を追加します。これは、ロジック アプリ ワークフローで実行されるタスクです。 この例では、RSS フィードに新しい項目が表示されたときに電子メールを送信するアクションを追加します。

1. Logic Apps デザイナーのトリガーで、**[+ 新しいステップ]** > **[アクションの追加]** の順に選択します。

   ![アクションを追加する](./media/logic-apps-create-a-logic-app/add-new-action.png)

   デザイナーに、[使用可能なコネクタ](../connectors/apis-list.md)が表示されます。ここで、トリガーが開始されたときに実行するアクションを選択します。

   ![アクションの一覧から選択する](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. 検索ボックスに、フィルターとして「send email」と入力します。 電子メール プロバイダーに基づいて、一致するコネクタを検索して選択します。 次に、コネクタに対して "電子メールの送信" アクションを選択します。 For example: 

   * たとえば、Azure 職場または学校アカウントの場合は、Office 365 Outlook コネクタを選択します。 
   * 個人用 Microsoft アカウントの場合は、Outlook.com コネクタを選択します。 
   * Gmail アカウントの場合は、Gmail コネクタを選択します。 

   ここでは Office 365 Outlook コネクタを使用して続行します。 
   別のプロバイダーを使用する場合、手順は変わりませんが、UI の表示が異なることがあります。 

   ![[Office 365 Outlook - 電子メールの送信] アクションを選択する](./media/logic-apps-create-a-logic-app/actions.png)

3. 資格情報の入力を求められたら、該当する電子メール アカウントのユーザー名とパスワードを使ってサインインします。 

4. 次の表で指定された詳細を入力し、メールに含めるフィールドを選択します。

   | ターゲット | 手順 | 
   | -- | ----- | 
   | ワークフローに使用できるフィールドを選択する。 | 編集ボックスの中をクリックして **[動的なコンテンツ]** の一覧を開くか、**[動的なコンテンツの追加]** を選択します。 | 
   | その他の使用可能なフィールドを表示する。 | **[動的なコンテンツ]** の一覧で、各セクションの **[See more]\(表示を増やす\)** を選択します。  | 
   | 編集ボックスに空白行を追加する。 | Shift + Enter キーを押します。 | 
   | **[動的なコンテンツ]** の一覧を閉じる。 | **[動的なコンテンツの追加]** を再度選択します。 | 
   ||| 

   ![電子メールに含めるデータを選択する](./media/logic-apps-create-a-logic-app/rss-action-setup.png)

   | 設定 | 推奨値 | Description | 
   | ------- | --------------- | ----------- | 
   | **To** | <*受信者のメール アドレス*> | 受信者の電子メール アドレスを入力します。 テスト目的で自分の電子メール アドレスを使用できます。 | 
   | **[件名]** | CNN の新しい投稿: **フィード タイトル** | 電子メールの件名の内容を入力します。 <p>このチュートリアルでは、推奨テキストを入力し、トリガーの **[フィード タイトル]** フィールドを選択します。このフィールドは、フィード項目のタイトルを示します。 | 
   | **本文** | タイトル: **フィード タイトル** <p>発行日: **フィードのプライマリ リンク** <p>リンク: **プライマリ フィード リンク** | 電子メールの本文の内容を入力します。 <p>このチュートリアルでは、推奨テキストを入力し、これらのトリガーのフィールドを選択します。 <p>- **[フィード タイトル]**: フィード項目のタイトルを再度表示します。 </br>- **[フィードの公開日付]**: 項目が公開された日時を表示します。 </br>- **[プライマリ フィード リンク]**: フィード項目の URL を表示します。 | 
   |||| 

   > [!NOTE] 
   > 配列を格納するフィールドを選択すると、その配列を参照するアクションに "For each" ループが自動的に追加されます。 このように、ロジック アプリは、そのアクションを各配列項目に対して実行します。

5. 完了したら、変更を保存します。 デザイナーのツール バーで、**[保存]** を選択します。

   ![完成したロジック アプリ](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

   ロジック アプリをテストするには、次のセクションに進みます。

## <a name="4-run-and-test-your-workflow"></a>4.ワークフローを実行してテストする

1. テスト目的でロジック アプリを手動で実行するには、デザイナーのツール バーで **[実行]** を選択します。 または、設定したスケジュールに基づいて指定の RSS フィードをロジック アプリが確認するまで待ってもかまいません。

   ![ロジック アプリを実行する](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

   ロジック アプリは、新しい項目を検出すると、選択されたデータを含む電子メールを送信します。次に例を示します。

   ![新しい RSS フィード項目について送信された電子メール](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

   ロジック アプリは、新しい項目を検出しなかった場合、電子メールを送信するアクションをスキップし、次の機会を待ってからもう一度項目を確認します。 

2. ロジック アプリの実行とトリガーの履歴を確認するには、ロジック アプリのメニューで **[概要]** を選択します。
実行の詳細を表示するには、その実行の行を選択します。

   ![ロジック アプリの実行とトリガーの履歴を監視および表示する](./media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > 必要なデータが見つからない場合は、ツール バーの **[更新]** を選択してください。

   実行が成功したか失敗したかにかかわらず、[実行の詳細] ビューには、成功または失敗したステップが表示されます。 

   ![ロジック アプリの実行の詳細を表示する](./media/logic-apps-create-a-logic-app/logic-app-run-details.png)

   ロジック アプリの状態、実行履歴、トリガーの履歴の詳細を確認したり、ロジック アプリを診断したりするには、[ロジック アプリのトラブルシューティング](../logic-apps/logic-apps-diagnosing-failures.md)に関するページを参照してください。

3. 各ステップの入力と出力を表示するには、確認するステップを展開します。 この情報は、ロジック アプリの問題を診断してデバッグするのに役立ちます。 For example:

   ![ステップの詳細を表示する](./media/logic-apps-create-a-logic-app/logic-app-run-details-expanded.png)

   詳細については、[ロジック アプリの監視](../logic-apps/logic-apps-monitor-your-logic-apps.md)に関するページを参照してください。

これで、初めての基本的なロジック アプリを作成し、実行することができました。 この例では、コードをまったく使用することなく、システムやサービスを統合するためのプロセスを自動化するワークフローを簡単に作成する方法を示しました。

> [!NOTE]
> ロジック アプリは、停止するまで実行され続けます。 アプリを一時的に無効にするには、次のセクションに進んでください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルでは、リソースを使用して、Azure サブスクリプションで料金が発生する可能性があるアクションを実行します。 このチュートリアルとテストが完了したら、料金を発生させたくないリソースは必ず無効にするか、削除してください。

アプリを削除せずに、ロジック アプリの電子メールの実行と送信を停止できます。 ロジック アプリのメニューで、**[概要]** を選択します。 ツール バーで、**[無効にする]** を選択します。

![ロジック アプリをオフにする](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

## <a name="faq"></a>FAQ

**Q:** ロジック アプリを使用すると他に何ができますか? </br>
**A:** 他に実行できるタスクの例として、編集、JSON 定義の表示、アクティビティ ログの確認、ロジック アプリの削除などがあります。

その他のロジック アプリ管理タスクについては、ロジック アプリのメニューにある次のコマンドを確認してください。

| タスク | 手順 | 
| ---- | ----- | 
| アプリの状態、実行とトリガーの履歴、一般情報を表示する | **[概要]** を選択します。 | 
| アプリを編集する | **[ロジック アプリ デザイナー]** を選択します。 | 
| アプリのワークフローの JSON 定義を表示する | **[ロジック アプリ コード ビュー]** を選択します。 | 
| ロジック アプリで実行された操作を表示する | **[アクティビティ ログ]** を選択します。 | 
| ロジック アプリの以前のバージョンを表示する | **[バージョン]** を選択します。 | 
| アプリを一時的に停止する | **[概要]** を選択し、ツール バーの **[Disable]\(無効にする\)** を選択します。 | 
| アプリを削除する | **[概要]** を選択し、ツール バーの **[削除]** を選択します。 ロジック アプリの名前を入力し、**[削除]** を選択します。 | 
||| 

## <a name="get-support"></a>サポートを受ける

* Azure Logic Apps に関する質問については、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)を参照してください。

* Azure Logic Apps とコネクタの改善にご協力いただける場合は、 [Azure Logic Apps のユーザーからの意見のサイト](http://aka.ms/logicapps-wish)でアイデアへの投票やアイデアの送信を行ってください。

## <a name="next-steps"></a>次のステップ

* [Visual Studio でロジック アプリを作成する](../logic-apps/logic-apps-deploy-from-vs.md)
* [条件を追加してワークフローを実行する](../logic-apps/logic-apps-use-logic-app-features.md)
*   [ロジック アプリ テンプレート](../logic-apps/logic-apps-use-logic-app-templates.md)
* [Azure Resource Manager テンプレートを使用したロジック アプリの作成](../logic-apps/logic-apps-arm-provision.md)
* [Logic Apps の使用状況の測定](../logic-apps/logic-apps-pricing.md) 
* [Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps)

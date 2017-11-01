---
title: "システムとクラウド サービスの間のワークフローの自動化 - Azure Logic Apps | Microsoft Docs"
description: "システム統合と Enterprise Application Integration (EAI) のシナリオのためのワークフローを自動化するロジック アプリを作成します"
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
ms.date: 10/20/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 5906605192f9b03f612e6ca3a445434a23713d7f
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2017
---
# <a name="automate-your-first-workflow-to-process-data-with-a-logic-app"></a>ロジック アプリでデータを処理するための初めてのワークフローの自動化

組織のシステムとサービスをより迅速に統合するために、[Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md) でワークフローとビジネス プロセスを自動化できます。 このクイックスタートでは、ロジック アプリを作成することで、自動化されたワークフローを簡単に構築および実行する方法を紹介します。 サンプル アプリでは、Web サイトの RSS フィードで新しい項目がないか確認し、各項目に対して電子メールを送信するワークフローを自動化する方法を示します。

このサンプル ロジック アプリでは、次の例のような電子メールを送信します。

![新しい RSS フィード項目について送信された電子メール](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

構築するロジック アプリ ワークフローの概要は次のとおりです。

![概要 - ロジック アプリの例](./media/logic-apps-create-a-logic-app/logic-app-simple-overview.png)

このクイックスタートでは、次の方法について説明します。

> [!div class="checklist"]
> * 空のロジック アプリを作成します。
> * RSS フィードに新しい項目が表示されたときにワークフローを開始するトリガーを追加します。
> * その RSS フィード項目の詳細が記載された電子メールを送信するアクションを追加します。
> * ロジック アプリ ワークフローを実行します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

* 通知を送信するための、Azure Logic Apps でサポートされている任意の電子メール プロバイダーの電子メール アカウント。 たとえば、Office 365 Outlook、Outlook.com、または Gmail を使用できます。 サポートされているその他の電子メール コネクタについては、[コネクタの一覧](https://docs.microsoft.com/connectors/)を確認してください。 このクイックスタートでは、Office 365 Outlook を使用します。

  > [!TIP]
  > 個人用の [Microsoft アカウント](https://account.microsoft.com/account)を取得している場合は、Outlook.com アカウントがあります。 それ以外で Azure の職場または学校アカウントを取得している場合は、Office 365 Outlook アカウントがあります。

* Web サイトの RSS フィードへのリンク。 この例では、[Reuters Web サイトのトップ記事の RSS フィード](http://feeds.reuters.com/reuters/topNews) (`http://feeds.reuters.com/reuters/topNews`) を使用します。

このクイックスタートではコードを記述する必要はありませんが、Logic Apps ではコードを使用する他のシナリオもサポートされています (たとえば、[Azure Functions](../azure-functions/functions-overview.md) でロジック アプリから独自のコードを実行するなど)。

## <a name="create-a-blank-logic-app"></a>空のロジック アプリを作成する 

1. [Azure Portal](https://portal.azure.com "Azure Portal") にサインインします。 

2. Azure のメイン メニューで、**[新規]** > **[Enterprise Integration]\(エンタープライズ統合\)** > **[Logic App]\(ロジック アプリ\)** の順に選択します。

   ![Azure Portal、[新規]、[Enterprise Integration]、[Logic App]](./media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. 次の図の下にある表の設定を使用して、ロジック アプリを作成します。

   ![ロジック アプリの詳細を指定する](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | 設定 | 推奨値 | Description | 
   | ------- | --------------- | ----------- | 
   | **名前** | <*ロジック アプリ名*> | 一意のロジック アプリ名を指定します。 | 
   | **サブスクリプション** | *Azure サブスクリプション名* | 使用する Azure サブスクリプションを選択します。 | 
   | **[リソース グループ]** | *Azure リソース グループ名* | このロジック アプリ用の [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成し、このアプリに関連するすべてのリソースを整理します。 | 
   | **場所** | *Azure データセンターのリージョン* | ロジック アプリをデプロイするデータセンターのリージョンを選択します (たとえば米国西部)。 | 
   | **Log Analytics** | オフ | ロジック アプリの診断ログは有効にしますが、このクイックスタートでは**オフ**の設定のままにします。 | 
   |||| 

4. 準備ができたら、**[ダッシュボードにピン留めする]** を選択します。 そうすることで、ロジック アプリがデプロイ後に Azure ダッシュボードに自動的に表示され、開かれます。 **[作成]**を選択します。

   > [!NOTE]
   > ロジック アプリをピン留めしない場合、作業を続けるには、デプロイ後にロジック アプリを手動で探して開く必要があります。

   ロジック アプリのデプロイ後、Logic Apps デザイナーが開かれ、紹介ビデオのあるページが表示されます。 
   ビデオの下に、一般的なロジック アプリのパターンのテンプレートがあります。 
   このクイックスタートでは、ロジック アプリをゼロから構築します。 

5. 紹介ビデオおよび一般的なトリガーの下までスクロールします。 **[テンプレート]** で **[空のロジック アプリ]** を選択します。

   ![空のロジック アプリ テンプレートを選択する](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   Logic Apps デザイナーには、使用可能なコネクタとそのトリガーが表示されています。これらは、ロジック アプリ ワークフローを開始するために使用します。

   ![ロジック アプリのトリガー](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="add-a-trigger-to-detect-new-items"></a>新しい項目を検出するトリガーを追加する

すべてのロジック アプリ ワークフローは、[トリガー](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)で開始されます。 トリガーは、特定のイベントが発生した場合か、設定した条件を新しいデータが満たしている場合に起動します。 トリガーが起動するたびに、Logic Apps エンジンによって、ワークフローを開始および実行するロジック アプリ インスタンスが作成されます。

1. 検索ボックスに、フィルターとして「rss」と入力します。 **[RSS - フィード項目が発行される場合]** トリガーを選択します。 

   ![[RSS - フィード項目が発行される場合] トリガーを選択する](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. 監視する RSS フィードのリンクを指定します (例: `http://feeds.reuters.com/reuters/topNews`)。 繰り返しの間隔と頻度を設定します。 この例では、5 分ごとにフィードを確認します。

   ![RSS フィード、頻度、および間隔を指定してトリガーを設定する](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

   Logic Apps によって RSS フィードへの接続が作成されます。

   > [!TIP]
   > デザイナーでの表示を簡素化するために、図形の詳細を折りたたんで非表示にすることができます。そのためには、図形のタイトル バーをクリックしてください。

3. 作業内容を保存します。 デザイナーのツール バーで、**[保存]** を選択します。 

   ![ロジック アプリを保存する](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   ロジック アプリは現在稼働していますが、RSS フィードをチェックすること以外は何もしていません。 そこで、トリガーが起動したときに応答するアクションを追加してみましょう。

## <a name="add-an-action-to-send-email"></a>電子メールを送信するアクションを追加する

既にトリガーはあるので、RSS フィードに新しい項目が表示されたときに電子メールを送信する[アクション](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を追加します。 ワークフローは、トリガーの起動後にこのアクションを実行します。

1. Logic Apps デザイナーのトリガーで、**[+ 新しいステップ]** > **[アクションの追加]** の順に選択します。

   ![アクションを追加する](./media/logic-apps-create-a-logic-app/add-new-action.png)

   トリガーの起動時にロジック アプリで実行できるアクションがデザイナーに表示されます。

   ![アクションの一覧から選択する](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. 検索ボックスに、フィルターとして「send email」と入力します。 使用する電子メール コネクタを見つけて選択します。 次に、コネクタに対して "電子メールの送信" アクションを選択します。 For example: 

   * Azure の職場または学校アカウントには、Office 365 Outlook を選択します。 
   * 個人用 Microsoft アカウントには、Outlook.com を選択します。 
   * Gmail アカウントには、Gmail を選択します。 

   このクイックスタートでは、Office 365 Outlook を使用します。 
   別の電子メール プロバイダーを使用する場合、手順は変わりませんが、UI の表示が異なることがあります。 

   ![[Office 365 Outlook - 電子メールの送信] アクションを選択する](./media/logic-apps-create-a-logic-app/actions.png)

3. 資格情報の入力を求められたら、該当する電子メール アカウントのユーザー名とパスワードを使ってサインインします。 

   Logic Apps によって電子メール アカウントへの接続が作成されます。

4. ここで、電子メールに含めるデータを指定します。 

   1. **[宛先]** ボックスに、受信者の電子メール アドレスを入力します。 
   テスト目的で自分の電子メール アドレスを使用できます。

   2. **[件名]** ボックスに、電子メールの件名を入力します。 
   この例では、次のように、「New RSS item: 」と入力します。

      ![電子メールの件名を入力する](./media/logic-apps-create-a-logic-app/logic-app-add-subject.png)

      編集ボックス内をクリックすると、**動的なコンテンツを追加するための一覧**が表示され、アクションに追加可能なデータ フィールドを選択できるようになります。 
      動的コンテンツの一覧が表示されない場合は、各編集ボックスの下で **[動的なコンテンツの追加]** を選択します。

   3. **[動的なコンテンツの追加]** の一覧で、**[フィード タイトル]** を選択します。これで、項目のタイトルが電子メールに追加されます。

      ![電子メールの件名を入力する](./media/logic-apps-create-a-logic-app/logic-app-select-field.png)

      操作を終了すると、電子メールの件名は、次の例のようになります。

      ![追加されたフィード タイトル](./media/logic-apps-create-a-logic-app/added-feed-title.png)

      > [!NOTE] 
      > **categories-item** など、配列が格納されているフィールドを選択すると、そのフィールドを参照するアクションに "For each" ループが自動的に追加されます。 こうすることで、ロジック アプリは、そのアクションを各配列項目に対して実行できます。 
      > 
      > ループを削除するには、ループのタイトル バーで省略記号 (**...**) を選択し、**[削除]** を選択します。

   4. **[本文]** ボックスに、電子メールの本文の内容を入力します。 
   この例では、次のテキストを入力し、次のフィールドを選択します。

      ![電子メールの本文の内容を追加する](./media/logic-apps-create-a-logic-app/logic-app-complete.png)

      | フィールド | Description | 
      | ----- | ----------- | 
      | **フィード タイトル** | 項目のタイトルが表示されます。 | 
      | **フィードの公開日付** | 項目の公開日時が表示されます。 | 
      | **プライマリ フィード リンク** | 項目の URL が表示されます。 | 
      ||| 

      > [!TIP]
      > 編集ボックスで空白行を追加するには、Shift + Enter キーを押します。 
      
5. 作業内容を保存します。 デザイナーのツール バーで、**[保存]** を選択します。

   ![完成したロジック アプリ](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

## <a name="run-your-logic-app-workflow"></a>ロジック アプリ ワークフローを実行する

ロジック アプリを手動で開始するには、デザイナーのツール バーで **[実行]** を選択します。 または、設定したスケジュールでロジック アプリが実行されるまで待つこともできます。

![ロジック アプリを実行する](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

RSS フィードに新しい項目がある場合、ロジック アプリは新しい項目ごとに電子メールを送信します。 たとえば、このロジック アプリは、次のような Outlook 電子メールを送信します。

![新しい RSS フィード項目について送信された電子メール](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

フィードに新しい項目がなかった場合、ロジック アプリは電子メールを送信する手順をスキップし、次の機会を待ってからもう一度項目を確認します。 

> [!TIP]
> 電子メールが届かない場合は、迷惑メール フォルダーを確認してください。 また、ロジック アプリが正しく動作しているかどうかわからない場合は、[ロジック アプリのトラブルシューティング](../logic-apps/logic-apps-diagnosing-failures.md)に関するページを参照してください。

これで、初めてのロジック アプリを作成し、実行することができました。 このクイックスタートでは、システムとサービスを統合するための自動化ワークフローを簡単かつ迅速に作成できることを説明しました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

ロジック アプリは、アプリを無効にしたり削除したりするまで稼働し続け、Azure サブスクリプションに対する課金が発生する可能性があります。 また、ロジック アプリのために接続を作成すると、ロジック アプリを削除した後も、それらの接続はそのまま残ります。 

完了後は、料金を発生させたくないリソースや残したくないリソースは、必ず無効にするか削除してください。 このクイックスタート用に作成したすべてのリソースを削除するには、このロジック アプリ用に作成した Azure リソース グループを削除します。 

### <a name="delete-resource-group"></a>Delete resource group

ロジック アプリに関連するものを何も残したくない場合は、このクイックスタート用に作成したリソース グループと、関連するすべてのリソースを削除してください。 詳細については、[Azure リソース グループの管理方法](../azure-resource-manager/resource-group-portal.md#manage-resources)に関するページを参照してください。

1. Azure メニューで **[リソース グループ]** を選択します。

2. 削除するリソース グループを選択します。 リソース グループのメニューで、**[概要]** を選択します (まだ選択されていない場合)。 

3. 削除するグループ内のすべてのリソースを確認します。 準備ができたら、リソース グループのツール バーで **[リソース グループの削除]** を選択します。

### <a name="turn-off-logic-app"></a>ロジック アプリを無効にする

作業内容を削除することなくロジック アプリの実行を停止するには、アプリを無効にします。 

ロジック アプリのメニューで、**[概要]** を選択します。 ツール バーで、**[無効にする]** を選択します。

  ![ロジック アプリをオフにする](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > ロジック アプリのメニューが表示されない場合は、Azure ダッシュボードに戻り、ロジック アプリを開き直してください。

### <a name="delete-logic-app"></a>ロジック アプリを削除する

ロジック アプリだけを削除し、作成した接続のような他のすべての関連リソースは保持することができます。

1. ロジック アプリのメニューで、**[概要]** を選択します。 ツール バーで、**[削除]** を選択します。 

   ![ロジック アプリを削除する](./media/logic-apps-create-a-logic-app/delete-logic-app.png)

   > [!TIP]
   > ロジック アプリのメニューが表示されない場合は、Azure ダッシュボードに戻り、ロジック アプリを開き直してください。

2. ロジック アプリを削除することに同意し、**[削除]** を選択します。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](http://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [事前構築済みテンプレートを使用したロジック アプリ ワークフローの作成](../logic-apps/logic-apps-create-logic-apps-from-templates.md)
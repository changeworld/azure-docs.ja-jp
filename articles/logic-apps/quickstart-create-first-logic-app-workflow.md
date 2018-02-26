---
title: "初めての自動化されたワークフローの作成 - Azure Logic Apps | Microsoft Docs"
description: "このクイックスタートでは、Azure Logic Apps によるワークフロー自動化の基本について説明します。システム統合と Enterprise Application Integration (EAI) によってシステムとクラウド サービスの連携を図るシナリオを想定しています。"
author: ecfan
manager: anneta
editor: 
services: logic-apps
keywords: "ワークフロー, クラウド サービス, システム統合, Enterprise Application Integration, EAI"
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 1/12/2018
ms.author: LADocs; estfan
ms.openlocfilehash: d382144c202b2b7f5623f2cca2a82c384387e8ca
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="quickstart-build-your-first-logic-app-workflow---azure-portal"></a>クイックスタート: 初めてのロジック アプリ ワークフローの作成 - Azure Portal

このクイックスタートでは、[Azure Logic Apps](../logic-apps/logic-apps-overview.md) を使って、自動化されたワークフローを作成する基本的な方法を紹介しています。 この記事では、Web サイトの RSS フィードを定期的にチェックして新しい項目を検出するロジック アプリを作成します。 新しい項目が存在する場合、ロジック アプリから項目ごとにメールが送信されます。 完成したロジック アプリの大まかなワークフローは、次のようになります。

![概要 - ロジック アプリの例](./media/quickstart-create-first-logic-app-workflow/overview.png)

このクイックスタートの手順に従うためには、Office 365 Outlook、Outlook.com、Gmail など、Logic Apps に対応しているプロバイダーのメール アカウントが必要です。 その他のプロバイダーについては、[こちらのコネクタ一覧を参照](https://docs.microsoft.com/connectors/)してください。 このロジック アプリでは、Office 365 Outlook アカウントを使います。 別のメール アカウントをお使いの場合でも、全体的な手順は同じですが、UI がやや異なる場合があります。 

また、Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

Azure アカウントの資格情報で <a href="https://portal.azure.com" target="_blank">Azure Portal</a> にサインインします。

## <a name="create-your-logic-app"></a>ロジック アプリを作成する 

1. Azure のメイン メニューで、**[リソースの作成]** > **[Enterprise Integration]** > **[Logic App]** の順に選択します。

   ![ロジック アプリを作成する](./media/quickstart-create-first-logic-app-workflow/create-logic-app.png)

3. **[ロジック アプリの作成]** で、次に示すようにロジック アプリの詳細を入力します。 終了したら、**[ダッシュボードにピン留めする]** > **[作成]** を選択します。

   ![ロジック アプリの詳細を指定する](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Setting | 値 | [説明] | 
   | ------- | ----- | ----------- | 
   | **名前** | MyFirstLogicApp | ロジック アプリの名前 | 
   | **サブスクリプション** | <*Azure サブスクリプションの名前*> | Azure サブスクリプションの名前 | 
   | **[リソース グループ]** | My-First-LA-RG | 関連するリソースの整理に使用する[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)の名前 | 
   | **場所** | 米国東部 2 | ロジック アプリの情報の保存先となるリージョン | 
   | **Log Analytics** | オフ | 診断ログの場合は、この設定を**オフ**のままにしてください。 | 
   |||| 

3. アプリのデプロイ後、Logic Apps デザイナーが開き、紹介ビデオやよく使用されるトリガーが含まれたページが表示されます。 **[テンプレート]** で **[空のロジック アプリ]** を選択します。

   ![空のロジック アプリ テンプレートを選択する](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

次に、新しい RSS フィード項目が発行されたときに起動される[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)を追加します。 すべてのロジック アプリは必ずトリガーから起動され、トリガーは、特定のイベントが発生するか特定の条件が満たされたときに起動されます。 トリガーが起動するたびに、Logic Apps エンジンによって、ワークフローを開始および実行するロジック アプリ インスタンスが作成されます。

## <a name="check-rss-feed-with-a-trigger"></a>トリガーを使って RSS フィードをチェックする

1. デザイナーの検索ボックスに「rss」と入力します。 **[RSS - フィード項目が発行される場合]** トリガーを選択します。

   ![[RSS - フィード項目が発行される場合] トリガーを選択する](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss.png)

2. トリガーに関する情報を次のように入力します。 

   ![RSS フィード、頻度、および間隔を指定してトリガーを設定する](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss-settings.png)

   | Setting | 値 | [説明] | 
   | ------- | ----- | ----------- | 
   | **RSS フィードの URL** | ```http://feeds.reuters.com/reuters/topNews``` | 監視する RSS フィードのリンク | 
   | **間隔** | 1 | チェックの間隔 (単位数) | 
   | **頻度** | [分] | チェックの間隔に使う時間の単位  | 
   |  |  |  | 

   ロジック アプリのトリガーには、間隔と頻度の組み合わせでそのスケジュールを定義します。 
   このロジック アプリでは、1 分おきにフィードをチェックします。

3. ここではトリガーの詳細を非表示にするために、トリガーのタイトル バー内をクリックします。

   ![シェイプを折りたたんで詳細を非表示](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

4. ロジック アプリを保存し、 デザイナーのツール バーで、**[保存]** を選択します。 

ロジック アプリは現在稼働していますが、RSS フィードをチェックすること以外は何もしていません。 そこで、トリガーが起動したときに反応するアクションを追加します。

## <a name="send-email-with-an-action"></a>アクションを使ってメールを送信する

今度は、RSS フィードに新しい項目が発行されたときにメールを送信する[アクション](../logic-apps/logic-apps-overview.md#logic-app-concepts)を追加します。 

1. **[フィード項目が発行される場合]** トリガーから **[+ 新しいステップ]** > **[アクションの追加]** の順に選択します。

   ![アクションを追加する](./media/quickstart-create-first-logic-app-workflow/add-new-action.png)

2. **[アクションを選択してください]** で「メールの送信」を検索し、目的のメール プロバイダーの "メールの送信" アクションを選択します。 アクション一覧をフィルタリングしてサービスを絞り込み、**[コネクタ]** の一番上に表示されるコネクタを選択してください。

   ![[Office 365 Outlook - 電子メールの送信] アクションを選択する](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   * Azure の職場または学校アカウントには、Office 365 Outlook を選択します。 
   * 個人用 Microsoft アカウントには、Outlook.com を選択します。

3. 資格情報を求められた場合は、お使いのメール アカウントへの接続が Logic Apps によって作成されるように、そのメール アカウントにサインインします。

4. **[電子メールの送信]** アクションで、メールに設定するデータを指定します。 

   1. **[宛先]** ボックスに、受信者の電子メール アドレスを入力します。 
   テスト目的で自分の電子メール アドレスを使用できます。

      パラメーター リストまたは **[動的なコンテンツの追加]** リストが表示されますが、ここでは無視してください。 
      いくつかの編集ボックスは、その内部をクリックすると、このリストが表示され、先行するステップから、ワークフローへの入力として追加できるパラメーターがすべて表示されます。
      どちらのリストが表示されるかは、お使いのブラウザーの幅によって決まります。

   2. **[件名]** ボックスに「```New RSS item: ```」と入力し、その後ろに空白スペースを追加します。

      ![電子メールの件名を入力する](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)
 
   3. パラメーター リストまたは **[動的なコンテンツの追加]** リストから **[フィード タイトル]** を選択して RSS 項目のタイトルを追加します。

      パラメーター リストの例を次に示します。

      ![パラメーター リスト - "フィード タイトル"](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-parameters-list.png)

      一方、動的コンテンツ リストは次のように表示されます。

      ![動的コンテンツ リスト - "フィード タイトル"](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      操作を終了すると、電子メールの件名は、次の例のようになります。

      ![追加されたフィード タイトル](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      デザイナーに "For each" ループが表示された場合、**[categories-item]\(カテゴリ-項目\)** フィールドなど、配列を含むフィールドを選択したことが原因です。 
      この種のフィールドを参照するアクションの前後には、デザイナーによってこのループが自動的に追加されます。 
      そうすることで個々の配列項目に同じアクションが実行されます。 
      ループを削除するには、ループのタイトル バーで**省略記号** (**...**) を選択し、**[削除]** を選択します。

   4. **[Body]\(本文\)** ボックスには、メール本文に使用するテキストを次のように入力し、対応するフィールドを選択します。 
   編集ボックスで空白行を追加するには、Shift + Enter キーを押します。 

      ![電子メールの本文の内容を追加する](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Setting | [説明] | 
      | ----- | ----------- | 
      | **フィード タイトル** | 項目のタイトル | 
      | **フィードの公開日付** | 項目の公開日時 | 
      | **プライマリ フィード リンク** | 項目の URL | 
      ||| 
   
5. ロジック アプリを保存し、

ロジック アプリのテストに進みます。

## <a name="run-your-logic-app"></a>ロジック アプリを実行する

ロジック アプリを手動で開始するには、デザイナーのツール バーで **[実行]** を選択します。 または、指定したスケジュール (1 分おき) でロジック アプリが実行されるのを待ちます。 RSS フィードに新しい項目がある場合、ロジック アプリは新しい項目ごとに電子メールを送信します。 ただし、フィードに新しい項目がなかった場合、ロジック アプリはトリガーを起動する手順をスキップし、次の機会を待ってからもう一度項目をチェックします。 

このロジック アプリからは、次のようなメールが送信されます。

![新しい RSS フィード項目について送信された電子メール](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

メールが届かない場合は、迷惑メール フォルダーを確認してください。 この種のメールは、迷惑メール フィルターによってリダイレクトされる場合があります。 

お疲れさまでした。初めてのロジック アプリを作成し、実行することができました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、ロジック アプリと関連リソースを含んだリソース グループを削除しましょう。 Azure のメイン メニューから **[リソース グループ]** に移動し、対象のロジック アプリのリソース グループを選択します。 **[リソース グループの削除]** を選択します。 確認のためにリソース グループ名を入力し、**[削除]** を選択します。

![[リソース グループ] > [概要] > [リソース グループの削除]](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](http://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、初めてのロジック アプリを作成しました。作成したロジック アプリは、指定したスケジュール (1 分おき) で RSS の更新をチェックし、更新があったときはアクション (メール送信) を実行するものです。 さらに理解を深めるために、より高度なスケジュール ベースのワークフローを作成するチュートリアルに取り組みましょう。

> [!div class="nextstepaction"]
> [スケジュールに基づいたロジック アプリでトラフィックをチェックする](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
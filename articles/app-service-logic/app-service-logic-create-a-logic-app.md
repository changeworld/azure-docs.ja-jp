<properties
	pageTitle="ロジック アプリを作成する | Microsoft Azure"
	description="App Services ロジック アプリを作成する方法を説明します。"
	authors="stepsic-microsoft-com"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/10/2015"
	ms.author="stepsic"/>

# 新しいロジック アプリを作成する

| クイック リファレンス |
| --------------- |
| [Logic Apps の定義言語](https://msdn.microsoft.com/library/azure/dn948512.aspx?f=255&MSPPError=-2147217396) |
| [Logic Apps コネクターのドキュメント](https://azure.microsoft.com/ja-JP/documentation/articles/app-service-logic-connectors-list/) |
| [Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/ja-JP/home?forum=azurelogicapps) |

このトピックでは、初めての方に向けて [App Service Logic Apps](app-service-logic-what-are-logic-apps.md) の使い方を簡単に説明します。今回は、興味を持ったツイートを Dropbox フォルダーに保存できるようにするまでの流れを見ていきます。

このシナリオでは、以下のものが必要になります。

- Azure サブスクリプション
- Twitter アカウント
- Dropbox アカウント

<!--- TODO: Add try it now information here -->

## コネクタを取得する

まずは、使用するコネクタとして [Dropbox Connector](app-service-logic-connector-dropbox.md) と [Twitter Connector](app-service-logic-connector-twitter.md) の 2 つを作成する必要があります。Twitter API でリダイレクトが行われるため、無料のアプリを Twitter に登録する必要もあります。コネクタを作成する手順は、以下のとおりです。

1. Azure ポータルにサインインします。

2. ホーム画面の [[Marketplace]](https://portal.azure.com/#blade/HubsExtension/GalleryFeaturedMenuItemBlade/selectedMenuItemId/apiapps) をクリックし、Twitter を検索します (または、[ここをクリックします](https://portal.azure.com/#create/microsoft_com.TwitterConnector.0.2.2))。

3. **[Twitter Connector]** を選択し、**[作成]** をクリックします。すべての設定が表示されます。名前は **Twitter Connector** のままでかまいません。
4. **[パッケージの設定]** を選択します。ここに、Twitter アプリケーションからの情報を入力する必要があります。次の手順で、無料のアプリケーションを設定できます。
	1. [[Twitter アプリの登録ページ]](http://apps.twitter.com) に移動します。
	2. 新しいアプリを作成します。
	3. 名前と説明を入力します。Web サイトの任意の URL を入力し、コールバック URL は空白のままにしておくことができます。
	4. 登録が完了したら、Twitter の **Consumer Key** を Azure の **clientId** フィールドにコピーし、Twitter の **Consumer Secret** を **clientSecret** にコピーします。
	5. Azure ウィンドウで **[OK]** をクリックして、他の API 設定に戻ります。

5. **[App Service プランの新規作成]** に、プランの名前を入力します。

	>[AZURE.NOTE]このセクションに示した手順では、新しい App Service のプランを作成することを前提に話を進めています。既存の App Service プランを使用する場合は、**[既存の選択]** をクリックして既存のプランを選択した後、このセクションの最後の手順に進んでください。すべてのアプリをホストするプランが必要です。

6.  新しいプランの**価格レベル**を選択します。

	>[AZURE.NOTE]既定では、Logic Apps 向けにお勧めとされるプランのみが表示されます。利用可能なプランをすべて表示する場合には、**[すべて表示]** をクリックしてください。ロジック アプリを Free レベルで実行する場合には、実行間隔は 1 時間ごと、使用できるアクションは 1 か月につき 1,000 回までとなります。

7. フローの**リソース グループ**を作成します。

	リソース グループは、アプリのコンテナーとしての役割を果たすものです。同じアプリのリソースはすべて、同じリソース グループに入ります。

8. Azure サブスクリプションが複数ある場合には、使用するものを 1 つ選択します。

9. ロジック アプリの**場所**を選択します。

	![[API アプリの作成] ビュー](./media/app-service-logic-create-a-logic-app/gallery.png)

10. **[作成]** をクリックします。1 ～ 2 分でプロビジョニングが始まります。

11. [Dropbox](https://portal.azure.com/#create/microsoft_com.DropboxConnector.0.2.2) についても、同じ手順を繰り返します。

## ロジック アプリを起動する

次は、新しいロジック アプリを作成します。

1. 画面左下の **[+ 新規]** をクリックします。**[Web + Mobile]** を展開し、**[Logic App]** をクリックします。

 	[ロジック アプリの作成] ビューが表示されるので、開始するための基本設定を入力していきます。

	![[ロジック アプリの作成] ビュー](./media/app-service-logic-create-a-logic-app/createlogicapp.png)

2. **[名前]** には、ロジック アプリの名前として意味のある文字列を入力します。

3. **App Service** プランには、コネクタの作成時に使用したものを選択します。これにより、場所、サブスクリプション、リソース グループが自動的に選択されます。

基本となる設定は以上ですが、**[作成]** はまだクリックしないでください。次に、ワークフローにトリガーとアクションを追加します。

## トリガーを追加する

トリガーとは、ロジック アプリの実行の引き金となるものです。ここでは、繰り返しトリガーを追加します。このトリガーは、事前に設定したスケジュールでワークフローを開始するためのものです。

1. **[ロジック アプリの作成]** ビューで、**[トリガーとアクション]** をクリックします。

	フローを表示するデザイナーが全画面表示されます。右側に、トリガーを設定できるサービスが一覧表示されています。

2. 上部のセクションで、**[繰り返し]** をクリックします。

	繰り返しの設定を指定するためのボックスが表示されます。

	![定期的なアイテム](./media/app-service-logic-create-a-logic-app/recurrence.png)

3.  (1 時間ごとなど) 繰り返しの **[頻度]** と **[間隔]** を選択し、緑色のチェック マークをクリックします。

次に、フローにアクションを追加します。

## Twitter アクションを追加する

アクションとは、ワークフローの動作を指します。アクションの数には上限はありません。また、アクションを調整して、あるアクションの情報がその次のアクションに送られるようにすることもできます。

1. 右側のウィンドウで、**[Twitter Connector]** をクリックします。

2. 読み込みが終わったら **[承認]** をクリックし、Twitter アカウントにサインインして **[アプリを認証]** をクリックします。

	これにより、コネクタが Twitter アカウントにアクセスできるようになります。Twitter コネクタに用意されている処理のリストが表示されます。

	![アクション](./media/app-service-logic-create-a-logic-app/actions.png)

	> [AZURE.NOTE]**[承認]** ボタンは、OAUTH セキュリティを使用して、Twitter などの SaaS サービスに接続します。OAUTH の詳細については、「[SaaS コネクタでの OAUTH セキュリティについて](app-service-logic-oauth-security.md)」を参照してください。

3. **[ツイートの検索]** をクリックし、**[クエリの指定]** に「`#MicrosoftAzure`」などの文字を入力して、緑色のチェック マークをクリックします。

	![Twitter の検索](./media/app-service-logic-create-a-logic-app/twittersearch.png)

これで、Twitter コネクタがワークフローの一部となりました。

## Dropbox アクションを追加してアプリを作成する

最後に、ツイートを Dropbox のファイルにアップロードするアクションを追加します。

1. 右側のウィンドウで、**[Dropbox Connector]** をクリックします。

2. プロビジョニングが完了したら **[承認]** をクリックし、Dropbox アカウントにサインインして **[許可]** をクリックします。

	![Dropbox コネクタの承認](./media/app-service-logic-create-a-logic-app/authorize.png)

	これで、コネクタが Dropbox アカウントにアクセスできるようになります。Dropbox コネクタに用意されている処理のリストが表示されます。

4. **[ファイルのアップロード]** をクリックします。

	Dropbox コネクタの設定が表示されるので、ここで Twitter の検索結果のデータを Dropbox に送るように設定していきます。

	![Dropbox](./media/app-service-logic-create-a-logic-app/dropbox.png)

3. **[ファイルパス]** フィールドには、「`/tweet.txt`」と入力します。

4. **[コンテンツ]** フィールドは、`...` ボタン、**[ツイートのテキスト]** オプションの順にクリックします。

	これによって、値 `@first(body('twitterconnector')).TweetText` がテキスト ボックスに入力されます。この値の各部の意味は、以下のとおりです。

	部分 | 説明
	------------------------------------------ | ------------
	 `@` | 実際の値ではなく、関数を入力しようとしていることを示します。
	`actions('twitterconnector').outputs.body` | Twitter コネクタのクエリの結果として返されたツイートを取得します。
	`first()` | ツイートの検索アクションによって返されるのはリストであるものの、アップロードするのは 1 つのファイルのみとなります。
	`.TweetText` | ツイート メッセージのプロパティを選択します。

5. 緑色のチェック マークをクリックして、コネクタの設定を保存します。

5. これで、設計が完了しました。デザイナーの左上の **[コード ビュー]** をクリックしてみましょう。デザイナーで作成したワークフローを定義する JSON コードが確認できます。このコードについては、[次のトピック][Use logic app features]で詳しく説明します。

6. 画面下の **[OK]** ボタンをクリックし、**[作成]** ボタンをクリックします。

	新しいロジック アプリが作成されます。

## 作成後にロジック アプリを管理する

ここまでの手順で、ロジック アプリが実行できるようになりました。スケジュール設定されているワークフローが実行されるたびに、特定のハッシュタグが含まれるツイートがないかどうかが確認されます。条件に合致するツイートが見つかった場合には、Dropbox に保存されます。ここでは、最後にアプリを無効にしたり、動作内容を確認したりする方法を見ていきます。

1. 画面左側の **[参照]** をクリックし、**[Logic Apps]** を選択します。

2. 新たに作成したロジック アプリをクリックすると、現在の状態や全般的な情報を確認できます。

3. 新しいロジック アプリを編集するには、**[トリガーとアクション]** をクリックします。

5. アプリを無効にするには、コマンド バーで **[無効化]** をクリックします。

クラウド上で実行できる簡単なロジック アプリを 5 分もしないうちに作成できました。Logic Apps の機能の使用について詳しくは、「[ロジック アプリの機能を使用する]」をご覧ください。Logic App の定義自体の詳細については、「[Logic App の定義を作成する](app-service-logic-author-definitions.md)」を参照してください。

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Use logic app features]: app-service-logic-use-logic-app-features.md
[ロジック アプリの機能を使用する]: app-service-logic-use-logic-app-features.md

<!---HONumber=September15_HO1-->
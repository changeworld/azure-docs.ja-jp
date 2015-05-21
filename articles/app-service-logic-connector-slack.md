<properties 
	pageTitle="Slack コネクタ"
	description="Slack コネクタの使用"
	authors="anuragdalmia" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/21/2015"
	ms.author="andalmia"/>

# ロジック アプリで Slack コネクタを使用する #

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。

Slack コネクタを使用して、Slack チャネルにメッセージを投稿することができます。

## ロジック アプリ用の Slack コネクタを作成する ##
Slack コネクタを使用するには、まず Slack コネクタ API アプリのインスタンスを作成する必要があります。そのためには、次の手順に従います。

1.	Azure ポータルの左下にある \[+ 新規\] を使用して Azure Marketplace を開きます。
2.	\[Web + モバイル\]、\[Azure Marketplace\] の順に移動して、"Slack コネクタ" を検索します。
3.	次のように Slack コネクタを構成します。
 
	![][1] 
	- **名前** - Slack コネクタの名前を指定します 
	- **App Service プラン** - App Service プランを選択または作成します 
	- **価格レベル** - コネクタの価格レベルを選択します 
	- **リソース グループ** - コネクタを置くリソース グループを選択または作成します 
	- **サブスクリプション** - このコネクタを作成するサブスクリプションを選択します 
	- **場所** - コネクタをデプロイする地理的な場所を選択します

4. \[作成\] をクリックします。新しい Slack コネクタが作成されます。
5. API アプリのインスタンスを作成したら、同じリソース グループに、Slack コネクタを使用するロジック アプリを作成します。

## ロジック アプリで Slack コネクタを使用する ##
API アプリを作成すると、ロジック アプリのアクションとして Slack コネクタを使用できます。そのためには、次の手順を実行する必要があります。

1.	新しいロジック アプリを作成し、Slack コネクタと同じリソース グループを選択します。詳細については、[新しいロジック アプリの作成]に関するページをご覧ください。  	
	
2.	作成したロジック アプリで \[トリガーとアクション\] を選択してロジック アプリ デザイナーを開き、フローを構成します。
	
3.	Slack コネクタは右側のギャラリーの \[このリソース グループの API アプリ\] セクションに表示されます。
 
	![][2]
4.	\[Slack コネクタ\] をクリックして、Slack コネクタの API アプリをエディターにドロップできます。\[承認\] をクリックします。Microsoft の資格情報を入力します \(自動でサインインできない場合\)。次の手順に従って Slack アカウントにサインインします。最後に、Slack アカウントにアクセスする権限をコネクタに付与するように求められます。\[承認\] をクリックします。
 
	![][3]
	![][4]
	![][5]
	![][6]
	
5.	これで、フローで Slack コネクタを使用できるようになりました。現時点では、トリガーは Slack コネクタで使用できません。実行可能な操作は \[メッセージの投稿\] です。
 
	![][7]

6.	\[メッセージの投稿\] の使い方を簡単に見てみましょう。このアクションを使用すると、任意の Slack チャネルにメッセージを投稿することができます。
 
	![][8]

	\[Post Message \(メッセージを投稿\)\] アクションの入力プロパティを次のように構成します。

 - **テキスト** - 投稿するメッセージのテキストを指定します。
 - **チャネル名** - このメッセージをアップロードする Slack チャネルを指定します。これが指定されていない場合、メッセージは \#general で投稿されます。

 	**詳細プロパティ**
 	- **Bot ユーザー名** -このメッセージに使用されるボットの名前です。これが指定されていない場合、メッセージは "Bot" として投稿されます。
 	- **アイコンの URL** - このメッセージのアイコンとして使用されるイメージを示す URL です。
 	- **アイコンの絵文字** - このメッセージのアイコンとして使用される絵文字です。アイコンの URL の上書き
 

7. ロジック アプリの外部のコネクタを使用するには、そのコネクタで公開されている REST API を活用できます。\[参照\]、\[API アプリ\]、\[Slack コネクタ\] の順に選択して、この API 定義を表示できます。\[概要\] セクションの \[API Definition \(API 定義\)\] レンズをクリックすると、このコネクタで公開されているすべての API が表示されます。

	![][9]

9. API の詳細については、[Slack API 定義]に関するページもご覧ください。

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-slack/img1.PNG
[2]: ./media/app-service-logic-connector-slack/img2.PNG
[3]: ./media/app-service-logic-connector-slack/img3.PNG
[4]: ./media/app-service-logic-connector-slack/img4.PNG
[5]: ./media/app-service-logic-connector-slack/img5.PNG
[6]: ./media/app-service-logic-connector-slack/img6.PNG
[7]: ./media/app-service-logic-connector-slack/img7.PNG
[8]: ./media/app-service-logic-connector-slack/img8.PNG
[9]: ./media/app-service-logic-connector-slack/img9.PNG

<!-- Links -->
[新しいロジック アプリの作成]: app-service-logic-create-a-logic-app.md
[Slack API 定義]: https://msdn.microsoft.com/ja-jp/library/dn708020.aspx
<!--HONumber=52-->

<properties 
	pageTitle="Azure App Service での Slack コネクタの使用"
	description="Slack コネクタを使用する方法"
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
	ms.date="06/29/2015"
	ms.author="andalmia"/>

# Slack コネクタ

Slack チャネルに接続し、チームにメッセージを投稿します。コネクタを "ワークフロー" の一部として Logic Apps で使用し、さまざまなタスクを実行できます。ワークフローで Slack コネクタを使用すると、他のコネクタを使用してさまざまなシナリオを実現できます。たとえば、ワークフローで [Facebook コネクタ](app-service-logic-connector-facebook.md)を使用して、Slack チャネルにメッセージを投稿できます。

## トリガーとアクション
*トリガー*とは、発生するイベントを指します。たとえば、注文が更新された、新しい顧客が追加された、といったイベントがあります。*アクション*は、トリガーの結果です。たとえば、注文が更新されたときに、営業担当者にアラートを送信します。または、新しい顧客が追加されたときに、ウェルカム メールを新しい顧客に送信します。

Slack コネクタは、ロジック アプリでアクションとして使用でき、JSON 形式と XML 形式のデータをサポートします。現時点では、Slack コネクタには使用可能なトリガーはありません。

Slack コネクタでは、次のトリガーとアクションを使用できます。

トリガー | アクション
--- | ---
なし | Post Message

## Slack コネクタの作成
コネクタは、ロジック アプリ内で作成することも、Azure Marketplace から直接作成することもできます。Marketplace からコネクタを作成するには、次の操作を実行します。

1. Azure のスタート画面で、**[Marketplace]** を選択します。
2. **[API Apps]** を選択し、"Slack コネクタ" を検索します。
3. 名前、App Service プラン、その他のプロパティを入力します。<br/> ![][1] 

4. **[作成]** をクリックします。

## ロジック アプリのアクションとしてのコネクタの使用

> [AZURE.IMPORTANT]コネクタとロジック アプリは、常に同じリソース グループに作成する必要があります。

Slack コネクタが作成されると、ロジック アプリにアクションとして追加できます。

1.	ロジック アプリで、**[トリガーとアクション]** を開きます。[Create a new Logic App (新しいロジック アプリの作成)](app-service-logic-create-a-logic-app.md)

3.	Slack コネクタは右側のギャラリーの [このリソース グループの API アプリ] セクションに表示されます。

	![][2]
4.	[Slack コネクタ] をクリックして、Slack コネクタの API アプリをエディターにドロップできます。[承認] をクリックします。Microsoft の資格情報を入力します (自動でサインインできない場合)。次の手順に従って Slack アカウントにサインインします。最後に、Slack アカウントにアクセスする権限をコネクタに付与するように求められます。[承認] をクリックします。
	![][3]
	![][4]
	![][5]
	![][6]
	
5.	これで、フローで Slack コネクタを使用できるようになりました。Post Message アクションが使用できます。<br/>。![][7]

	![][7]

[メッセージの投稿] の使い方を簡単に見てみましょう。このアクションを使用すると、任意の Slack チャネルにメッセージを投稿することができます。
 
![][8]

"Post Message" アクションの入力プロパティを次のように構成します。

プロパティ | 説明
--- | ---
テキスト | 投稿するメッセージのテキストを入力します。
チャネル名 | このメッセージが投稿される Slack チャネルを入力します。チャネルが入力されていない場合は、メッセージが #general に投稿されます。
高度なプロパティ | <ul><li><strong>Bot User name</strong>: このメッセージに使用する Bot の名前。これが入力されていない場合、メッセージは "Bot" として投稿されます。</li><li><strong>Icon URL</strong>: このメッセージのアイコンとして使用される画像を示す URL です。</li><li><strong>Icon Emoji</strong>: このメッセージのアイコンとして使用される絵文字です。このプロパティは、アイコンの URL プロパティを上書きします。</li></ul>

Slack コネクタでは REST API が使用できるため、ロジック アプリの外部で Slack コネクタを使用できます。Slack コネクタを開き、**[API の定義]** を選択します。

![][9]


## コネクタでできること
コネクタが作成されたため、Logic App を使用してコネクタをビジネス ワークフローに追加できます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

REST API を使用した API Apps を作成します。[コネクタと API Apps のリファレンス](http://go.microsoft.com/fwlink/p/?LinkId=529766)に関するページを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。[組み込みの API Apps とコネクタの管理と監視](app-service-logic-monitor-your-connectors.md)に関するページを参照してください。


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

<!---HONumber=July15_HO5-->
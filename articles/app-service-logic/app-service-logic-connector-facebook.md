<properties
   pageTitle="Azure App Service のロジック アプリでの Facebook コネクタの使用"
   description="ロジック アプリでの Facebook コネクタの使用方法"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/19/2015"
   ms.author="andalmia"/>


# Facebook コネクタ

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。

- Facebook コネクタ トリガーは、ユーザー タイムラインの新しい投稿またはページの新しい投稿を取得します。新しいツイートが取得されると、フローの新しいインスタンスをトリガーし、要求で受信したデータを処理のためにフローに渡します。
- Facebook コネクタ アクションを使用すると、投稿の発行、写真の発行などの操作を実行できます。これらのアクションによって応答を取得すると、フローの後続のアクションで使用できます。

## トリガーとアクション

トリガー | アクション
--- | ---
<ul><li>ユーザーのタイムラインでの新規投稿</li><li>ページでの新規投稿</li></ul> | <ul><li>投稿の公開</li><li>写真の公開</li></ul>



## ロジック アプリ用の Facebook コネクタを作成する
Facebook コネクタを使用するには、まず Facebook コネクタ API アプリのインスタンスを作成する必要があります。そのためには、次の手順に従います。

1. Azure のスタート画面で、**[Marketplace]** を選択します。
2. "Facebook コネクタ" を検索して選択し、**[作成]** を選択します。
3. 名前、App Service プラン、その他のプロパティを入力します。![][1]
4.	**[作成]** を選択します。


## ロジック アプリでの Facebook コネクタの使用
API アプリを作成すると、ロジック アプリのトリガーやアクションとして Facebook コネクタを使用できます。そのためには、次の手順を実行する必要があります。

1.	ロジック アプリで **[トリガーとアクション]** を選択してロジック アプリ デザイナーを開き、フローを構成します。![][3]
2.	Facebook コネクタがギャラリーの一覧に表示されます。![][4]
3. Facebook コネクタを選択すると、自動的にデザイナーに追加されます。**[承認]** を選択して資格情報を入力し、**[許可]** を選択します。![][5] ![][6] ![][7] ![][8]
4.	トリガーを選択します。![][9]

これで、Facebook トリガーから取得した投稿を他のアクションで使用できるようになりました。次のフローでは、ユーザーの Facebook のタイムラインに新しいメッセージが投稿されるたびに、同じ投稿がユーザーの Twitter のタイムラインにツイートされます。![][10]

同様に、Facebook コネクタのアクションを使用してフローを作成できます。次のフローでは、Yammer グループに投稿された新しいメッセージを取得し、ユーザーによって管理されている Facebook ページに同じ投稿を発行します。![][11]

> [AZURE.TIP]Facebook ページの ID や Yammer グループの ID を取得するには、URL に埋め込まれた数値コードを見つけます。

## コネクタでできること
コネクタが作成されたため、Logic App を使用してコネクタをビジネス ワークフローに追加できます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

「[Connectors and API Apps Reference (コネクタと API Apps のリファレンス)](http://go.microsoft.com/fwlink/p/?LinkId=529766)」で Swagger REST API のリファレンスを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。[組み込みの API Apps とコネクタの管理と監視](app-service-logic-monitor-your-connectors.md)に関するページを参照してください。

<!--Image references-->
[1]: ./media/app-service-logic-connector-facebook/img1.png
[2]: ./media/app-service-logic-connector-facebook/img2.png
[3]: ./media/app-service-logic-connector-facebook/img3.png
[4]: ./media/app-service-logic-connector-facebook/img4.png
[5]: ./media/app-service-logic-connector-facebook/img5.png
[6]: ./media/app-service-logic-connector-facebook/img6.png
[7]: ./media/app-service-logic-connector-facebook/img7.png
[8]: ./media/app-service-logic-connector-facebook/img8.png
[9]: ./media/app-service-logic-connector-facebook/img9.png
[10]: ./media/app-service-logic-connector-facebook/img10.png
[11]: ./media/app-service-logic-connector-facebook/img11.png

<!---HONumber=August15_HO8-->
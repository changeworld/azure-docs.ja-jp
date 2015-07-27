<properties
   pageTitle="Microsoft Azure App Service での Twitter コネクタの使用"
   description="Twitter コネクタ API アプリを使用する方法"
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
   ms.date="07/14/2015"
   ms.author="sameerch"/>


# Twitter コネクタ

Twitter フィードに接続して、ツイートを投稿し、自分のタイムライン、友人のタイムライン、Twitter アカウントのフォロワーからツイートを取得します。コネクタを Logic Apps で使用して、"ワークフロー" の一部としてデータを取得、処理、またはプッシュできます。Twitter コネクタをワークフローで使用すると、さまざまなシナリオを実現できます。たとえば、次のようなことができます。

- 指定したキーワードまたはテキストに関連付けられている新しいツイートを取得します。新しいツイートを取得すると、ワークフローの新しいインスタンスがトリガーされ、ワークフローの次のコネクタにデータが渡されます。たとえば、Twitter コネクタを作成し、#peanutbutterandjelly を監視する New Tweet From Search トリガーを使用します。#peanutbutterandjelly で新しいツイートがある場合は、必ずワークフロー (ロジック アプリともいいます) が自動的に開始されます。
- "Search Tweets" など、さまざまなアクションを使用して、応答を取得し、ワークフロー内で使用します。たとえば、会社名でツイートを検索できます。見つかった場合は、ロジック アプリを使用して、SQL Server データベースにこのデータを書き込むことができます。次に、SQL Server のデータを使用して、会社のどのような話題がツイートされているのかを特定します。 


## トリガーとアクション
*トリガー*とは、発生するイベントを指します。たとえば、新しいツイートは、ワークフローまたはプロセスをトリガー、つまり開始できます。*アクション*とは、何かの操作の結果です。たとえば、特定のツイートを検索して見つかったときに、電子メールを送信したり、データベースを更新したりします。

Twitter コネクタは、ロジック アプリでトリガーまたはアクションとして使用でき、JSON 形式と XML 形式のデータをサポートします。

Twitter コネクタでは、次のトリガーとアクションを使用できます。

トリガー | アクション
--- | ---
New Tweet From Search | <ul><li>Get User Timeline</li><li>Search Tweets</li><li>Tweet</li><li>Get Mentions Timeline</li><li>Get Home Timeline</li><li>Get Followers</li><li>Get Friends</li><li>Get User Details</li><li>Tweet to User</li><li>Send Direct Message</li></ul>

> [AZURE.IMPORTANT]**New Tweet** トリガーはアーカイブされました。現時点では、高度な操作として引き続き使用できます。**Retweet** アクションが削除され、サポートされなくなりました。Retweet アクションを使用すると、実行時に失敗します。よって、ロジック アプリから、Retweet アクションを削除してください。


## Twitter コネクタの作成
コネクタは、ロジック アプリ内で作成することも、Azure Marketplace から直接作成することもできます。Marketplace からコネクタを作成するには、次の操作を実行します。

1. Azure のスタート画面で、**[Marketplace]** を選択します。
2. "Twitter コネクタ" を検索します。
3. 名前、App Service プラン、その他のプロパティを入力します。

	![][1]
4.	**[作成]** をクリックします。


## ロジック アプリで Twitter Connector を使用する
API アプリを作成した後は、ロジック アプリのトリガーまたはアクションとして Twitter コネクタを使用できます。これを行うには、次の手順を実行します。

1.	新しいロジック アプリを作成するか、既存のロジック アプリを開きます。

	![][2]
2.	**[トリガーとアクション]** を開いて、Logic Apps デザイナーを開きます。

	![][3]
3.	Twitter コネクタは、右側に一覧表示されます。コネクタを選択すると、自動的にロジック アプリに追加されます。

	![][4]
4.	**[承認]** を選択し、Twitter の資格情報を入力します。**[アプリの承認]** を選択します。

	![][5]


これで、Twitter コネクタを構成して、ワークフローを構築できます。Twitter トリガーを使って取得したツイートを、フローの他のアクションで使用できます。

![][6]

ワークフローで Twitter アクションを使用する方法もほぼ同じです。Twitter アクションを選択して、対象のアクションの入力を構成してください。

![][7] ![][8]

## コネクタでできること
コネクタが作成されたため、ロジック アプリを使用してコネクタをビジネス ワークフローに追加できます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

REST API を使用した API Apps を作成します。[コネクタと API Apps のリファレンス](http://go.microsoft.com/fwlink/p/?LinkId=529766)に関するページを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。[組み込みの API Apps とコネクタの管理と監視](app-service-logic-monitor-your-connectors.md)に関するページを参照してください。

	<!--Image references-->
[1]: ./media/app-service-logic-connector-twitter/img1.png
[2]: ./media/app-service-logic-connector-twitter/img2.png
[3]: ./media/app-service-logic-connector-twitter/img3.png
[4]: ./media/app-service-logic-connector-twitter/img4.png
[5]: ./media/app-service-logic-connector-twitter/img5.png
[6]: ./media/app-service-logic-connector-twitter/triggers.png
[7]: ./media/app-service-logic-connector-twitter/img7.png
[8]: ./media/app-service-logic-connector-twitter/actions.png

<!---HONumber=July15_HO3-->
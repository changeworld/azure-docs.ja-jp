<properties
   pageTitle="Chatter コネクタの API アプリ"
   description="Chatter コネクタの使用方法"
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
   ms.author="sameerch"/>


# ロジック アプリで Chatter コネクタを使用する

Chatter コネクタを使うと、Chatter に接続し、メッセージの投稿などの一般的なタスクを実行できます。たとえば、Chatter フィードを検索して、特定の項目が見つかったときに、Sales グループにその Chatter メッセージを投稿できます。

この Chatter コネクタをビジネス ワークフローに追加し、そのワークフローの一部としてデータを処理できます。

## トリガーとアクション

トリガーは、新しい Chatter メッセージの着信など、特定のイベントに応じて新しいインスタンスを起動します。アクションは、新しい Chatter メッセージの着信後に、別の Chatter グループまたは別のソーシャル メディア サイト (Facebook や Twitter など) にメッセージを投稿するなどの結果を指します。

Chatter コネクタは、ロジック アプリでトリガーまたはアクションとして使用でき、JSON 形式と XML 形式のデータをサポートします。Chatter コネクタでは、次のトリガーとアクションを使用できます。

トリガー | アクション
--- | ---
新しいメッセージ | <ul><li>メッセージの投稿</li><li>検索</li></ul>


## ロジック アプリ用の Chatter コネクタを作成する
コネクタは、ロジック アプリ内で作成することも、Azure Marketplace から直接作成することもできます。Marketplace からコネクタを作成するには、次の操作を実行します。

1. Azure のスタート画面で、**[Marketplace]** を選択します。
2. "Chatter コネクタ" を検索して選択し、**[作成]** を選択します。
3. 名前、App Service プラン、その他のプロパティを入力します。
	![][1]  
	- **[場所]** - コネクタをデプロイする地域を選択します。
	- **[サブスクリプション]** - コネクタを作成するサブスクリプションを選択します。
	- **[リソース グループ]** - コネクタを格納するリソース グループを選択するか、作成します。
	- **[Web ホスティング プラン]** - Web ホスティング プランを選択するか、作成します。
	- **[価格レベル]** - コネクタの価格レベルを選択します。
	- **[名前]** - Chatter コネクタの名前を指定します。

4. **[作成]** を選択します。


## ロジック アプリで Chatter コネクタを使用する
API アプリが作成されたら、ロジック アプリのトリガーやアクションとして Chatter コネクタを使用できます。これを行うには、次の手順を実行します。

1. ロジック アプリで **[トリガーとアクション]** を選択してロジック アプリ デザイナーを開き、フローを構成します。

2. Chatter コネクタがギャラリーの一覧に表示されます。
	![][4]
3. Chatter コネクタを選択すると、自動的にデザイナーに追加されます。**[承認]** を選択して資格情報を入力し、**[許可]** を選択します。
	![][5]
	![][6]
	![][7]

これで、フローで Chatter コネクタを使用できるようになりました。今後は、Chatter トリガー ([新しいメッセージ]) を使って取得した新しいメッセージを、フローの他のアクションで使用できます。Chatter トリガーの入力プロパティを次のように構成します。

**[グループ ID]** - 新しいメッセージを取得する必要があるグループの ID を入力します。グループ ID を指定しない場合は、新しいメッセージがユーザーのフィードから取得されます。![][8] ![][9]


同様に、[Post Message (メッセージを投稿)] アクションを選択すると、フローの Chatter アクションを使用してメッセージを投稿できます。"Post Message" アクションの入力プロパティを次のように構成します。**[メッセージ テキスト]** - 投稿するメッセージのテキスト コンテンツ。**[グループ ID]** - 新しいメッセージを投稿する必要があるグループの ID を指定します。グループ ID を指定しない場合は、メッセージがユーザーのフィードに投稿されます。**[ファイル名]** - このメッセージに添付されるファイルの名前。**[コンテンツ データ]** - 添付ファイルのコンテンツ データ。**[コンテンツの種類]** - 添付ファイルのコンテンツの種類。**[コンテンツ転送エンコード]** - 添付ファイルのコンテンツ転送エンコード ([なし] または [base64])。**[メンション]** - このメッセージにタグ付けされたユーザー名の配列。**[ハッシュタグ]** - メッセージと共に投稿されるハッシュタグの配列。

![][10]
![][11]

## コネクタでできること
コネクタが作成されたため、Logic App を使用してコネクタをビジネス ワークフローに追加できます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

「[Connectors and API Apps Reference (コネクタと API Apps のリファレンス)](http://go.microsoft.com/fwlink/p/?LinkId=529766)」で Swagger REST API のリファレンスを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。[組み込みの API Apps とコネクタの管理と監視](app-service-logic-monitor-your-connectors.md)に関するページを参照してください。


<!--Image references-->
[1]: ./media/app-service-logic-connector-chatter/img1.PNG
[2]: ./media/app-service-logic-connector-chatter/img2.PNG
[3]: ./media/app-service-logic-connector-chatter/img3.png
[4]: ./media/app-service-logic-connector-chatter/img4.png
[5]: ./media/app-service-logic-connector-chatter/img5.PNG
[6]: ./media/app-service-logic-connector-chatter/img6.PNG
[7]: ./media/app-service-logic-connector-chatter/img7.PNG
[8]: ./media/app-service-logic-connector-chatter/img8.PNG
[9]: ./media/app-service-logic-connector-chatter/img9.PNG
[10]: ./media/app-service-logic-connector-chatter/img10.PNG
[11]: ./media/app-service-logic-connector-chatter/img11.PNG

<!---HONumber=August15_HO8-->
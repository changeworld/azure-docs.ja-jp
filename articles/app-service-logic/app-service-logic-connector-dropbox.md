<properties
	pageTitle="Logic Apps での Dropbox コネクタの使用 | Microsoft Azure App Service"
	description="Dropbox コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法"
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
	ms.date="11/30/2015"
	ms.author="sameerch"/>

# Dropbox コネクタの使用開始とロジック アプリへの追加
Dropbox アカウントに接続して、ファイルをアップロードまたはダウンロードします。ロジック アプリはさまざまなデータ ソースを基にトリガーでき、データの取得と処理のためのコネクタを提供します。この Dropbox コネクタをビジネス ワークフローに追加し、ロジック アプリ内のそのワークフローの一部としてデータを処理できます。

## トリガーとアクション

トリガーは、新しいメッセージの着信など、特定のイベントに応じて新しいインスタンスを起動します。アクションは、新しいメッセージを受け取った後に、ファイルを Dropbox にアップロードするなどの結果を指します。

Dropbox コネクタは、ロジック アプリでトリガーまたはアクションとして使用でき、JSON 形式と XML 形式のデータをサポートします。Dropbox コネクタでは、次のトリガーとアクションを使用できます。

トリガー | アクション
--- | ---
なし | <ul><li>ファイルの削除</li><li>ファイルの取得</li><li>ファイルのアップロード</li><li>ファイルの一覧表示</li></ul>


## ロジック アプリ用の Dropbox コネクタを作成する
コネクタは、ロジック アプリ内で作成することも、Azure Marketplace から直接作成することもできます。Marketplace からコネクタを作成するには、次の操作を実行します。

1. Azure のスタート画面で、**[Marketplace]** を選択します。
2. "Dropbox コネクタ" を検索して選択し、**[作成]** を選択します。
3. 名前、App Service プラン、その他のプロパティを入力します。![][1]
	- **[場所]** - コネクタをデプロイする地域を選択します。
	- **[サブスクリプション]** - コネクタを作成するサブスクリプションを選択します。
	- **[リソース グループ]** - コネクタを格納するリソース グループを選択するか、作成します。
	- **[App Service プラン]** - Web ホスティング プランを選択または作成します。
	- **[価格レベル]** - コネクタの価格レベルを選択します。
	- **[名前]** - Dropbox コネクタの名前を指定します。  
4. **[作成]** を選択します。


## Logic App で Dropbox コネクタを使用する
API アプリが作成されたら、Logic App のアクションとして Dropbox コネクタを使用できます。これを行うには、次の手順を実行します。

1.	ロジック アプリで **[トリガーとアクション]** を選択して Logic Apps デザイナーを開き、フローを構成します。![][3]
2.	Dropbox コネクタがギャラリーの一覧に表示されます。![][4]
3.	Dropbox コネクタを選択すると、自動的にデザイナーに追加されます。**[承認]** を選択して資格情報を入力し、**[許可]** を選択します。![][5] ![][6] ![][7]

これで、フローで Dropbox コネクタを使用できるようになりました。Dropbox アクションの [ファイルのアップロード] を使用して Dropbox アカウントにファイルをアップロードできます。![][8] ![][9]

[ファイルのアップロード] アクションの入力プロパティを次のように構成します。

- **[ファイル パス]** - アップロードするファイルの宛先の Dropbox ファイル パスを指定します。例: Photos/image.png
- **[コンテンツ]** - アップロードするファイルのコンテンツを指定します。多くの場合、これは Logic App の前の手順から取得されます。
- **[コンテンツ転送エンコード]** - [なし] または [base64] を指定します。
- **[上書き]** - ファイルが既に存在する場合に上書きするには、"true" を指定します。

## コネクタでできること
コネクタが作成されたため、ロジック アプリを使用してコネクタをビジネス ワークフローに追加できます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure Logic Apps の使用を開始する場合は、「[Azure App Service アプリケーションの作成](https://tryappservice.azure.com/?appservice=logic)」を参照してください。App Service で有効期間の短いスターター ロジック アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

「[Connectors and API Apps Reference (コネクタと API Apps のリファレンス)](http://go.microsoft.com/fwlink/p/?LinkId=529766)」で Swagger REST API のリファレンスを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。[組み込みの API Apps とコネクタの管理と監視](app-service-logic-monitor-your-connectors.md)に関するページを参照してください。

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-dropbox/img1.PNG
[2]: ./media/app-service-logic-connector-dropbox/img2.PNG
[3]: ./media/app-service-logic-connector-dropbox/img3.png
[4]: ./media/app-service-logic-connector-dropbox/img4.png
[5]: ./media/app-service-logic-connector-dropbox/img5.PNG
[6]: ./media/app-service-logic-connector-dropbox/img6.PNG
[7]: ./media/app-service-logic-connector-dropbox/img7.PNG
[8]: ./media/app-service-logic-connector-dropbox/img8.PNG
[9]: ./media/app-service-logic-connector-dropbox/img9.PNG

<!---HONumber=AcomDC_1203_2015-->
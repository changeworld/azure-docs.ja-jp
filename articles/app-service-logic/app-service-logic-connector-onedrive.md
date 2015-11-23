<properties
	pageTitle="Logic Apps での OneDrive コネクタの使用 | Microsoft Azure App Service"
	description="OneDrive コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法"
	authors="rajeshramabathiran"
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
	ms.date="11/11/2015"
	ms.author="rajram"/>

# OneDrive コネクタの使用開始とロジック アプリへの追加
OneDrive に接続して、ファイルのアップロード、ダウンロード、および削除を実行できます。ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。この OneDrive コネクタをビジネス ワークフローに追加し、ロジック アプリ内のそのワークフローの一部としてデータを処理できます。

## Logic App 用の OneDrive コネクタを作成する ##
OneDrive コネクタを使用するには、まず OneDrive コネクタ API アプリのインスタンスを作成する必要があります。これは、ロジック アプリ デザイナーから直接作成することも、ロジック アプリ デザイナーの外から作成することも実行できます。デザイナーの外からインスタンスを作成する場合の手順は次のとおりです。

1.	Azure ポータルのホーム ページから Azure Marketplace を開きます。
2.	[すべて] の下で "OneDrive コネクタ" を検索します。
3.	次のように、OneDrive コネクタを構成します。

	![][1] - **[名前]** - OneDrive コネクタの名前を指定します。 - **[App Service プラン]** - App Service プランを選択または作成します。 - **[価格レベル]** - コネクタの価格レベルを選択します。 - **[リソース グループ]** - コネクタを格納するリソース グループを選択または作成します。 - **[サブスクリプション]** - このコネクタを作成するサブスクリプションを選択します。 - **[場所]** - コネクタをデプロイする地理的な場所を選択します。

4. [作成] をクリックします。新しい OneDrive コネクタが作成されます。
5. API アプリのインスタンスを作成したら、同じリソース グループに、OneDrive コネクタを使用するロジック アプリを作成します。

## Logic App で OneDrive コネクタを使用する ##
API アプリを作成すると、Logic App のアクションとして OneDrive コネクタを使用できます。そのためには、次の手順を実行する必要があります。

1.	新しい Logic App を作成し、OneDrive コネクタと同じリソース グループを選択します。詳細については、[新しい Logic App の作成]に関するページをご覧ください。

2.	作成した Logic App で [トリガーとアクション] を選択して Logic Apps デザイナーを開き、フローを構成します。

3.	OneDrive コネクタが右側のギャラリーの [API Apps in this resource group (このリソース グループの API Apps)] セクションに表示されます。

	![][2]
4.	[OneDrive コネクタ] をクリックして、OneDrive コネクタの API アプリをエディターにドロップできます。[承認] ボタンをクリックします。Microsoft の資格情報を入力します (自動でサインインできない場合)。[はい] をクリックしてアクセスを許可します。

	![][3] ![][4]

5.	これで、フローで OneDrive コネクタを使用できるようになりました。現時点では、トリガーは OneDrive コネクタで使用できません。アクションは、[Get File (ファイルの取得)]、[Upload a File (ファイルのアップロード)]、[Delete File (ファイルの削除)]、[List Files (ファイルの一覧)] を使用できます。

	![][5]

6.	[ファイルのアップロード] の使い方を簡単に見てみましょう。OneDrive アクションの [ファイルのアップロード] を使用して OneDrive アカウントにファイルをアップロードできます。

	![][6]

	[ファイルのアップロード] アクションの入力プロパティを次のように構成します。

 - **[ファイル パス]** - アップロードするファイルのファイル パスを指定します。
 - **[コンテンツ]** - アップロードするファイルのコンテンツを指定します
 - **[コンテンツ転送エンコード]** - [なし] または [Base64] を指定します。
 - **[上書き]** - ファイルが既に存在する場合に上書きするには、"true" を指定します。これは高度なプロパティです。

7. これらを構成すると、[ファイルのアップロード] アクションが構成され、フローで使用できるようになります。同様に、他のアクションも構成できます。

8. ロジック アプリの外部のコネクタを使用するには、そのコネクタで公開されている REST API を活用できます。[参照]、[API App]、[OneDrive コネクタ] の順に選択して、この API 定義を表示できます。[概要] セクションの [API Definition (API 定義)] レンズをクリックすると、このコネクタで公開されているすべての API が表示されます。

	![][7]

9. API の詳細については、「[OneDrive API の定義]」を参照してください。

## コネクタでできること
コネクタが作成されたため、ロジック アプリを使用してコネクタをビジネス ワークフローに追加できます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure Logic Apps の使用を開始する場合は、「[Azure App Service アプリケーションの作成](https://tryappservice.azure.com/?appservice=logic)」を参照してください。App Service で有効期間の短いスターター ロジック アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

「[Connectors and API Apps Reference (コネクタと API Apps のリファレンス)](http://go.microsoft.com/fwlink/p/?LinkId=529766)」で Swagger REST API のリファレンスを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。[組み込みの API Apps とコネクタの管理と監視](app-service-logic-monitor-your-connectors.md)に関するページを参照してください。

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-onedrive/img1.PNG
[2]: ./media/app-service-logic-connector-onedrive/img2.PNG
[3]: ./media/app-service-logic-connector-onedrive/img3.PNG
[4]: ./media/app-service-logic-connector-onedrive/img4.PNG
[5]: ./media/app-service-logic-connector-onedrive/img5.PNG
[6]: ./media/app-service-logic-connector-onedrive/img6.PNG
[7]: ./media/app-service-logic-connector-onedrive/img7.PNG

<!-- Links -->
[新しい Logic App の作成]: app-service-logic-create-a-logic-app.md
[OneDrive API の定義]: https://msdn.microsoft.com/library/dn974227.aspx

<!---HONumber=Nov15_HO3-->
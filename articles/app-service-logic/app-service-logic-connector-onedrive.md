<properties
	pageTitle="OneDrive コネクタ"
	description="OneDrive コネクタの使用"
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
	ms.date="07/02/2015"
	ms.author="andalmia"/>

# ロジック アプリで OneDrive コネクタを使用する #

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。

OneDrive コネクタを使用すると、OneDrive アカウントからファイルをアップロード、ダウンロード、削除できます。

## Logic App 用の OneDrive コネクタを作成する ##
OneDrive コネクタを使用するには、まず OneDrive コネクタ API アプリのインスタンスを作成する必要があります。これは、ロジック アプリ デザイナーから直接作成することも、ロジック アプリ デザイナーの外から作成することも実行できます。デザイナーの外からインスタンスを作成する場合の手順は次のとおりです。

1.	Azure ポータルのホーム ページから Azure Marketplace を開きます。
2.	[すべて] の下で "OneDrive コネクタ" を検索します。
3.	次のように、OneDrive コネクタを構成します。

	![][1]
	- **[名前]** - OneDrive コネクタの名前を指定します。
	- **[App Service プラン]** - App Service プランを選択または作成します。
	- **[価格レベル]** - コネクタの価格レベルを選択します。
	- **[リソース グループ]** - コネクタを格納するリソース グループを選択または作成します。
	- **[サブスクリプション]** - このコネクタを作成するサブスクリプションを選択します。
	- **[場所]** - コネクタをデプロイする地理的な場所を選択します。

4. [作成] をクリックします。新しい OneDrive コネクタが作成されます。
5. API アプリのインスタンスを作成したら、同じリソース グループに、OneDrive コネクタを使用するロジック アプリを作成します。

## Logic App で OneDrive コネクタを使用する ##
API アプリを作成すると、Logic App のアクションとして OneDrive コネクタを使用できます。そのためには、次の手順を実行する必要があります。

1.	新しい Logic App を作成し、OneDrive コネクタと同じリソース グループを選択します。詳細については、[新しい Logic App の作成]に関するページをご覧ください。

2.	作成した Logic App で [トリガーとアクション] を選択して Logic Apps デザイナーを開き、フローを構成します。

3.	OneDrive コネクタが右側のギャラリーの [API Apps in this resource group (このリソース グループの API Apps)] セクションに表示されます。

	![][2]
4.	[OneDrive コネクタ] をクリックして、OneDrive コネクタの API アプリをエディターにドロップできます。[承認] ボタンをクリックします。Microsoft の資格情報を入力します (自動でサインインできない場合)。[はい] をクリックしてアクセスを許可します。

	![][3]
	![][4]

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
[OneDrive API の定義]: https://msdn.microsoft.com/ja-jp/library/dn974227.aspx

<!---HONumber=July15_HO4-->
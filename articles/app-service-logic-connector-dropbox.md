<properties 
	pageTitle="Dropbox コネクタ"
	description="Dropbox コネクタの使用"
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
	ms.date="03/31/2015"
	ms.author="adgoda"/>

# ロジック アプリで Dropbox コネクタを使用する #

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。 

Dropbox コネクタを使用すると、Dropbox アカウントからファイルをアップロードまたはダウンロードできます。

## ロジック アプリ用の Dropbox コネクタを作成する ##
Dropbox コネクタを使用するには、まず Dropbox コネクタ API アプリのインスタンスを作成する必要があります。そのためには、次の手順に従います。

1.	Azure ポータルの左下にある [+ 新規] を使用して Azure Marketplace を開きます。
2.	[Web + モバイル]、[API アプリ] の順に移動して、"Dropbox コネクタ" を検索します。
3.	次のように、Dropbox コネクタを構成します。
 
	![][1] 
	- **[場所]** - コネクタをデプロイする地域を選択します。
	- **[サブスクリプション]** - コネクタを作成するサブスクリプションを選択します。
	- **[リソース グループ]** - コネクタを格納するリソース グループを選択するか、作成します。
	- **[Web ホスティング プラン]** - Web ホスティング プランを選択するか、作成します。
	- **[価格レベル]** - コネクタの価格レベルを選択します。
	- **[名前]** - Dropbox コネクタの名前を指定します。
4. [作成] をクリックします。新しい Dropbox コネクタが作成されます。
5. API アプリのインスタンスを作成したら、同じリソース グループに、Dropbox コネクタを使用するロジック アプリを作成します。

## ロジック アプリで Dropbox コネクタを使用する ##
API アプリを作成すると、ロジック アプリのアクションとして Dropbox コネクタを使用できます。そのためには、次の手順を実行する必要があります。

1.	新しいロジック アプリを作成し、Dropbox コネクタと同じリソース グループを選択します。
 	
	![][2]
2.	[トリガーとアクション] を選択してロジック アプリ デザイナーを開き、フローを構成します。 
 	
	![][3]
3.	Dropbox コネクタが右側のギャラリーの [API Apps in this resource group (このリソース グループの API アプリ)] セクションに表示されます。
 
	![][4]
4.	[Dropbox コネクタ] をクリックして、Dropbox コネクタの API アプリをエディターにドロップできます。[承認] をクリックします。Dropbox 資格情報を入力します。[許可する] をクリックします。
 
	![][5]
	![][6]
	![][7]
6.	これで、フローで Dropbox コネクタを使用できるようになりました。Dropbox アクションの [ファイルのアップロード] を使用して Dropbox アカウントにファイルをアップロードできます。
 
	![][8]
	![][9]

[ファイルのアップロード] アクションの入力プロパティを次のように構成します。

- **[ファイル パス]** - アップロードするファイルのファイル パスを指定します。
- **[コンテンツ]** - アップロードするファイルのコンテンツを指定します。
- **[コンテンツ転送エンコード]** - [なし] または [base64] を指定します。
- **[上書き]** - ファイルが既に存在する場合は、"true"を 指定して上書きします。


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


<!--HONumber=52-->
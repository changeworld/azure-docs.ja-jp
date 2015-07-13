<properties 
   pageTitle="Yammer コネクタの API App" 
   description="Yammer コネクタの使用方法" 
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
   ms.date="03/31/2015"
   ms.author="adgoda"/>


# ロジック アプリで Yammer コネクタを使用する #

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。

Yammer コネクタを使用すると、Yammer に接続し、メッセージの投稿アクションと新しいメッセージを取得するためのトリガーを実行できます。

## Logic App 用の Yammer コネクタを作成する ##
Yammer コネクタを使用するには、まず Yammer コネクタ API アプリのインスタンスを作成する必要があります。そのためには、次の手順に従います。

1.	Azure ポータルの左下にある [+ 新規] を使用して Azure Marketplace を開きます。
2.	[Web + Mobile]、[API Apps] の順に移動して、"Yammer コネクタ" を検索します。
3.	次のように、Yammer コネクタを構成します。
 
 ![][1]
 
	- **Location** - choose the geographic location where you would like the connector to be deployed
	- **Subscription** - choose a subscription you want this connector to be created in
	- **Resource group** - select or create a resource group where the connector should reside
	- **App Service plan** - select or create a web hosting plan
	- **Pricing tier** - choose a pricing tier for the connector
	- **Name** - give a name for your Yammer Connector 

4.	[作成] をクリックします。新しい Yammer コネクタが作成されます。
5.	API アプリのインスタンスを作成したら、同じリソース グループに、Yammer コネクタを使用するロジック アプリを作成します。 

## Logic App で Yammer コネクタを使用する ##
API アプリを作成すると、Logic App のトリガーやアクションとして Yammer コネクタを使用できます。そのためには、次の手順を実行する必要があります。

1.	新しい Logic App を作成し、Yammer コネクタと同じリソース グループを選択します。
 
![][2]
 
2.	[トリガーとアクション] を選択してロジック アプリ デザイナーを開き、フローを構成します。 
 
![][3]
 
3.	Yammer コネクタが右側のギャラリーの [API Apps in this resource group (このリソース グループの API Apps)] セクションに表示されます。
 
![][4]
 
4. [Yammer コネクタ] をクリックして、Yammer コネクタの API アプリをエディターにドロップできます。[承認] ボタンをクリックします。Yammer 資格情報を入力します。[許可する] をクリックします。

![][5]
 
![][6]
 
![][7]
 
これで、フローで Yammer コネクタを使用できるようになりました。

## Yammer コネクタをトリガーとして使用する

1.	 今後は、Yammer トリガー ([新しいメッセージ]) を使って取得した新しいメッセージを、フローの他のアクションで使用できます。Yammer トリガーの入力プロパティを次のように構成します。

	- **グループ ID** - 新しいメッセージを取得する必要があるグループの ID。グループ ID を指定しない場合は、メッセージが次のフィードから取得されます。グループ ID は、Yammer のグループ URL から取得できます。例: 次の URL のグループ ID は "5453203" です https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203

	![][8]
 
	![][9]

## Yammer コネクタを使用してメッセージを投稿する

6.	Yammer コネクタは、ロジック アプリ内でアクションとして使用することもできます。最初に、ロジック アプリのトリガーを指定するか、[このロジックを手動で実行］ をオンにします (下記参照)。Yammer コネクタを追加し、必要に応じて承認し、[メッセージの投稿] アクションを選択します。[Post Message (メッセージを投稿)] アクションの入力プロパティを次のように構成します。

	- **[メッセージ テキスト]** - 投稿するメッセージのテキスト コンテンツ。
	- **グループ ID** - 新しいメッセージを投稿する必要があるグループの ID を指定します。グループ ID を指定しない場合は、メッセージがすべての会社フィードに投稿されます。グループ ID は、Yammer のグループ URL から取得できます。例: 次の URL のグループ ID は "5453203" です https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
	- 	**タグ ユーザー** - メッセージにタグ付けする必要があるユーザー ネットワーク名の配列。 

	![][10]
 
	![][11]

	<!--Image references-->
[1]: ./media/app-service-logic-connector-yammer/img1.PNG
[2]: ./media/app-service-logic-connector-yammer/img2.PNG
[3]: ./media/app-service-logic-connector-yammer/img3.png
[4]: ./media/app-service-logic-connector-yammer/img4.png
[5]: ./media/app-service-logic-connector-yammer/img5.PNG
[6]: ./media/app-service-logic-connector-yammer/img6.PNG
[7]: ./media/app-service-logic-connector-yammer/img7.png
[8]: ./media/app-service-logic-connector-yammer/img8.PNG
[9]: ./media/app-service-logic-connector-yammer/img9.PNG
[10]: ./media/app-service-logic-connector-yammer/img10.PNG
[11]: ./media/app-service-logic-connector-yammer/img11.PNG

<!---HONumber=62-->
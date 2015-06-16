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
   ms.date="03/31/2015"
   ms.author="adgoda"/>


# ロジック アプリで Chatter コネクタを使用する #

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。 

Chatter コネクタを使用すると、Chatter に接続し、メッセージの投稿、新しいメッセージを取得するための検索、トリガーなど、さまざまな操作を実行できます。

## ロジック アプリ用の Chatter コネクタを作成する ##
Chatter コネクタを使用するには、まず Chatter コネクタ API アプリのインスタンスを作成する必要があります。そのためには、次の手順に従います。

1.	Azure ポータルの左下にある [+ 新規] を使用して Azure Marketplace を開きます。
2.	[Web + モバイル]、[API アプリ] の順に移動して、"Chatter コネクタ" を検索します。
3.	次のように、Chatter コネクタを構成します。
 
	![][1]
	- **[場所]** - コネクタをデプロイする地域を選択します。
	- **[サブスクリプション]** - コネクタを作成するサブスクリプションを選択します。
	- **[リソース グループ]** - コネクタを格納するリソース グループを選択するか、作成します。
	- **[Web ホスティング プラン]** - Web ホスティング プランを選択するか、作成します。
	- **[価格レベル]** - コネクタの価格レベルを選択します。
	- **[名前]** - Chatter コネクタの名前を指定します。 

4.	[作成] をクリックします。新しい Chatter コネクタが作成されます。
5.	API アプリのインスタンスを作成したら、同じリソース グループに、Chatter コネクタを使用するロジック アプリを作成します。 

## ロジック アプリで Chatter コネクタを使用する ##
API アプリを作成すると、ロジック アプリのトリガーやアクションとして Chatter コネクタを使用できます。そのためには、次の手順を実行する必要があります。

1.	新しいロジック アプリを作成し、Chatter コネクタと同じリソース グループを選択します。
 
	![][2]
2.	[トリガーとアクション] を選択してロジック アプリ デザイナーを開き、フローを構成します。 
 
	![][3]
3.	Chatter コネクタが右側のギャラリーの [API Apps in this resource group (このリソース グループの API アプリ)] セクションに表示されます。
 
	![][4]
4. [Chatter コネクタ] をクリックして、Chatter コネクタの API アプリをエディターにドロップできます。[承認] をクリックします。資格情報を入力します。[許可する] をクリックします。
 
	![][5]
	![][6]
	![][7]
5.	これで、フローで Chatter コネクタを使用できるようになりました。今後は、Chatter トリガー ([新しいメッセージ]) を使って取得した新しいメッセージを、フローの他のアクションで使用できます。Chatter トリガーの入力プロパティを次のように構成します。
	- **[グループ ID]** - 新しいメッセージを取得する必要があるグループの ID を指定します。グループ ID を指定しない場合は、新しいメッセージがユーザーのフィードから取得されます。 

 
	![][8]
	![][9]


6. 同様に、[Post Message (メッセージを投稿)] アクションを選択すると、フローの Chatter アクションを使用してメッセージを投稿できます。[Post Message (メッセージを投稿)] アクションの入力プロパティを次のように構成します。
	- **[メッセージ テキスト]** - 投稿するメッセージのテキスト コンテンツ
	- **[グループ ID]** - 新しいメッセージを投稿する必要があるグループの ID を指定します。グループ ID を指定しない場合は、メッセージがユーザーのフィードに投稿されます。
	- 	**[ファイル名]** - このメッセージに添付されるファイルの名前
	- 	**[コンテンツ データ]** - 添付ファイルのコンテンツ データ
	- 	**[コンテンツの種類]** - 添付ファイルのコンテンツの種類
	- 	**[コンテンツ転送エンコード]** - 添付ファイルのコンテンツ転送エンコード ([なし] または [base64])
	- 	**[メンション]** - このメッセージにタグ付けされたユーザー名の配列
	- 	**[ハッシュタグ]** - メッセージと共に投稿されるハッシュタグの配列

	![][10]
	![][11] 




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


<!--HONumber=52--> 
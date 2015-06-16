<properties 
   pageTitle="POP3 コネクタの API アプリ" 
   description="POP3 コネクタの使用方法" 
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


# ロジック アプリで POP3 コネクタを使用する #

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。 

POP3 コネクタを使用すると、POP3 サーバーに接続できます。このコネクタには、添付ファイル付きのメールを取得するためのトリガーが含まれています。

## ロジック アプリ用の POP3 コネクタを作成する ##
POP3 コネクタを使用するには、まず POP3 コネクタ API アプリのインスタンスを作成する必要があります。そのためには、次の手順に従います。

1.	Azure ポータルの左下にある [+ 新規] を使用して Azure Marketplace を開きます。
2.	[Web + モバイル]、[API アプリ] の順に移動して、"POP3 コネクタ" を検索します。
3.	次のように、POP3 コネクタを構成します。
 
	![][1]
	- **[場所]** - コネクタをデプロイする地域を選択します。
	- **[サブスクリプション]** - コネクタを作成するサブスクリプションを選択します。
	- **[リソース グループ]** - コネクタを格納するリソース グループを選択するか、作成します。
	- **[Web ホスティング プラン]** - Web ホスティング プランを選択するか、作成します。
	- **[価格レベル]** - コネクタの価格レベルを選択します。
	- **[名前]** - POP3 コネクタの名前を指定します
	- **パッケージ設定**
		- **[ユーザー名]** - POP3 サーバーへの接続に使用するユーザー名を指定します。
		- **[パスワード]** - POP3 サーバーへの接続に使用するパスワードを指定します。
		- **[サーバー アドレス]** - POP3 サーバーの名前または IP アドレスを指定します。
		- **[サーバー ポート]** - POP3 サーバーのポート番号を指定します。
		- **[SSL を有効にする]** - セキュリティで保護された SSL/TLS チャネルで POP3 を使用する場合は true を指定します。
4.	[作成] をクリックします。新しい POP3 コネクタが作成されます。
5.	API アプリのインスタンスを作成したら、同じリソース グループに、POP3 コネクタを使用するロジック アプリを作成します。 

## ロジック アプリで POP3 コネクタを使用する ##
API アプリを作成すると、ロジック アプリのトリガーとして POP3 コネクタを使用できます。そのためには、次の手順を実行する必要があります。

1.	新しいロジック アプリを作成し、POP3 コネクタと同じリソース グループを選択します。
 
	![][2]
2.	[トリガーとアクション] を選択してロジック アプリ デザイナーを開き、フローを構成します。 
 
	![][3]
3.	POP3 コネクタが右側のギャラリーの [API Apps in this resource group (このリソース グループの API アプリ)] セクションに表示されます。それを選択します。
 
	![][4]
4.	[POP3 コネクタ] をクリックして、POP3 コネクタの API アプリをエディターにドロップできます。 
	
5.	これで、フローで POP3 コネクタを使用できるようになりました。[Get Email (メールの取得)] を選択し、頻度と間隔を構成します。今後は、POP3 トリガーを使って取得したメールを、フローの他のアクションで使用できます。
		 
	
	![][5]
	![][6]


	<!--Image references-->
[1]: ./media/app-service-logic-connector-pop3/img1.PNG
[2]: ./media/app-service-logic-connector-pop3/img2.PNG
[3]: ./media/app-service-logic-connector-pop3/img3.png
[4]: ./media/app-service-logic-connector-pop3/img4.PNG
[5]: ./media/app-service-logic-connector-pop3/img5.PNG
[6]: ./media/app-service-logic-connector-pop3/img6.PNG


<!--HONumber=52--> 
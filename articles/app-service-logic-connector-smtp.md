<properties 
   pageTitle="SMTP コネクタの API アプリ" 
   description="SMTP コネクタの使用方法" 
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


# ロジック アプリで SMTP コネクタを使用する #

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。 

SMTP コネクタを使用すると、SMTP サーバーに接続し、添付ファイル付きのメールを送信するアクションを実行できます。

- SMTP コネクタの [電子メールの送信] アクションを使用すると、指定されたメール アドレスにメールを送信できます。 

## ロジック アプリ用の SMTP コネクタを作成する ##
SMTP コネクタを使用するには、まず SMTP コネクタ API アプリのインスタンスを作成する必要があります。そのためには、次の手順に従います。

1.	Azure ポータルの左下にある [+ 新規] を使用して Azure Marketplace を開きます。
2.	[Web + モバイル]、[API アプリ] の順に移動して、"SMTP コネクタ" を検索します。
3.	次のように、SMTP コネクタを構成します。
 
	![][1]
	- **[場所]** - コネクタをデプロイする地域を選択します。
	- **[サブスクリプション]** - コネクタを作成するサブスクリプションを選択します。
	- **[リソース グループ]** - コネクタを格納するリソース グループを選択するか、作成します。
	- **[Web ホスティング プラン]** - Web ホスティング プランを選択するか、作成します。
	- **[価格レベル]** - コネクタの価格レベルを選択します。
	- **[名前]** - SMTP コネクタの名前を指定します。
	- **パッケージ設定**
		- **[ユーザー名]** - SMTP サーバーへの接続に使用するユーザー名を指定します。
		- **[パスワード]** - SMTP サーバーへの接続に使用するパスワードを指定します。
		- **[サーバー アドレス]** - SMTP サーバーの名前または IP アドレスを指定します。
		- **[サーバー ポート]** - SMTP サーバーのポート番号を指定します。
		- **[SSL を有効にする]** - セキュリティで保護された SSL/TLS チャネルで SMTP を使用する場合は true を指定します。
4.	[作成] をクリックします。新しい SMTP コネクタが作成されます。
5.	API アプリのインスタンスを作成したら、同じリソース グループに、SMTP コネクタを使用するロジック アプリを作成します。 

## ロジック アプリで SMTP コネクタを使用する ##
API アプリを作成すると、ロジック アプリのアクションとして SMTP コネクタを使用できます。そのためには、次の手順を実行する必要があります。

1.	新しいロジック アプリを作成し、SMTP コネクタと同じリソース グループを選択します。
 
	![][2]
2.	[トリガーとアクション] を選択してロジック アプリ デザイナーを開き、フローを構成します。 
 
	![][3]
3.	SMTP コネクタが右側のギャラリーの [API Apps in this resource group (このリソース グループの API アプリ)] セクションに表示されます。それを選択します。
 
	![][4]
4.	[SMTP コネクタ] をクリックして、SMTP コネクタの API アプリをエディターにドロップできます。 
	
7.	これで、フローで SMTP コネクタを使用できるようになりました。[電子メールの送信] アクションを選択し、入力プロパティを次のように構成します。
	- **[宛先]** - これは、受信者のメール アドレスです。セミコロン (;) を使用して複数のメール アドレスを区切ります。次に例を示します。 recipient1@domain.com;recipient2@domain.com.
	- **[Cc]** - これは、カーボン コピーの受信者のメール アドレスです。セミコロン (;) を使用して複数のメール アドレスを区切ります。次に例を示します。 recipient1@domain.com;recipient2@domain.com.
	- **[件名]** - これは、メールの件名です。
	- **[本文]** - これは、メールの本文です。
	- **[Is HTML]** - このプロパティを true に設定すると、本文のコンテンツが html 形式で送信されます。
	- **[Bcc]** - これは、ブラインド カーボン コピーの受信者のメール アドレスです。セミコロン (;) を使用して複数のメール アドレスを区切ります。次に例を示します。 recipient1@domain.com;recipient2@domain.com.
	- **[重要度]** - これは、メールの重要度です。オプションは、[標準]、[低]、[高] です。
	- **[添付ファイル]** - メールと共に送信される添付ファイル。これには、次のフィールドが含まれます。
		- コンテンツ (文字列)
		- コンテンツ転送エンコード (列挙型) ("なし"|"base64")
		- コンテンツの種類 (文字列)
		- コンテンツ ID (文字列)
		- ファイル名 (文字列)
	 
	
	![][5] 
	![][6]


	<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG


<!--HONumber=52-->
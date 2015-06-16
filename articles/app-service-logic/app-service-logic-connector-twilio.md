<properties 
   pageTitle="Twilio コネクタの API アプリ" 
   description="Twilio コネクタの使用方法" 
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


# ロジック アプリで Twilio コネクタを使用する #

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。 

Twilio コネクタを使用すると、Twilio アカウントから SMS を送受信できます。また、電話番号や使用状況データを取得することもできます。

## ロジック アプリ用の Twilio コネクタを作成する ##
Twilio コネクタを使用するには、まず Twilio コネクタ API アプリのインスタンスを作成する必要があります。そのためには、次の手順に従います。

1.	Azure ポータルの左下にある [+ 新規] を使用して Azure Marketplace を開きます。
2.	[Web + モバイル]、[API アプリ] の順に移動して、"Twilio コネクタ" を検索します。
3.	次のように、Twilio コネクタを構成します。
 
	![][1]
	- **[場所]** - コネクタをデプロイする地理的な場所を選択します。
	- **[サブスクリプション]** - コネクタを作成するサブスクリプションを選択します。
	- **[リソース グループ]** - ネクタを格納するリソース グループを選択するか、作成します。
	- **[Web ホスティング プラン]** - Web ホスティング プランを選択するか、作成します。
	- **[価格レベル]** - コネクタの価格レベルを選択します。
	- **[名前]** - Twilio コネクタの名前を指定します。
	- **[パッケージ設定]**
		- **[アカウント SID]** - アカウントの一意識別子。自分のアカウントのアカウントの SIDは、https://www.twilio.com/user/account/settings から取得できます。 
		- **[認証トークン]** - アカウントに関連付けられている認証トークン。自分のアカウントの認証トークンは、https://www.twilio.com/user/account/settings から取得できます。


4.	[作成] をクリックします。新しい Twilio コネクタが作成されます。
5.	API アプリのインスタンスを作成したら、同じリソース グループに、Twilio コネクタを使用するロジック アプリを作成します。 

## ロジック アプリで Twilio コネクタを使用する ##
API アプリを作成すると、ロジック アプリのアクションとして Twilio コネクタを使用できます。そのためには、次の手順を実行する必要があります。

1.	新しいロジック アプリを作成し、Twilio コネクタと同じリソース グループを選択します。
 
	![][2]
2.	[トリガーとアクション] を選択してロジック アプリ デザイナーを開き、フローを構成します。 
 
	![][3]
3.	Twilio コネクタが右側のギャラリーの [API Apps in this resource group (このリソース グループの API アプリ)] セクションに表示されます。
 
	![][4]
4. [Twilio コネクタ] をクリックして、Twilio コネクタの API アプリをエディターにドロップできます。
 
5.	これで、フローで Twilio コネクタを使用できるようになりました。フローで [メッセージの送信] アクションを使用すると、メッセージを送信できます。[メッセージを送信] アクションの入力プロパティを次のように構成します。
	- **[From Phone Number (通話元の電話番号)]** - 送信するメッセージの種類が有効になっている Twilio の電話番号を入力します。Twilio から購入した電話番号または短いコードのみがこのコネクタで機能します。
	- **[To Phone Number (通話先の電話番号)]** - 通話先の電話番号。使用できる形式は、+ の後に国コード、その次に電話番号を続けます。たとえば、+16175551212 です。+ を省略した場合、Twilio は  '通話元' の電話番号に入力した国コードを使用します。
	- **[テキスト]** - 送信するメッセージのテキスト。
 
	![][5]
	![][6] 



	<!--Image references-->
[1]: ./media/app-service-logic-connector-twilio/img1.PNG
[2]: ./media/app-service-logic-connector-twilio/img2.PNG
[3]: ./media/app-service-logic-connector-twilio/img3.png
[4]: ./media/app-service-logic-connector-twilio/img4.png
[5]: ./media/app-service-logic-connector-twilio/img5.PNG
[6]: ./media/app-service-logic-connector-twilio/img6.PNG


<!--HONumber=52--> 
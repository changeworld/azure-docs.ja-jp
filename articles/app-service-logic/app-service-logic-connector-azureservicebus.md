<properties 
   pageTitle="Azure Service Bus コネクタの API アプリ" 
   description="AzureServiceBus コネクタの使用方法" 
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


# ロジック アプリで Azure Service Bus コネクタを使用する #

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。 

Azure Service Bus コネクタを使用すると、キュー、トピック、サブスクリプションとメッセージを送受信できます。

## ロジック アプリ用の Azure Service Bus コネクタを作成する ##
Azure Service Bus コネクタを使用するには、まず Azure Service Bus コネクタ API アプリのインスタンスを作成する必要があります。そのためには、次の手順に従います。

1.	Azure ポータルの左下にある [+ 新規] を使用して Azure Marketplace を開きます。
2.	[Web + モバイル]、[API アプリ] の順に移動して、"Azure Service Bus コネクタ" を検索します。
3.	次のように、Azure Service Bus コネクタを構成します。
 
	![][1]
	- **[場所]** - コネクタをデプロイする地域を選択します。
	- **[サブスクリプション]** - コネクタを作成するサブスクリプションを選択します。
	- **[リソース グループ]** - コネクタを格納するリソース グループを選択するか、作成します。
	- **[Web ホスティング プラン]** - Web ホスティング プランを選択するか、作成します。
	- **[価格レベル]** - コネクタの価格レベルを選択します。
	- **[名前]** - Azure Service Bus コネクタの名前を指定します。
	- **パッケージ設定**
		**[接続文字列]** - Azure Service Bus の接続文字列。例:エンドポイント=sb://[名前空間].servicebus.windows.net;SharedAccessKeyName=[名前];SharedAccessKey=[キー]
		- **エンティティ名** - キューまたはトピックの名前
		- **サブスクリプション名** - メッセージを受信するサブスクリプションの名前。 

4.	[作成] をクリックします。新しい Azure Service Bus コネクタが作成されます。
5.	API アプリのインスタンスを作成したら、同じリソース グループに、Azure Service Bus コネクタを使用するロジック アプリを作成します。 

## ロジック アプリで Azure Service Bus コネクタを使用する ##
API アプリを作成すると、ロジック アプリのトリガーやアクションとして Azure Service Bus コネクタを使用できます。そのためには、次の手順を実行する必要があります。

1.	新しいロジック アプリを作成し、Azure Service Bus コネクタと同じリソース グループを選択します。
 
	![][2]
2.	[トリガーとアクション] を選択してロジック アプリ デザイナーを開き、フローを構成します。 
 
	![][3]
3.	Azure Service Bus コネクタが右側のギャラリーの [API Apps in this resource group (このリソース グループの API アプリ)] セクションに表示されます。
 
	![][4]
4. [Azure Service Bus Connector (Azure Service Bus コネクタ)] をクリックして、Azure Service Bus コネクタの API アプリをエディターにドロップできます。
 
5.	これで、フローで Azure Service Bus コネクタを使用できるようになりました。今後は、Azure Service Bus トリガー ([Message Available (メッセージあり)]) を使って取得したメッセージを、フローの他のアクションで使用できます。
 
	![][5]
	![][6] 
6.	同様に、Azure Service Bus アクション [メッセージの送信] を使用してメッセージを送信できます。

	![][7]
	![][8]


	<!--Image references-->
[1]: ./media/app-service-logic-connector-azureservicebus/img1.PNG
[2]: ./media/app-service-logic-connector-azureservicebus/img2.PNG
[3]: ./media/app-service-logic-connector-azureservicebus/img3.png
[4]: ./media/app-service-logic-connector-azureservicebus/img4.PNG
[5]: ./media/app-service-logic-connector-azureservicebus/img5.PNG
[6]: ./media/app-service-logic-connector-azureservicebus/img6.PNG
[7]: ./media/app-service-logic-connector-azureservicebus/img7.PNG
[8]: ./media/app-service-logic-connector-azureservicebus/img8.PNG


<!--HONumber=52--> 
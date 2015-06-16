<properties 
   pageTitle="Twitter Connector API アプリ" 
   description="TwitterConnector の使い方" 
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
   ms.date="03/20/2015"
   ms.author="adgoda"/>


# ロジック アプリで Twitter Connector を使用する #

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。

Twitter Connector を使えば、自分の Twitter アカウントからツイートを投稿したり、自分のタイムライン、友人、フォロワーのツイートを取得したりできます。

- Twitter Connector トリガーは、特定のキーワードが関連付けられた新しいツイートを取得するものです。新しいツイートが取得されると、フローの新しいインスタンスをトリガーし、要求で受信したデータを処理のためにフローに渡します。 
- Twitter Connector アクションは、"ツイート"、"ツイートの検索"、"リツイート"、"ユーザーのタイムラインの取得" などを可能にするものです。これらのアクションによって応答を取得すると、フローの後続のアクションで使用できます。

## ロジック アプリのための Twitter Connector を作成する ##
Twitter Connector を使用するにはまず、Twitter Connector API アプリのインスタンスを作成する必要があります。そのためには、次の手順に従います。

1. Azure ポータルの左下にある [+ 新規] を使用して Azure Marketplace を開きます。
1. [API Apps] に移動して、"Twitter Connector" を検索します。
1. Twitter Connector を以下のように構成します。

	![][1]
4.	[OK] をクリックして作成します。
5.	API アプリのインスタンスを作成したら、同じリソース グループに、Twitter Connector を使用するロジック アプリを作成します。 
	- Twitter Connector API アプリのインスタンスは、ロジック アプリから作成することもできます。 
	- ロジック アプリ エディターを開き、右側に表示されているギャラリーから、Twitter Connector をクリックします。
	- このようにすると、ロジック アプリと同じリソース グループに Twitter Connector API アプリのインスタンスが作成されます。


## ロジック アプリで Twitter Connector を使用する ##
API アプリを作成した後は、ロジック アプリのトリガーおよびアクションとして Twitter Connector を使用できます。そのためには、次の手順を実行する必要があります。

1.	ロジック アプリを作成し、Twitter Connector と同じリソース グループを選択します。
 	
	![][2]
2.	[トリガーとアクション] を選択してロジック アプリ デザイナーを開き、フローを構成します。 
 	
	![][3]
3.	右側のギャラリーの [最近使用した項目] セクションに、Twitter Connector が表示されます。それを選択します。
 
	![][4]
4.	右側に表示されているギャラリーの [最近使用した項目] セクションで [Twitter Connector] をクリックして、エディターに Twitter Connector API アプリをドロップします。[承認] をクリックします。Twitter の資格情報を入力します。[アプリを認証] をクリックします。
 
	![][5]
6.	これで、フローに Twitter Connector を使用できるようになりました。今後は、Twitter トリガーを使って取得したツイートを、フローの他のアクションで使用できます。
 
	![][6]
7.	フローで Twitter アクションを使用する方法もほぼ同じです。Twitter アクションを選択して、それぞれのアクションの入力を構成してください。

	![][7] 
	![][8]

	<!--Image references-->
[1]: ./media/app-service-logic-connector-twitter/img1.png
[2]: ./media/app-service-logic-connector-twitter/img2.png
[3]: ./media/app-service-logic-connector-twitter/img3.png
[4]: ./media/app-service-logic-connector-twitter/img4.png
[5]: ./media/app-service-logic-connector-twitter/img5.png
[6]: ./media/app-service-logic-connector-twitter/img6.png
[7]: ./media/app-service-logic-connector-twitter/img7.png
[8]: ./media/app-service-logic-connector-twitter/img8.png

<!---HONumber=54--> 
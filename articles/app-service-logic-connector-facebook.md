<properties 
   pageTitle="Facebook コネクタ API アプリ" 
   description="Facebook コネクタの使用方法" 
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


# ロジック アプリでの Facebook コネクタの使用 #

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。

Facebook コネクタを使用すると、ユーザー タイムラインの新しい投稿の取得、ページの新しい投稿の取得、投稿の発行、写真の発行などの操作を Facebook アカウントから実行できます。

- Facebook コネクタ トリガーは、ユーザー タイムラインの新しい投稿またはページの新しい投稿を取得します。新しいツイートが取得されると、フローの新しいインスタンスをトリガーし、要求で受信したデータを処理のためにフローに渡します。 
- Facebook コネクタ アクションを使用すると、投稿の発行、写真の発行などの操作を実行できます。これらのアクションによって応答を取得すると、フローの後続のアクションで使用できます。

## ロジック アプリ用の Facebook コネクタの作成 ##
Facebook コネクタを使用するには、まず Facebook コネクタ API アプリのインスタンスを作成する必要があります。そのためには、次の手順に従います。

1.	Azure ポータルの左下にある [+ 新規] を使用して Azure Marketplace を開きます。
2.	[Web + モバイル]、[API アプリ] の順に移動して、"Facebook コネクタ" を検索します。
3.	Facebook コネクタを次のように構成します。
 
	![][1]
4.	[OK] をクリックして作成します。
5.	API アプリのインスタンスが作成されたら、同じリソース グループ内に Facebook コネクタを使用するロジック アプリを作成できます。 
	- Facebook コネクタ API アプリのインスタンスは、ロジック アプリから作成することもできます。 
	- ロジック アプリ エディターを開き、右側のギャラリーに表示されている Facebook コネクタをクリックします。
	- これにより、ロジック アプリが作成されたのと同じリソース グループに Facebook コネクタ API アプリのインスタンスが作成されます。


## ロジック アプリでの Facebook コネクタの使用 ##
API アプリを作成すると、ロジック アプリのトリガーやアクションとして Facebook コネクタを使用できます。そのためには、次の手順を実行する必要があります。

1.	新しいロジック アプリを作成し、Facebook コネクタが含まれるリソース グループを選択します。
 
	![][2]
2.	[トリガーとアクション] を選択してロジック アプリ デザイナーを開き、フローを構成します。 
 
	![][3]
3.	右側のギャラリーの [最近使用した項目] セクションに、Facebook コネクタが表示されます。それを選択します。
 
	![][4]
4.	右側のギャラリーの [最近使用した項目] に表示されている [Facebook コネクタ] をクリックすると、Facebook コネクタ API アプリをエディターにドロップすることができます。[承認] をクリックします。Facebook の資格情報を入力します。
  
	![][5]
5.	"Azure AppService ロジック アプリ" を許可します。 

	![][6] ![][7] ![][8]     
6.	トリガーを選択します。
 
	![][9]
7.	これで、Facebook トリガーから取得した投稿を他のアクションで使用できるようになりました。次のフローでは、ユーザーの Facebook のタイムラインに新しいメッセージが投稿されるたびに、同じ投稿が Tweeted ユーザーの Twitter のタイムラインにツイートされます。
 
	![][10]
8.	同様に、Facebook コネクタのアクションを使用してフローを作成できます。次のフローでは、Yammer グループに投稿された新しいメッセージを取得し、ユーザーによって管理される Facebook ページに同じ投稿を発行します。
 
	![][11]

**ヒント** - Facebook ページの ID や Yammer グループの ID を取得するには、URL に埋め込まれた数値コードを見つけます。

<!--Image references-->
[1]: ./media/app-service-logic-connector-facebook/img1.png
[2]: ./media/app-service-logic-connector-facebook/img2.png
[3]: ./media/app-service-logic-connector-facebook/img3.png
[4]: ./media/app-service-logic-connector-facebook/img4.png
[5]: ./media/app-service-logic-connector-facebook/img5.png
[6]: ./media/app-service-logic-connector-facebook/img6.png
[7]: ./media/app-service-logic-connector-facebook/img7.png
[8]: ./media/app-service-logic-connector-facebook/img8.png
[9]: ./media/app-service-logic-connector-facebook/img9.png
[10]: ./media/app-service-logic-connector-facebook/img10.png
[11]: ./media/app-service-logic-connector-facebook/img11.png

<!--HONumber=54-->
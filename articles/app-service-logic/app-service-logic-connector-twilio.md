<properties
   pageTitle="Logic Apps での Twilio コネクタの使用 | Microsoft Azure App Service"
   description="Twilio コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法"
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
   ms.date="08/23/2015"
   ms.author="sameerch"/>


# Twilio コネクタの使用開始とロジック アプリへの追加
Twilio アカウントに接続して、SMS メッセージを送受信します。また、電話番号や使用状況データを取得することもできます。ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。この Twilio コネクタをビジネス ワークフローに追加し、ロジック アプリ内のそのワークフローの一部としてデータを処理できます。

## Logic App 用の Twilio コネクタを作成する ##
コネクタは、ロジック アプリ内で作成することも、Azure Marketplace から直接作成することもできます。Marketplace からコネクタを作成するには、次の操作を実行します。

1. Azure のスタート画面で、**[Marketplace]** を選択します。
2. "Twilio コネクタ" を検索して選択し、**[作成]** を選択します。
3. 次のように、Twilio コネクタを構成します。  
![][1]  
	- **[場所]** - コネクタをデプロイする地域を選択します。
	- **[サブスクリプション]** - コネクタを作成するサブスクリプションを選択します。
	- **[リソース グループ]** - コネクタを格納するリソース グループを選択するか、作成します。
	- **[Web ホスティング プラン]** - Web ホスティング プランを選択するか、作成します。
	- **[価格レベル]** - コネクタの価格レベルを選択します。
	- **[名前]** - Twilio コネクタの名前を指定します。
	- **パッケージの設定**
		- **[アカウント SID]** - アカウントの一意識別子。ユーザーのアカウントのアカウント SID は <https://www.twilio.com/user/account/settings> から取得できます。
		- **[認証トークン]** - アカウントに関連付けられている認証トークン。ユーザーのアカウントの認証トークンは <https://www.twilio.com/user/account/settings> から取得できます


4.	[作成] をクリックします。新しい Twilio コネクタが作成されます。
5.	API アプリのインスタンスを作成したら、Twilio コネクタを使用するロジック アプリを作成できます。

## Logic App で Twilio コネクタを使用する ##
API アプリを作成すると、Logic App のアクションとして Twilio コネクタを使用できます。そのためには、次の手順を実行する必要があります。

1.	新しいロジック アプリを作成し、Twilio コネクタと同じリソース グループを選択します。  
![][2]
2.	[トリガーとアクション] を選択して Logic Apps デザイナーを開き、フローを構成します。  
![][3]
3.	Twilio コネクタが右側のギャラリーの [API Apps in this resource group (このリソース グループの API Apps)] セクションに表示されます。  
![][4]
4. [Twilio コネクタ] をクリックして、Twilio コネクタの API アプリをエディターにドロップできます。

5.	これで、フローで Twilio コネクタを使用できるようになりました。フローで [メッセージの送信] アクションを使用すると、メッセージを送信できます。[メッセージを送信] アクションの入力プロパティを次のように構成します。
	- **通話元の電話番号** - 送信するメッセージの種類が有効になっている Twilio の電話番号を入力します。Twilio から購入した電話番号または短いコードのみがこのコネクタで機能します。
	- **通話先の電話番号** - 宛先電話番号。使用できる書式: + の後に国コード、その次に電話番号を続けます。たとえば、+16175551212 です。+ を省略した場合、Twilio は [通話元の電話番号] に入力した国コードを使用します。
	- **テキスト** - 送信するメッセージのテキスト。

	![][5]
	![][6]

## コネクタでできること
コネクタが作成されたため、Logic App を使用してコネクタをビジネス ワークフローに追加できます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure Logic Apps の使用を開始する場合は、「[Azure App Service アプリケーションの作成](https://tryappservice.azure.com/?appservice=logic)」を参照してください。App Service で有効期間の短いスターター ロジック アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

「[Connectors and API Apps Reference (コネクタと API Apps のリファレンス)](http://go.microsoft.com/fwlink/p/?LinkId=529766)」で Swagger REST API のリファレンスを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。[組み込みの API Apps とコネクタの管理と監視](app-service-logic-monitor-your-connectors.md)に関するページを参照してください。

<!--Image references-->
[1]: ./media/app-service-logic-connector-twilio/img1.PNG
[2]: ./media/app-service-logic-connector-twilio/img2.PNG
[3]: ./media/app-service-logic-connector-twilio/img3.png
[4]: ./media/app-service-logic-connector-twilio/img4.png
[5]: ./media/app-service-logic-connector-twilio/img5.PNG
[6]: ./media/app-service-logic-connector-twilio/img6.PNG

<!---HONumber=Oct15_HO3-->
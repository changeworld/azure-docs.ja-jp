<properties
	pageTitle="App Service 環境でホストされる API を PowerApps Enterprise で開発または作成する | Microsoft Azure"
	description="Azure ポータルを使用して App Service 環境でホストされるカスタム API を登録する方法をについて説明します。"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="12/09/2015"
   ms.author="guayan"/>

# App Service 環境内でホストされている API の登録
PowerApps は、クラウド内またはオンプレミス内でホストされている既存の API の登録をサポートしています。非常に強力です。シナリオによっては、新しい API を開発または作成することがあります。次のような場合です。

- 組織で使用する新しい機能を実装する。
- アプリを作成するユーザーのために、既存の機能やデータを基にして、より優れたエクスペリエンスを提供する。

App Service 環境で API をホストするときは、[App Service 環境](../app-service-app-service-environment-intro.md)のすべての既存機能を活用し、よりよい統合エクスペリエンスも利用します。

これらの API をアプリで使用するには、Azure ポータルで API を「登録」する必要があります。以下のオプションを使用できます。

- 事前に構築された [Microsoft 管理の API または IT 管理の API](powerapps-register-from-available-apis.md) を登録します。
- App Service Environment (このトピックの) 内にホストされた Web アプリ、API アプリ、およびモバイル アプリを登録します。
- [Swagger 2.0 API 定義](powerapps-register-existing-api-from-api-definition.md)を使用して、いずれかの独自の Swagger API を登録します。

この記事では、**アプリ サービス環境内でホストされている Web アプリ、API アプリ、モバイル アプリを登録する**方法について説明します。

#### 開始のための前提条件

- [PowerApps Enterprise](powerapps-get-started-azure-portal.md) にサインアップします。
- [App Service 環境](powerapps-get-started-azure-portal.md)を作成します。


## App Service 環境での API の開発とデプロイ

App service 環境では API を簡単に開発できます。好みのプログラミング言語を選択して Web API を作成し、[Swagger 2.0](http://swagger.io) を使用して API 定義を記述します。次に例をいくつか示します。

- [Azure App Service での .NET の構築とデプロイ](../app-service-api-dotnet-get-started.md)
- [Azure App Service での Java API アプリの構築とデプロイ](../app-service-api-java-api-app.md)
- [Azure App Service での Node.js API アプリの構築とデプロイ](../app-service-api-nodejs-api-app.md)

App Service 環境への Web API のデプロイでは、Visual Studio からのデプロイや、ソース管理システムを使用した継続的なデプロイなどのオプションもあります。「[Azure App Service での Web アプリのデプロイ](../web-sites-deploy.md)」でわかりやすく解説されています。

## App Service 環境でのカスタム API の登録

App Service 環境に API をデプロイした後は、以下の手順を使用して登録します。

1. [Azure ポータル](https://portal.azure.com/)で、**[PowerApps]** を選択して、**[API の管理]** を選択します。 ![][11]
2. [API の管理] で、**[追加]** を選択します。 ![][12]  
3. **[API の追加]** で、API のプロパティを入力します。  

	- **[名前]** に、API の名前を入力します。入力した名前が、API のランタイム URL に含まれていることを確認してください。名前は意味があり、組織内で一意のものにしてください。	
	- **[ソース]** で、**[App Service 環境でホストされている API から]** を選択します。 ![][13]
4. **[App Service 環境でホストされている API]** で、インポートするAPI を選択します。この一覧には、App Service 環境内に存在し、**apiDefinition.url** プロパティが構成されている、すべての Web アプリ、API アプリ、モバイル アプリが表示されます。API をインポートするには、このプロパティを使用して公開される Swagger 2.0 API 定義を使用します。API を登録するときは、この URL にパブリックにアクセスできることを確認します。 ![][14]
5. **[追加]** を選択して、手順を完了します。

> [AZURE.TIP]API を登録すると、その API が App Service Environment に登録されます。API が App Service Environment に登録されると、同じ App Service Environment 内の他のアプリでも使用することができます。

## まとめと次のステップ
このトピックでは、App Service 環境内でホストされている API を登録する方法について説明しました。PowerApps についての関連トピックとリソースがあります。

- [API プロパティの構成](powerapps-configure-apis.md)
- [ユーザーへの API アクセス許可](powerapps-manage-api-connection-user-access.md)
- [PowerApps でのアプリ作成の開始](https://powerapps.microsoft.com/tutorials/)に関するページ

<!--Reference-->
[11]: ./media/powerapps-register-api-hosted-in-app-service/registered-apis-part.png
[12]: ./media/powerapps-register-api-hosted-in-app-service/add-api-button.png
[13]: ./media/powerapps-register-api-hosted-in-app-service/add-api-blade.png
[14]: ./media/powerapps-register-api-hosted-in-app-service/add-api-select-from-ase.png

<!---HONumber=AcomDC_1210_2015-->
<properties
	pageTitle="PowerApps Enterprise での API からの Swagger 2.0 API 定義の作成 | Microsoft Azure"
	description="既存の API から作成した Swagger 2.0 API 定義から API を登録する方法を説明します"
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
   ms.date="03/02/2016"
   ms.author="guayan"/>

# Swagger 2.0 API 定義からの API の登録  
多くの組織には、ユーザーが自分のアプリ内で使用できる API が既にあります。これらの API をアプリで使用するには、マネージ API を使用するか、App Service 環境に存在する API を使用するか、または Swagger を使用して API を作成して、Azure ポータルで "登録" する必要があります。

> [AZURE.SELECTOR]
- [マネージ API](../articles/power-apps/powerapps-register-from-available-apis.md)
- [ASE の API](../articles/power-apps/powerapps-register-api-hosted-in-app-service.md)
- [Swagger API](../articles/power-apps/powerapps-register-existing-api-from-api-definition.md)

このトピックでは、**既存の API から作成した Swagger 2.0 API 定義を使用して作成した API の 1 つを登録する**、という 3 つ目の選択肢について説明します。

#### 開始のための前提条件

- [PowerApps Enterprise](powerapps-get-started-azure-portal.md) にサインアップします
- [App Service 環境](powerapps-get-started-azure-portal.md)を作成します

## Swagger 2.0 API 定義を使用した既存 API の登録

既存の API をとても簡単に登録できます。手順は次のとおりです。

1. 既存の API の [Swagger 2.0](http://swagger.io) API 定義を作成します。PowerApps は、API 定義の形式として Swagger 2.0 を使用します。[Swagger 2.0 の Web サイト](http://swagger.io)で紹介されているツールを使えば、Swagger 2.0 API 定義を簡単に作成できます。次の点に注意してください。  

	- ``host`` プロパティでは、既存の API の実際のエンドポイントを参照する必要があります。スキームやサブ パスは使用しないでください。たとえば、「``api.contoso.com``」のように入力します。<br/><br/>
	- 既存の API エンドポイントがある場合は、そのサブ パスを ``basePath`` プロパティで列記する必要があります。フォワードスラッシュ ``/`` で始めます。たとえば、「``/purchaseorderapi``」のように入力します。

2. App Service 環境から既存の API に安全にアクセスできることを確認します。

	1. インターネットを使用して問題なく API にアクセスできる場合、App Service 環境と既存の API 間に HTTP の基本アクセス認証を設定できます。[既存の API を更新](powerapps-configure-apis.md)して結果を確認してください。<br/><br/>
	2. 組織のネットワーク内に API を保持する場合は、App Service 環境に仮想ネットワークをセットアップすることで、組織のネットワークに安全にアクセスできます。「[App Service 環境の概要](../app-service-web/app-service-app-service-environment-intro.md)」を参照してください。

3. [Azure ポータル](https://portal.azure.com/)で、**[PowerApps]** を選択して、**[API の管理]** を選択します。 ![][11]
4. [API の管理] で、**[追加]** を選択します。 ![][12]
5. **[API の追加]** で、API のプロパティを入力します。  

	- **[名前]** に、API の名前を入力します。入力した名前が、API のランタイム URL に含まれていることを確認してください。名前は意味があり、組織内で一意のものにしてください。
	- **[ソース]** で **[Swagger 2.0 からインポート]** を選択します。

6. **[API 定義 (Swagger 2.0)]** で、Swagger 2.0 API 定義ファイルをアップロードします。 ![][13]
7. **[追加]** を選択して、手順を完了します。

> [AZURE.TIP] API を登録すると、その API が App Service Environment に登録されます。API が App Service Environment に登録されると、同じ App Service Environment 内の他のアプリでも使用することができます。

## まとめと次のステップ

このトピックでは、Swagger 2.0 API 定義から API を登録する方法を説明しました。PowerApps についての関連トピックとリソースがあります。

- [API プロパティの構成](powerapps-configure-apis.md)
- [ユーザーへの API アクセス許可](powerapps-manage-api-connection-user-access.md)
- [PowerApps でのアプリ作成の開始に関するページ](https://powerapps.microsoft.com/tutorials/)

<!--References-->
[11]: ./media/powerapps-register-existing-api-from-api-definition/registered-apis-part.png
[12]: ./media/powerapps-register-existing-api-from-api-definition/add-api-button.png
[13]: ./media/powerapps-register-existing-api-from-api-definition/add-api-blade.png

<!---HONumber=AcomDC_0309_2016-->
<properties 
	pageTitle="Azure App Service の API アプリに Web アプリを接続する" 
	description="このチュートリアルでは、Azure App Service でホストされる ASP.NET Web アプリから API アプリを使用する方法について示します。" 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="syntaxc4" 
	manager="yochayk" 
	editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="na" 
	ms.date="09/15/2015"
	ms.author="cfowler"/>

# Azure App Service の API アプリに Web アプリを接続する

このチュートリアルでは、[App Service](../app-service.md) でホストされる ASP.NET Web アプリから API アプリを使用する方法について示します。

## 前提条件

このチュートリアルは、次の API アプリ チュートリアル シリーズに基づいています。

1. [Azure API アプリの作成](../app-service-dotnet-create-api-app)
3. [Azure API アプリのデプロイ](../app-service-dotnet-deploy-api-app)
4. [Azure API アプリのデバッグ](../app-service-dotnet-remotely-debug-api-app)

## API アプリに対してパブリック アクセスができるようにする

[Azure プレビュー ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)で [API アプリ] を選択します。コマンド バーの **[設定]** をクリックします。**[アプリケーションの設定]** ブレードで、**[アクセス レベル]** を **[パブリック (匿名)]** に変更します。

![](./media/app-service-web-connect-web-app-to-saas-api/4-5-Change-Access-Level-To-Public.png)

## Visual Studio で ASP.NET MVC アプリケーションを作成する

1. Visual Studio を開きます。**[新しいプロジェクト]** ダイアログを使用して、新しい **ASP.NET Web アプリケーション**を追加します。**[OK]** をクリックします。

	![ファイル > 新規作成 > Web > ASP.NET Web アプリケーション](./media/app-service-web-connect-web-app-to-saas-api/1-Create-New-MVC-App-For-Consumption.png)

1. **MVC** テンプレートを選択します。**[認証の変更]** をクリックし、**[認証なし]** を選択して **[OK]** を 2 回クリックします。

	![新しい ASP.NET アプリケーション](./media/app-service-web-connect-web-app-to-saas-api/2-Change-Auth-To-No-Auth.png)

1. ソリューション エクスプローラーで、新しく作成した Web アプリケーション プロジェクトを右クリックし、**[Azure アプリ参照の追加]** を選択します。

	![Azure API アプリ参照の追加](./media/app-service-web-connect-web-app-to-saas-api/3-Add-Azure-API-App-SDK.png)

1. **[既存の API アプリ]** ボックスで、接続する API アプリを選択します。

	![既存の API アプリを選択](./media/app-service-web-connect-web-app-to-saas-api/4-Add-Azure-API-App-SDK-Dialog.png)

	>[AZURE.NOTE]API アプリに接続するためのクライアント コードが Swagger API エンドポイントから自動的に生成されます。

1. 生成される API コードを活用するには、HomeController.cs ファイルを開き、`Contact` アクションを次のコードに置き換えます。

	    public async Task<ActionResult> Contact()
	    {
	        ViewBag.Message = "Your contact page.";
	
	        var contacts = new ContactsList();
	        var response = await contacts.Contacts.GetAsync();
	        var contactList = response.Body;
	
	        return View(contactList);
	    }

	![HomeController.cs コードの更新](./media/app-service-web-connect-web-app-to-saas-api/5-Write-Code-Which-Leverages-Swagger-Generated-Code.png)

1. 連絡先の動的リストを反映するよう、以下のコードで `Contact` ビューを更新します。
	<pre>// Add to the very top of the view file
	@model IList&lt;MyContactsList.Web.Models.Contact&gt;
	
	// Replace the default email addresses with the following
    &lt;h3&gt;Public Contacts&lt;/h3&gt;
    &lt;ul&gt;
        @foreach (var contact in Model)
        {
            &lt;li&gt;&lt;a href=&quot;mailto:@contact.EmailAddress&quot;&gt;@contact.Name &amp;lt;@contact.EmailAddress&amp;gt;&lt;/a&gt;&lt;/li&gt;
        }
    &lt;/ul&gt; 
	</pre>

	![Contact.cshtml コードの更新](./media/app-service-web-connect-web-app-to-saas-api/6-Update-View-To-Reflect-Changes.png)

## App Service の Web Apps に Web アプリケーションをデプロイする

[Azure Web アプリのデプロイ方法](web-sites-deploy.md)に関するページにある手順に従ってください。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページにアクセスしてください。App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 変更内容
* Web サイトから App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 以前のポータルから新しいポータルへの変更ガイドについては、「[Azure ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)」を参照してください。
 

<!---HONumber=Nov15_HO3-->
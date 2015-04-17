<properties 
	pageTitle="Azure App Service の API アプリの作成" 
	description="この記事では、Visual Studio 2013 を使用して、Azure App Service の API アプリを作成する方法について説明します。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="bradyg;tarcher"/>

# Azure App Service の API アプリの作成

## 概要

このチュートリアルは、3 部構成になっているチュートリアルの最初で、Azure App Service の API アプリの使用を開始する方法について説明します。

1. このチュートリアルでは、新しい API アプリを作成し、さらに、Azure サブスクリプションにデプロイする準備を行います。 
* [API アプリのデプロイ](app-service-dotnet-create-api-app.md)に関するチュートリアルでは、API アプリを Azure サブスクリプションにデプロイします。
* [API アプリのデバッグ](app-service-dotnet-remotely-debug-api-app.md)に関するチュートリアルでは、Azure で実行中のコードを Visual Studio を使用してリモートでデバッグします。

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## 初めての API アプリの作成 ##

Visual Studio 2013 を開き、**[ファイル]、[新しいプロジェクト]** の順に選択します。**[ASP.NET Web アプリケーション]** テンプレートを選択します。  プロジェクトに *ContactsList* という名前を付けて **[OK]** をクリックします。

![](./media/app-service-dotnet-create-api-app/01-filenew-v3.png)

**[Azure API App]** プロジェクト テンプレートを選択して **[OK]** をクリックします。

![](./media/app-service-dotnet-create-api-app/02-api-app-template-v3.png)

Web API プロジェクトの **[モデル]** フォルダーを右クリックして、コンテキスト メニューの **[追加]、[クラス]** の順に選択します。 

![](./media/app-service-dotnet-create-api-app/03-add-new-class-v3.png) 

新しいファイルに *Contact.cs* という名前を付け、**[追加]** をクリックします。 

![](./media/app-service-dotnet-create-api-app/0301-add-new-class-dialog-v3.png) 

ファイルの内容全体を次のコードに置き換えます。 

	namespace ContactsList.Models
	{
		public class Contact
		{
			public int Id { get; set; }
			public string Name { get; set; }
			public string EmailAddress { get; set; }
		}
	}

**[コントローラー]** フォルダーを右クリックしてから、コンテキスト メニューの **[追加]、[コントローラー]** の順に選択します。 

![](./media/app-service-dotnet-create-api-app/05-new-controller-v3.png)

**[スキャフォールドの追加]** ダイアログで、**[Web API 2 コントローラー - 空]** を選択し、**[追加]** をクリックします。 

![](./media/app-service-dotnet-create-api-app/06-new-controller-dialog-v3.png)

コントローラーに **ContactsController** という名前を付け、**[追加]** をクリックします。 

![](./media/app-service-dotnet-create-api-app/07-new-controller-name-v2.png)

このファイルのコードを次のコードに置き換えます。 

	using ContactsList.Models;
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Net;
	using System.Net.Http;
	using System.Threading.Tasks;
	using System.Web.Http;
	
	namespace ContactsList.Controllers
	{
	    public class ContactsController : ApiController
	    {
	        [HttpGet]
	        public IEnumerable<Contact> Get()
	        {
	            return new Contact[]{
					new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
					new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
                	new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
	            };
	        }
	    }
	}

API アプリ プロジェクトが有効になり、API テスト ページと共に [Swagger](http://swagger.io/ "Official Swagger information") メタデータが自動生成されます。既定では、API テスト ページは無効に設定されています。API テスト ページを有効にするには、*App_Start/SwaggerConfig.cs* ファイルを開きます。**EnableSwaggerUI** を検索します。

![](./media/app-service-dotnet-create-api-app/12-enable-swagger-ui-with-box.png)

次のコード行をコメント解除します。

        })
    .EnableSwaggerUi(c =>
        {

完了すると、このファイルは、Visual Studio 2013 で次のように表示されます。

![](./media/app-service-dotnet-create-api-app/13-enable-swagger-ui-with-box.png)

API テスト ページを表示するには、ローカルでアプリを実行し、`/swagger` に移動します。 

![](./media/app-service-dotnet-create-api-app/14-swagger-ui.png)

**[Try it out]** をクリックすると、API が機能しており、予期される結果を返すことを確認できます。 

![](./media/app-service-dotnet-create-api-app/15-swagger-ui-post-test.png)

## 次のステップ

これで API アプリをデプロイする準備ができました。[API アプリのデプロイ](app-service-dotnet-deploy-api-app.md)に関するチュートリアルの手順に従って実際にデプロイできます。

詳細については、「[API Apps とは](app-service-api-apps-why-best-platform.md)」を参照してください。

<!--HONumber=49-->
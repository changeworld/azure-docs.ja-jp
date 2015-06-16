<properties 
	pageTitle="Azure App Service での ASP.NET API アプリの作成" 
	description="Visual Studio 2013 を使用して、Azure App Service で ASP.NET API アプリを作成する方法について説明します。" 
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

# Azure App Service での ASP.NET API アプリの作成

## 概要

このチュートリアルでは、ASP.NET Web API プロジェクトを 1 から作成し、[Azure App Service](../app-service/app-service-value-prop-what-is.md) で [API アプリ](app-service-api-apps-why-best-platform.md)としてクラウドにデプロイできるように構成する方法について説明します。既存の Web API プロジェクトを API アプリに変換する場合は、「[API アプリとしての Web API プロジェクトの構成](./app-service-dotnet-create-api-app-visual-studio)」を参照してください。このシリーズの以降のチュートリアルでは、このチュートリアルで作成する API アプリ プロジェクトを[デプロイ](app-service-dotnet-deploy-api-app.md)し、[デバッグ](../app-service-dotnet-remotely-debug-api-app.md)する方法について説明します。

API アプリの詳細については、「[API Apps とは](app-service-api-apps-why-best-platform.md)」を参照してください。

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

このチュートリアルでは、Azure SDK for .NET 2.5.1 以降のバージョンが必要です。

## API アプリ プロジェクトの作成 

このセクションでは、Azure API アプリ プロジェクト テンプレートを使用して、API アプリを 1 から作成する方法を説明します。既存の Web API プロジェクトを API アプリとして構成する方法については、[次のセクション](#configure-a-web-api-project-as-an-api-app)に進んでください。

1. Visual Studio 2013 を開きます。

2. **[ファイル] メニューの [新しいプロジェクト]** をクリックします。

3. **[ASP.NET Web アプリケーション]** テンプレートを選択します。

4. プロジェクトに「*ContactsList*」という名前を付けます。

	![](./media/app-service-dotnet-create-api-app/01-filenew-v3.png)

5. **[OK]** をクリックします。

6. **[新しい ASP.NET プロジェクト]** ダイアログで、**[Azure API アプリ]** プロジェクト テンプレートを選択します。

	![](./media/app-service-dotnet-create-api-app/02-api-app-template-v3.png)

7. **[OK]** をクリックしてプロジェクトを生成します。

Visual Studio によって、API アプリとしてデプロイされるように構成された Web API プロジェクトが作成されます。

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## 次のステップ

これで API アプリをデプロイする準備ができました。[API アプリのデプロイ](app-service-dotnet-deploy-api-app.md)に関するチュートリアルの手順に従って実際にデプロイできます。

<!--HONumber=54--> 
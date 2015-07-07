<properties 
	pageTitle="API アプリとしての Web API プロジェクトの構成" 
	description="Visual Studio 2013 を使用して、API アプリとして Web API プロジェクトを構成する方法を説明します。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/19/2015" 
	ms.author="bradyg;tarcher"/>

# API アプリとしての Web API プロジェクトの構成

## 概要

このチュートリアルでは、既存の Web API プロジェクトを利用して、[Azure App Service](../app-service/app-service-value-prop-what-is.md) で [API アプリ](app-service-api-apps-why-best-platform.md)としてデプロイするために構成する方法を説明します。このシリーズの続きのチュートリアルで、このチュートリアルで作成する API アプリ プロジェクトの[デプロイ](app-service-dotnet-deploy-api-app.md)と[デバッグ](../app-service-dotnet-remotely-debug-api-app.md)の方法について説明します。

API アプリの詳細については、「[API Apps とは](app-service-api-apps-why-best-platform.md)」を参照してください。

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

このチュートリアルでは、Azure SDK for .NET 2.5.1 以降のバージョンが必要です。

## Web API プロジェクトの構成 

このセクションでは、API アプリとして既存の Web API プロジェクトを構成する方法を示します。まず Web API プロジェクトのテンプレートを使用して Web API プロジェクトを作成してから、このプロジェクトを API アプリとして構成します。

1. Visual Studio 2013 を開きます。

2. **[ファイル] メニューの [新しいプロジェクト]** をクリックします。

3. **[ASP.NET Web アプリケーション]** テンプレートを選択します。

4. **[Application Insights をプロジェクトに追加]** チェック ボックスがオフになっていることを確認します。

4. プロジェクトに「*ContactsList*」という名前を付けます。

	![](./media/app-service-dotnet-create-api-app-visual-studio/01-filenew-v3.png)

5. **[OK]** をクリックします。

6. **[新しい ASP.NET プロジェクト]** ダイアログで、**空**のテンプレートを選択します。

7. **[Web API]** チェック ボックスをオンにします。

8. **[クラウドにホスト]** オプションをオフにします。

	![](./media/app-service-dotnet-create-api-app-visual-studio/webapinewproj.png)

9. **[OK]** をクリックしてプロジェクトを生成します。

	![](./media/app-service-dotnet-create-api-app-visual-studio/sewebapi.png)

10. **ソリューション エクスプローラー**で、プロジェクト (ソリューションではありません) を右クリックし、**[追加]、[Azure API App SDK]** の順に選択します。

	![](./media/app-service-dotnet-create-api-app-visual-studio/addapiappsdk.png)

11. **[API アプリのメタデータ ソースの選択]** ダイアログで、**[メタデータの自動生成]** をクリックします。

	![](./media/app-service-dotnet-create-api-app-visual-studio/chooseswagger.png)

	この選択により、Swagger の動的 UI が有効になります。これについてはチュートリアルの後半で説明します。Swagger メタデータ ファイルをアップロードすることにした場合は、次のセクションで説明されているように、ファイル名 *apiDefinition.swagger.json* で保存されます。

12. **[OK]** をクリックします。
 
	この時点で、Visual Studio によって API アプリの NuGet パッケージがインストールされ、API アプリのメタデータが Web API プロジェクトに追加されます。

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## 次のステップ

これで API アプリをデプロイする準備ができました。[API アプリのデプロイ](app-service-dotnet-deploy-api-app.md)に関するチュートリアルの手順に従って実際にデプロイできます。
 

<!---HONumber=62-->
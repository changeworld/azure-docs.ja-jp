<properties
	pageTitle="Azure App Service での ASP.NET API アプリの作成 | Microsoft Azure"
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
	ms.topic="hero-article"
	ms.date="08/14/2015"
	ms.author="tdykstra"/>

# Azure App Service での ASP.NET API アプリの作成

> [AZURE.SELECTOR]
- [.NET - Visual Studio 2015](app-service-dotnet-create-api-app.md)
- [.NET - Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)
- [Node.js](app-service-api-nodejs-api-app.md)
- [Java](app-service-api-java-api-app.md)

## 概要

このチュートリアルでは、[Azure App Service で API アプリ](app-service-api-apps-why-best-platform.md)としてクラウドにデプロイできるように構成された ASP.NET Web API プロジェクトを作成する方法について説明します。API アプリとしてデプロイできるように既存の Web API プロジェクトを構成する方法については、「[API アプリとしての Web API プロジェクトの構成](app-service-dotnet-create-api-app-visual-studio.md)」を参照してください。

このシリーズの続きのチュートリアルで、このチュートリアルで作成する API アプリ プロジェクトの[デプロイ](app-service-dotnet-deploy-api-app.md)と[デバッグ](../app-service-dotnet-remotely-debug-api-app.md)の方法について説明します。

[AZURE.INCLUDE [install-sdk-2015-2013](../../includes/install-sdk-2015-2013.md)]

このチュートリアルでは、Azure SDK for .NET 2.6 以降のバージョンが必要です。

## API アプリ プロジェクトの作成

プロジェクトの名前を入力するよう要求されたら、「**ContactsList**」と入力します。

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## 次のステップ

これで API アプリをデプロイする準備ができました。[API アプリのデプロイ](app-service-dotnet-deploy-api-app.md)に関するチュートリアルの手順に従って実際にデプロイできます。

<!---HONumber=Sept15_HO4-->
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
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Azure App Service での ASP.NET API アプリの作成

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## 概要

このチュートリアルでは、[Azure App Service で API アプリ](app-service-api-apps-why-best-platform.md)としてクラウドにデプロイできるように構成された ASP.NET Web API プロジェクトを作成する方法について説明します。API アプリとしてデプロイできるように既存の Web API プロジェクトを構成する方法については、「[API アプリとしての Web API プロジェクトの構成](app-service-dotnet-create-api-app-visual-studio.md)」を参照してください。

これは、テンプレートを使用して Visual Studio プロジェクトを作成する方法のみを示す手軽で簡単なチュートリアルです。このチュートリアルで作成する API アプリ プロジェクトを[デプロイ](app-service-dotnet-deploy-api-app.md)および[デバッグ](../app-service-dotnet-remotely-debug-api-app.md)する方法も示す、シリーズで最初のチュートリアルです。API アプリの詳しい操作方法については、このチュートリアル末尾の「[次のステップ](#next-steps)」セクションを参照してください。

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

自動的に生成されたクライアント コードを使用して API アプリを呼び出す方法については、[.NET クライアントから API アプリを使用する](app-service-api-dotnet-consume.md)方法に関するページを参照してください。

API アプリ向けに自動的に生成された Swagger メタデータをカスタマイズする方法については、「[Swashbuckle が生成する API 定義をカスタマイズする](app-service-api-dotnet-swashbuckle-customize.md)」を参照してください。

Azure プレビュー ポータルで API アプリを作成、削除、構成する方法については、[API アプリの管理](app-service-api-manage-in-portal.md)に関するページを参照してください。

API アプリのユーザーの認証については、「[Azure App Service での API Apps と Mobile Apps の認証](../app-service/app-service-authentication-overview.md)」を参照してください。

API アプリの機能については、「[API Apps とは](app-service-api-apps-why-best-platform.md)」を参照してください。

<!---HONumber=AcomDC_0114_2016-->
<properties 
	pageTitle="API アプリ パッケージの作成" 
	description="API アプリ パッケージを Azure Marketplace に発行する方法について説明します。" 
	services="app-service\api" 
	documentationCenter=".net"
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="guayan"/>

# Azure Marketplace への API アプリ パッケージの発行

## 概要

この記事では、API アプリ パッケージを [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/) に発行する方法について説明します。

- API アプリを作成する方法については、[Visual Studio を使用した API アプリの作成](app-service-dotnet-create-api-app.md)に関するページを参照してください。
- API アプリ パッケージを作成する方法については、「[API アプリ パッケージの作成](app-service-api-create-package)」を参照してください。

## 一般的な発行フロー

一般的な発行フローを次に示します。

1. チュートリアル「[API アプリ パッケージの作成](app-service-api-create-package)」の手順に従って、API アプリの Nuget パッケージを作成します。
2. API アプリ パッケージを Nuget 対応ギャラリー (https://apiapps.nuget.org) に発行します。
3. API アプリ パッケージが自動的に [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/) に同期されます。
4. [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/) と [Azure プレビュー ポータル](https://portal.azure.com)に移動し、エンド ツー エンドのエクスペリエンスを確認します。

## Nuget 対応ギャラリーへの発行

1. https://apiapps.nuget.org にアクセスします。

    ![Nuget 対応ギャラリーのホーム ページ](./media/app-service-api-publish-package/nuget-homepage.png)

2. **[Sign in]** をクリックし、Azure アカウントを使用してログインします。
3. **[API Apps]** タブ、**[Upload API App]** の順にクリックし、API アプリ パッケージをアップロードします。

    ![Nuget 対応ギャラリーの [Upload Package] ページ](./media/app-service-api-publish-package/nuget-upload-package-page.png)

4.  **[Namespace]** ボックスの一覧に、使用している Azure アカウントの Azure Active Directory テナントの確認済みドメインがすべて表示されます。API アプリ パッケージの apiapp.json ファイルの名前空間プロパティに一致する名前空間を選択します。発行者が API アプリ パッケージの有効な名前空間を要求できるようにするために、この値が検証されます。
5. **[Unlist this API app]** チェック ボックスがオフになっていることを確認します。
6. **[発行]** をクリックします。
7. 検証エラーが発生した場合は、エラーを修正してからもう一度アップロードします。

## Azure Marketplace で API アプリ パッケージを表示する

数分後、API アプリ パッケージが Azure Marketplace に同期されます。[このページ](http://azure.microsoft.com/marketplace/api-apps/)から、タイトル、説明、アイコンなどを確認できます。変更が必要な場合は、API アプリ パッケージに変更を加えた後、API アプリ パッケージを再び発行します。

![Azure Marketplace API アプリのページ](./media/app-service-api-publish-package/acom-marketplace-apiapp-page.png)

## Azure プレビュー ポータルで API アプリ パッケージをデプロイする

Azure アカウントを使用して [Azure プレビュー ポータル](https://portal.azure.com)にログインすることもできます (API アプリ パッケージの発行に使用したアカウントとは異なるアカウントを使用できます)。ここでは、Azure プレビュー ポータルでの API アプリ パッケージの作成エクスペリエンスを確認できます。変更が必要な場合は、API アプリ パッケージに変更を加えた後、API アプリ パッケージを再び発行します。

Azure ポータルで API アプリ パッケージをデプロイする方法の詳細については、[このページ](app-service-api-connnect-your-app-to-saas-connector.md)の DropboxConnector をデプロイする例を参照してください。

<!--HONumber=52-->

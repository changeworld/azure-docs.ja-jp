---
title: "Azure Web App on Linux での継続的なデプロイ | Microsoft Docs"
description: "Azure Web App on Linux で継続的なデプロイを設定する方法について説明します。"
keywords: Azure App Service, Linux, OSS, ACR
services: app-service
documentationcenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;wesmc
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 6b6c173c6c4bb3f670c54208c80e6d966a1f396e
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="continuous-deployment-with-azure-web-app-on-linux"></a>Azure Web App on Linux での継続的なデプロイ

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

このチュートリアルでは、管理対象の [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/) リポジトリまたは [Docker Hub](https://hub.docker.com) からのカスタム コンテナー イメージに継続的なデプロイを構成します。

## <a name="step-1---sign-in-to-azure"></a>手順 1 - Azure にサインインする

Azure Portal (http://portal.azure.com) にサインインします

## <a name="step-2---enable-docker-hub-continuous-deployment"></a>手順 2 - Docker Hub の継続的なデプロイを有効にする

**[Azure Portal](https://portal.azure.com/)** で、ページの左側にある **[App Service]** オプションをクリックします。

Docker Hub の継続的なデプロイを構成するアプリの名前をクリックします。

**[App settings (アプリ設定)]** で、`true` を使用して `DOCKER_ENABLE_CI` と呼ばれるアプリ設定を追加します。

![アプリ設定のイメージを挿入](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---prepare-webhook-url"></a>手順 3 - Webhook URL を準備する

Webhook URL には、エンドポイント `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook` が必要です。

`publishingusername` および `publishingpwd` を取得するには、Azure Portal を使用して Web アプリの発行プロファイルをダウンロードします。

![webhook 2 追加のイメージを挿入](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="step-4---add-a-web-hook"></a>手順 4 - webhook を追加する

### <a name="azure-container-registry"></a>Azure Container Registry

レジストリ ポータル ブレードで、**[Webhooks] \(Webhook\)** をクリックし、**[追加]** をクリックして、新しい webhook を作成します。 **[Create webhook]\(webhook の作成\)** ブレードで、webhook に名前を付けます。 [Webhook URI] には、**手順 3** で取得した URL を指定する必要があります

スコープがコンテナー イメージを含むリポジトリとして定義されていることを確認してください。

![webhook のイメージを挿入](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

イメージが更新されると、Web アプリも新しいイメージで自動的に更新されます。

### <a name="docker-hub"></a>Docker Hub

Docker Hub ページで、**[Webhooks (Webhook)]**、**[CREATE A WEBHOOK (Webhook の作成)]** の順にクリックします。

![webhook 1 追加のイメージを挿入](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

[Webhook URL] には、**手順 3** で取得した URL を指定する必要があります

![webhook 2 追加のイメージを挿入](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

イメージが更新されると、Web アプリも新しいイメージで自動的に更新されます。

## <a name="next-steps"></a>次のステップ
* [Azure Web App on Linux とは](./app-service-linux-intro.md)
* [Azure Web App on Linux でアプリを作成する](./app-service-linux-how-to-create-web-app.md)
* [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/)
* [Azure Web App on Linuxで Node.js の PM2 構成を使用する](app-service-linux-using-nodejs-pm2.md)
* [Azure Web App on Linux で .NET Core を使用する](app-service-linux-using-dotnetcore.md)
* [Azure Web App on Linuxで Ruby を使用する](app-service-linux-ruby-get-started.md)
* [Azure Web App on Linux 向けにカスタム Docker イメージを使用する方法](./app-service-linux-using-custom-docker-image.md)
* [Azure App Service Web App on Linux の FAQ](./app-service-linux-faq.md) 





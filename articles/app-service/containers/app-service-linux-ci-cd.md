---
title: Web App for Containers での Docker コンテナー レジストリからの継続的なデプロイ - Azure | Microsoft Docs
description: Web App for Containers で Docker コンテナー レジストリからの継続的なデプロイを設定する方法。
keywords: Azure App Service, Linux, Docker, acr, oss
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;msangapu
ms.openlocfilehash: ac35dbd041de50ab8aae1a0fb4c00fe3917a7297
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Web App for Containers での継続的デプロイ

このチュートリアルでは、管理された [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) リポジトリまたは [Docker Hub](https://hub.docker.com) からのカスタム コンテナー イメージの継続的なデプロイを構成します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure Portal](https://portal.azure.com) にサインインします。

## <a name="enable-the-continuous-deployment-feature"></a>継続的なデプロイ機能を有効にする

[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) を使用し、次のコマンドを実行することによって、継続的なデプロイ機能を有効にします。

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

[Azure Portal](https://portal.azure.com/) で、ページの左側にある **[App Service]** オプションを選択します。

Docker Hub の継続的なデプロイを構成するアプリの名前を選択します。

**[Docker コンテナー]** ページで、**[オン]** を選択してから、**[保存]** を選択して継続的なデプロイを有効にします。

![アプリ設定のスクリーンショット](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-the-webhook-url"></a>webhook URL を準備する

[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) を使用し、次のコマンドを実行することによって、webhook URL を取得します。

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

webhook URL の場合は、エンドポイント `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook` が必要です。

`publishingusername` および `publishingpwd` を取得するには、Azure Portal を使用して Web アプリの発行プロファイルをダウンロードします。

![webhook 2 の追加のスクリーンショット](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>webhook を追加する

### <a name="azure-container-registry"></a>Azure Container Registry

1. レジストリ ポータル ページで、**[Webhook]** を選択します。
2. 新しい webhook を作成するには、**[追加]** を選択します。 
3. **[Create webhook] (webhook の作成)** ウィンドウで、webhook に名前を付けます。 webhook URI には、前のセクションで取得された URL を指定します。

スコープがコンテナー イメージを含むリポジトリとして定義されていることを確認してください。

![webhook のスクリーンショット](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

イメージを更新すると、Web アプリが新しいイメージで自動的に更新されます。

### <a name="docker-hub"></a>Docker Hub

[Docker Hub] ページで、**[Webhook]**、**[CREATE A WEBHOOK] (webhook の作成)** の順に選択します。

![webhook 1 の追加のスクリーンショット](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

webhook URL には、前に取得した URL を指定します。

![webhook 2 の追加のスクリーンショット](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

イメージを更新すると、Web アプリが新しいイメージで自動的に更新されます。

## <a name="next-steps"></a>次の手順

* [Azure App Service on Linux の概要](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [App Service on Linux での .NET Core Web アプリの作成](quickstart-dotnetcore.md)
* [App Service on Linux で Ruby Web アプリを作成する](quickstart-ruby.md)
* [Web App for Containers で Docker/Go Web アプリをデプロイする](quickstart-docker-go.md)
* [Azure App Service on Linux の FAQ](./app-service-linux-faq.md)
* [Azure CLI を使用して Web App for Containers を管理する](./app-service-linux-cli.md)

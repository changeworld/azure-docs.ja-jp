---
title: "Web App for Containers での Docker コンテナー レジストリからの継続的デプロイ - Azure | Microsoft Docs"
description: "Web App for Containers で Docker コンテナー レジストリから継続的デプロイをセットアップする方法"
keywords: Azure App Service, Linux, Docker, acr, oss
services: app-service
documentationcenter: 
author: ahmedelnably
manager: cfowler
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;msangapu
ms.openlocfilehash: e61c767ada31fc32e28bfd9a2a4e843e9ca88053
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Web App for Containers での継続的デプロイ

このチュートリアルでは、管理対象の [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) リポジトリまたは [Docker Hub](https://hub.docker.com) からのカスタム コンテナー イメージに継続的なデプロイを構成します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure ポータル](https://portal.azure.com)

## <a name="enable-container-continuous-deployment-feature"></a>コンテナーの継続的デプロイ機能を有効にする

[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) で次のコマンドを実行して、継続的なデプロイ機能を有効にできます。

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

**[Azure Portal](https://portal.azure.com/)** で、ページの左側にある **[App Service]** オプションをクリックします。

Docker Hub の継続的なデプロイを構成するアプリの名前をクリックします。

**[Docker コンテナー]** で、[オン] を選択し、[保存] を押して継続的デプロイを有効にします。

![アプリ設定のイメージを挿入](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-webhook-url"></a>Webhook URL を準備する

[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) で次のコマンドを実行して、Webhook URL を取得できます。

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

Webhook URL には、エンドポイント `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook` が必要です。

`publishingusername` および `publishingpwd` を取得するには、Azure Portal を使用して Web アプリの発行プロファイルをダウンロードします。

![webhook 2 追加のイメージを挿入](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-web-hook"></a>Webhook を追加する

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

## <a name="next-steps"></a>次の手順

* [Azure App Service on Linux とは](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Azure App Service on Linux で .NET Core を使用する](quickstart-dotnetcore.md)
* [Azure App Service on Linux で Ruby を使用する](quickstart-ruby.md)
* [Web App for Containers のカスタム Docker イメージを使用する方法](quickstart-docker-go.md)
* [Azure App Service Web App for Containers の FAQ](./app-service-linux-faq.md)
* [Azure CLI 2.0 を使って Web App for Containers を管理する](./app-service-linux-cli.md)

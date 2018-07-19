---
title: Web App for Containers での Docker コンテナー レジストリからの継続的なデプロイ - Azure | Microsoft Docs
description: Web App for Containers で Docker コンテナー レジストリからの継続的なデプロイを設定する方法。
keywords: Azure App Service, Linux, Docker, acr, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2018
ms.author: msangapu
ms.openlocfilehash: 0f2d4626308eed376b71f1b3df2f9e43f1b2a4f7
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130967"
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

Webhook URL を書き留めておきます。 これは、次のセクションで必要になります。
`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`

`publishingusername` および `publishingpwd` を取得するには、Azure Portal を使用して Web アプリの発行プロファイルをダウンロードします。

![webhook 2 の追加のスクリーンショット](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>webhook を追加する

Webhook を追加するには、次のガイドの手順に従います。

- Webhook URL を使用する [Azure Container Registry](../../container-registry/container-registry-webhook.md)
- [Docker Hub 用 Webhook](https://docs.docker.com/docker-hub/webhooks/)

## <a name="next-steps"></a>次の手順

* [Azure App Service on Linux の概要](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [App Service on Linux での .NET Core Web アプリの作成](quickstart-dotnetcore.md)
* [App Service on Linux で Ruby Web アプリを作成する](quickstart-ruby.md)
* [Web App for Containers で Docker/Go Web アプリをデプロイする](quickstart-docker-go.md)
* [Azure App Service on Linux の FAQ](./app-service-linux-faq.md)
* [Azure CLI を使用して Web App for Containers を管理する](./app-service-linux-cli.md)

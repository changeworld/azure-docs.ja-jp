---
title: "Web App for Containers でカスタム Docker Hub イメージを実行する | Microsoft Docs"
description: "Web App for Containers でカスタム Docker イメージを使用する方法"
keywords: "Azure App Service, Web アプリ, Linux, Docker, コンテナー"
services: app-service
documentationcenter: 
author: naziml
manager: cfowler
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/05/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: eadc0f7eb20b9e8d1cacc79b2907559e2b2535a2
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2017
---
# <a name="run-a-custom-docker-hub-image-in-web-app-for-containers"></a>Web App for Containers でカスタム Docker Hub イメージを実行する

App Service は、事前定義済みのアプリケーション スタックを Linux 上で提供し、PHP 7.0 や Node.js 4.5 などの特定のバージョンをサポートします。 まだ Azure で定義されていないアプリケーション スタックに Web アプリをデプロイする場合にも、カスタム Docker イメージを使用できます。 このクイックスタートでは、Web アプリを作成し、そのアプリに Python ベースの Docker イメージをデプロイする方法を示します。 Web アプリは、[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) を使用して作成します。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-container"></a>Web App for Containers の作成

[az webapp create](/cli/azure/webapp#create) コマンドを使って、`myAppServicePlan`App Service プランに [Web アプリ](../app-service-web-overview.md)を作成します。 `<app name>` を固有のアプリ名に置き換えることを忘れないでください。

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name elnably/dockerimagetest
```

前述のコマンド `--deployment-container-image-name` は、パブリック Docker Hub イメージ [https://hub.docker.com/r/elnably/dockerimagetest/](https://hub.docker.com/r/elnably/dockerimagetest/) をポイントしています。 その内容は、[https://github.com/rachelappel/docker-image](https://github.com/rachelappel/docker-image) で調べることができます。

Web アプリが作成されると、Azure CLI によって次の例のような出力が表示されます。

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="browse-to-the-app"></a>アプリの参照

Web ブラウザーで次の URL に移動します。

```bash
http://<app_name>.azurewebsites.net
```

![Azure で実行されるサンプル アプリ](media/quickstart-custom-docker-image/hello-world-in-browser.png)

**お疲れさまでした。** カスタム Docker イメージが Web App for Containers にデプロイされています。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure で Docker Python と PostgreSQL Web アプリを作成する](tutorial-docker-python-postgresql-app.md)

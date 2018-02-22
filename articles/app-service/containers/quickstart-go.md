---
title: "Azure Web App for Containers で Go アプリをデプロイする | Microsoft Docs"
description: "Azure Web Apps for Containers に対して Go アプリケーションを実行する Docker イメージをデプロイする方法。"
keywords: "Azure App Service, Web アプリ, Go, Docker, コンテナー"
services: app-service
author: sptramer
manager: cfowler
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.devlang: go
ms.topic: quickstart
ms.date: 01/17/2018
ms.author: sttramer
ms.custom: mvc
ms.openlocfilehash: dbe4d7bc6f5f1d83a079993c9616206fd82a1b9d
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2018
---
# <a name="deploy-a-go-app-in-azure-web-app-for-containers"></a>Azure Web App for Containers で Go アプリをデプロイする

App Service は、事前定義済みのアプリケーション スタックを Linux 上で提供し、PHP 7.0 や Node.js 4.5 などの特定のバージョンをサポートします。 まだ Azure で定義されていないアプリケーション スタックで Web アプリを実行する場合にも、カスタム Docker イメージを使用できます。 このクイック スタートでは、Go アプリケーションを含む既存の Docker コンテナーを取得し、それを Azure App Service で実行する方法を紹介します。 Web アプリは、[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) を使用して作成します。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-the-container"></a>Web App for the Container を作成する

[az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) コマンドを使って、`myAppServicePlan`App Service プランに [Web アプリ](../app-service-web-overview.md)を作成します。 忘れずに `<app name>` をグローバルに一意のアプリ名に置き換えてください。

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name microsoft/appservice-go-quickstart
```

上記のコマンドにある `--deployment-container-image-name` は、パブリック Docker Hub イメージ [microsoft/appservice-go-quickstart](https://hub.docker.com/r/microsoft/appservice-go-quickstart) を指しています。

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

## <a name="get-data-from-the-apps-endpoint"></a>アプリのエンドポイントからデータを取得する

`curl` コマンドを使用して、コンテナーのアプリケーションから提供された REST API エンドポイントにアクセスします。

```bash
curl -X GET http://<app_name>.azurewebsites.net:8080/hello
```

```output
Hello world!
```

**お疲れさまでした。** Go アプリケーションを実行しているカスタム Docker イメージが Web Apps for Containers にデプロイされました。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [カスタム Docker イメージを使用する](tutorial-custom-docker-image.md)

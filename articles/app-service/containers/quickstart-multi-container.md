---
title: Docker Compose 構成を使用して Azure Web App for Containers にマルチコンテナー (プレビュー) アプリを作成する
description: 最初のマルチコンテナー アプリを数分で Azure Web App for Containers にデプロイする
keywords: Azure App Service, Web アプリ, Linux, Docker, 作成, マルチコンテナー, コンテナー, Kubernetes
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/22/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: ec5c92415668c925fe360c0c8887fd792a121842
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753719"
---
# <a name="create-a-multicontainer-preview-app-using-web-app-for-containers"></a>Web App for Containers を使用してマルチコンテナー (プレビュー) アプリを作成する

[Web App for Containers](app-service-linux-intro.md) には、Docker イメージを柔軟に使用できる機能があります。 このクイック スタートでは、[Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) で Docker Compose 構成を使用して Web App for Containers にマルチコンテナー アプリをデプロイする方法を示します。 Kubernetes については、[マルチコンテナー チュートリアル](tutorial-multi-container-app.md)の Kubernetes の手順に従ってください。

このクイック スタートは Cloud Shell で行いますが、これらのコマンドは [Azure CLI](/cli/azure/install-azure-cli) (2.0.32 以降) を使用してローカルで実行することもできます。 このクイック スタートでは、Docker Compose 構成ファイルを使用します。

![Web App for Containers のサンプル マルチコンテナー アプリ][1]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>サンプルのダウンロード

このクイック スタートでは、[Docker](https://docs.docker.com/compose/wordpress/#define-the-project) の作成ファイルを使用しますが、Azure Database for MySQL、永続的なストレージ、Redis を含むように変更します。 構成ファイルは [Azure サンプル](https://github.com/Azure-Samples/multicontainerwordpress)にあります。

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

Cloud Shell で、クイックスタートのディレクトリを作成し、それに変更します。

```bash
mkdir quickstart

cd quickstart
```

次に、次のコマンドを実行して、サンプル アプリのリポジトリをクイックスタートのディレクトリに複製します。

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Cloud Shell で [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create) コマンドを使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを場所*米国中南部*に作成します。 **Standard** レベルの Linux 上の App Service がサポートされているすべての場所を表示するには、[`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations) コマンドを実行します。

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

通常は、現在地付近の地域にリソース グループおよびリソースを作成します。

コマンドが完了すると、リソース グループのプロパティが JSON 出力に表示されます。

## <a name="create-an-azure-app-service-plan"></a>Azure App Service プランの作成

Cloud Shell で [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) コマンドを使用して、リソース グループに App Service プランを作成します。

次の例では、**Standard** 価格レベル (`--sku S1`) を使用して、Linux コンテナー (`--is-linux`) に `myAppServicePlan` という名前の App Service プランを作成します。

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

App Service プランが作成されると、Azure CLI によって、次の例のような情報が表示されます。

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

## <a name="create-a-docker-compose-app"></a>Docker Compose アプリを作成する

Cloud Shell 端末で、`multicontainerwordpress` ディレクトリに移動します。 [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) コマンドを使って、`myAppServicePlan` App Service プランにマルチコンテナー [Web アプリ](app-service-linux-intro.md)を作成します。 _\<app_name>_ は忘れずに固有のアプリ名に置き換えてください。

```bash
cd multicontainerwordpress

az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Web アプリが作成されると、Azure CLI によって次の例のような出力が表示されます。

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>アプリの参照

展開されたアプリ (`http://<app_name>.azurewebsites.net`) を参照します。 アプリの読み込みには数分かかることがあります。 エラーが発生した場合は、数分待ってからブラウザーを更新してください。

![Web App for Containers のサンプル マルチコンテナー アプリ][1]

**これで終了です**。Web App for Containers にマルチコンテナー アプリが作成されました。

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Web App for Containers にマルチコンテナー WordPress アプリを作成する](tutorial-multi-container-app.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
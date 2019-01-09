---
title: Azure App Service プランで Linux 上に関数アプリを作成する
description: Azure CLI を使用して、App Service プラン内の Linux 上で実行される関数アプリを作成する方法について説明します。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: d9800ff3fc82636c5cae12167738667ec84326ee
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855553"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan-preview"></a>Azure App Service プランで Linux 上に 関数アプリを作成する (プレビュー)

Azure Functions を使うと、既定の Azure App Service コンテナー内の Linux で関数をホストできます。 この記事では、Azure CLI を使用して、Azure 上の Linux でホストされた関数アプリを作成し、[App Service プラン](functions-scale.md#app-service-plan)内で実行する方法について説明します。 [独自のカスタム コンテナーを利用する](functions-create-function-linux-custom-image.md)こともできます。 Linux ホスティングは現在プレビューの段階です。

App Service プランでは、関数アプリのスケーリングはお客様の責任において行われます。 Azure Functions のサーバーレス機能を利用するには、[従量課金プラン](functions-scale.md#consumption-plan)の Linux 上で関数をホストする方法もあります。

以下の手順は、Mac、Windows、または Linux コンピューターを使用して実行することができます。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次のものが必要です。

+ 有効な Azure サブスクリプション

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0.21 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Linux App Service プランの作成

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Linux で関数アプリを作成する

Linux での関数の実行をホストするための関数アプリが必要です。 関数アプリは、関数コードの実行環境を提供します。 Function App を使用すると、リソースの管理、デプロイ、共有を容易にするためのロジック ユニットとして関数をグループ化できます。 関数アプリの作成には、Linux App Service プランで [az functionapp create](/cli/azure/functionapp#az-functionapp-create) コマンドを使います。

次のコマンドでは、`<app_name>` プレースホルダーを一意の Function App 名で、`<storage_name>` をストレージ アカウント名で置き換えます。 `<app_name>` は、Function App の既定の DNS ドメインとして使用されます。そのため、名前は Azure のすべてのアプリ間で一意である必要があります。 また、関数アプリの `<language>` ランタイムを `dotnet` (C#)、`node` (JavaScript)、または `python` から設定する必要があります。

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --plan myAppServicePlan \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

関数アプリを作成してデプロイすると、Azure CLI によって次の例のような情報が表示されます。

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

`myAppServicePlan` は Linux プランなので、Linux 上で関数アプリを実行するコンテナーを作成するには、組み込みの docker イメージを使います。

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources-simple.md)]

## <a name="next-steps"></a>次の手順

この記事では、Linux でホストされた関数アプリを Azure 上で作成する方法について説明しました。 この関数アプリには、[関数プロジェクトをデプロイ](https://docs.microsoft.com/cli/azure/functionapp/deployment/source?view=azure-cli-latest)することができます。 Azure Functions Core Tools を使用すると、ローカル コンピューター上で [Functions プロジェクトを作成](functions-run-local.md#create-a-local-functions-project)し、それを新しい Linux 関数アプリにデプロイすることができます。  

> [!div class="nextstepaction"] 
> [Azure Functions をローカルでコーディングしてテストする](functions-run-local.md)

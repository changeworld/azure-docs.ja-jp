---
title: Azure CLI を使用して Linux で初めての関数を作成する (プレビュー) | Microsoft Docs
description: Azure CLI を使って既定の Linux イメージで実行する Azure 関数を初めて作成する方法について説明します。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: f4285b3e02176f6f734e6de4d02d3c9c26e5524c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429715"
---
# <a name="create-your-first-function-running-on-linux-using-the-azure-cli-preview"></a>Azure CLI を使用して Linux で実行する関数を初めて作成する (プレビュー)

Azure Functions を使うと、既定の Azure App Service コンテナー内の Linux で関数をホストできます。 [独自のカスタム コンテナーを利用する](functions-create-function-linux-custom-image.md)こともできます。 この機能は現在プレビュー段階であり、同じくプレビュー段階の [Functions 2.0 ランタイム](functions-versions.md)が必要です。

このクイックスタート トピックでは、Azure CLI で Azure Functions を使って、既定の App Service コンテナーでホストされている Linux で初めての関数アプリを作成する方法について説明します。 関数コード自体は、GitHub サンプル リポジトリからイメージにデプロイされます。    

次の手順は、Mac、Windows、または Linux コンピューターでサポートされます。 

## <a name="prerequisites"></a>前提条件 

このクイック スタートを完了するには、次のものが必要です。

+ 有効な Azure サブスクリプション

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0.21 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Linux App Service プランの作成

現在、Linux での Functions のホスティングは App Service プランでのみサポートされています。 従量課金プランでのホスティングはまだサポートされていません。 ホスティングについて詳しくは、「[Azure Functions のホスティング プランの比較](functions-scale.md)」をご覧ください。 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Linux で関数アプリを作成する

Linux での関数の実行をホストするための関数アプリが必要です。 関数アプリは、関数コードの実行環境を提供します。 Function App を使用すると、リソースの管理、デプロイ、共有を容易にするためのロジック ユニットとして関数をグループ化できます。 関数アプリの作成には、Linux App Service プランで [az functionapp create](/cli/azure/functionapp#az-functionapp-create) コマンドを使います。 

次のコマンドでは、`<app_name>` プレースホルダーを一意の Function App 名で、`<storage_name>` をストレージ アカウント名で置き換えます。 `<app_name>` は、Function App の既定の DNS ドメインとして使用されます。そのため、名前は Azure のすべてのアプリ間で一意である必要があります。 _deployment-source-url_ パラメーターは、HTTP によってトリガーされる "Hello World" 関数を含む GitHub のサンプル リポジトリです。

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-source-url https://github.com/Azure-Samples/functions-quickstart-linux
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

>[!NOTE]  
>現在、サンプル リポジトリには、2 つのスクリプト ファイル [deploy.sh](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/deploy.sh) と [.deployment](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/.deployment) が含まれます。 .deployment ファイルは、デプロイ プロセスに対し、[カスタム デプロイ スクリプト](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)として deploy.sh を使うように指示します。 現在のプレビュー リリースでは、Linux イメージに関数アプリをデプロイするにはスクリプトが必要です。  

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]

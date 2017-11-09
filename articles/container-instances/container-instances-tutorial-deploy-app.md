---
title: "Azure Container Instances チュートリアル - アプリのデプロイ"
description: "Azure Container Instances チュートリアル - アプリのデプロイ"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 3b651526f5ee3197e7d04accb6a87e2f10bf0791
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="deploy-a-container-to-azure-container-instances"></a>コンテナーを Azure Container Instances にデプロイする

これは 3 つのパートで構成されるチュートリアルの最後のタスクです。 前のセクションでは、[コンテナー イメージを作成](container-instances-tutorial-prepare-app.md)して、[Azure Container Registry にプッシュ](container-instances-tutorial-prepare-acr.md)しました。 このセクションで Azure Container Instances にコンテナーをデプロイして、このチュートリアルは完了です。 手順は次のとおりです。

> [!div class="checklist"]
> * Azure CLI を使用した Azure Container Registry からのコンテナーのデプロイ
> * ブラウザーでのアプリケーションの表示
> * コンテナー ログの表示

## <a name="before-you-begin"></a>開始する前に

このチュートリアルでは、Azure CLI バージョン 2.0.20 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。

このチュートリアルを完了するには、Docker 開発環境が必要です。 Docker では、[Mac](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、または [Linux](https://docs.docker.com/engine/installation/#supported-platforms) システムで Docker を簡単に構成できるパッケージが提供されています。

Azure Cloud Shell には、このチュートリアルの各ステップを完了するのに必要な Docker コンポーネントがすべて含まれているわけではありません。 そのため、Azure CLI および Docker 開発環境のローカル インストールをお勧めします。

## <a name="deploy-the-container-using-the-azure-cli"></a>Azure CLI を使用して、コンテナーをデプロイする

Azure CLI を使用すると、コンテナーを 1 つのコマンドで Azure Container Instances にデプロイできます。 コンテナー イメージはプライベートの Azure Container Registry でホストされているため、アクセスに必要な資格情報を含める必要があります。 必要に応じて、次のようにクエリを実行できます。

コンテナー レジストリ ログイン サーバー (レジストリ名で更新):

```azurecli
az acr show --name <acrName> --query loginServer
```

コンテナー レジストリ パスワード:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

1 CPU コアおよび 1 GB メモリのリソース要求で、コンテナー レジストリからコンテナー イメージをデプロイするには、次のコマンドを実行します。

```azurecli
az container create --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-password <acrPassword> --ip-address public -g myResourceGroup
```

数秒以内に、Azure Resource Manager から最初の応答を受信します。 デプロイの状態を表示するには、次を使用します。

```azurecli
az container show --name aci-tutorial-app --resource-group myResourceGroup --query state
```

状態が "*[保留中]*" から "*[実行中]*" に変更されるまで、このコマンドを実行し続けることができます。 その後、次に進むことができます。

## <a name="view-the-application-and-container-logs"></a>アプリケーションとコンテナー ログを表示する

デプロイに成功したら、ブラウザーを開いて、次のコマンドの出力に示されている IP アドレスにアクセスします。

```bash
az container show --name aci-tutorial-app --resource-group myResourceGroup --query ipAddress.ip
```

```json
"13.88.176.27"
```

![ブラウザーでの Hello World アプリ][aci-app-browser]

コンテナーのログ出力を表示することもできます。

```azurecli
az container logs --name aci-tutorial-app -g myResourceGroup
```

出力:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="next-steps"></a>次のステップ

このチュートリアルの、Azure Container Instances インスタンスへのコンテナーのデプロイ プロセスは完了です。 次の手順を完了しました。

> [!div class="checklist"]
> * Azure CLI を使用した Azure Container Registry からのコンテナーのデプロイ
> * ブラウザーでのアプリケーションの表示
> * コンテナー ログの表示

<!-- LINKS -->
[prepare-app]: ./container-instances-tutorial-prepare-app.md

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

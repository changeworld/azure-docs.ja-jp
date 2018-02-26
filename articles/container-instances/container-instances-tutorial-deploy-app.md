---
title: "Azure Container Instances チュートリアル - アプリのデプロイ"
description: "Azure Container Instances チュートリアル 3 / 3 - アプリケーションのデプロイ"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 250f74b1a05959b93000452c4d5f025311f379d8
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-a-container-to-azure-container-instances"></a>コンテナーを Azure Container Instances にデプロイする

これは、3 部構成のシリーズの最後のチュートリアルです。 シリーズではこれまで、[コンテナー イメージを作成](container-instances-tutorial-prepare-app.md)して、[Azure Container Registry にプッシュ](container-instances-tutorial-prepare-acr.md)しました。 この記事で Azure Container Instances にコンテナーをデプロイして、このチュートリアルのシリーズは完了です。

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Azure CLI を使用した Azure Container Registry からのコンテナーのデプロイ
> * ブラウザーでのアプリケーションの表示
> * コンテナー ログの表示

## <a name="before-you-begin"></a>開始する前に

このチュートリアルでは、Azure CLI バージョン 2.0.23 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール][azure-cli-install]」をご覧ください。

このチュートリアルを完了するには、Docker 開発環境がローカルにインストールされている必要があります。 Docker では、[Mac][docker-mac]、[Windows][docker-windows]、または [Linux][docker-linux] システムで Docker を簡単に構成できるパッケージが提供されています。

Azure Cloud Shell には、このチュートリアルの各ステップを完了するのに必要な Docker コンポーネントがすべて含まれているわけではありません。 このチュートリアルを完了するには、ローカル コンピューターに Azure CLI と Docker 開発環境をインストールする必要があります。

## <a name="deploy-the-container-using-the-azure-cli"></a>Azure CLI を使用して、コンテナーをデプロイする

Azure CLI を使用すると、コンテナーを 1 つのコマンドで Azure Container Instances にデプロイできます。 コンテナー イメージはプライベートの Azure Container Registry でホストされているため、アクセスに必要な資格情報を含める必要があります。 次の Azure CLI コマンドを使用して資格情報を取得します。

コンテナー レジストリ ログイン サーバー (レジストリ名で更新):

```azurecli
az acr show --name <acrName> --query loginServer
```

コンテナー レジストリ パスワード:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

1 CPU コアおよび 1 GB メモリのリソース要求で、コンテナー レジストリからコンテナー イメージをデプロイするには、次のコマンドを実行します。 `<acrLoginServer>` および `<acrPassword>` を、前の 2 つのコマンドから取得した値に置き換えます。 `<acrName>` を、コンテナー レジストリの名前に置き換えます。

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

数秒以内に、Azure Resource Manager から最初の応答を受信します。 `--dns-name-label` の値は、コンテナー インスタンスを作成する Azure リージョン内で一意である必要があります。 コマンドを実行したときに **DNS 名ラベル**のエラー メッセージが表示された場合、前の例の値を更新してください。

デプロイの状態を表示するには、[az container show][az-container-show] を使用します。

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

状態が *Pending* から *Running* に変わるまで、[az container show][az-container-show] コマンドを繰り返します。これには 1 分もかかりません。 コンテナーが *Running* になったら、次の手順に進みます。

## <a name="view-the-application-and-container-logs"></a>アプリケーションとコンテナー ログを表示する

デプロイが成功すると、[az container show][az-container-show] コマンドでコンテナーの完全修飾ドメイン名 (FQDN) が表示されます。

```bash
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

出力例: `"aci-demo.eastus.azurecontainer.io"`

実行中のアプリケーションを表示するには、お使いのブラウザーで表示された DNS 名に移動します。

![ブラウザーでの Hello World アプリ][aci-app-browser]

コンテナーのログ出力を表示することもできます。

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

出力:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアル シリーズで作成したリソースのいずれかが不要になった場合は、[az group delete][az-group-delete] コマンドを実行して、リソース グループとそこに含まれているすべてのリソースを削除できます。 このコマンドは、作成したコンテナー レジストリだけでなく、実行中のコンテナーと関連するすべてのリソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次の手順

このチュートリアルの、Azure Container Instances インスタンスへのコンテナーのデプロイ プロセスは完了です。 次の手順を完了しました。

> [!div class="checklist"]
> * Azure CLI を使用した Azure Container Registry からのコンテナーのデプロイ
> * ブラウザーでのアプリケーションの表示
> * コンテナー ログの表示

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md

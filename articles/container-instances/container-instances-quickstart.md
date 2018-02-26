---
title: "クイックスタート - 最初の Azure Container Instances コンテナーの作成"
description: "Azure Container Instances でのデプロイおよび作業の開始"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 02/20/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: d2d317d6c66aa0fb81779c3a8a192b6a50571d1f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2018
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Azure Container Instances での最初のコンテナーの作成
Azure Container Instances を使用すると、仮想マシンをプロビジョニングしたり、より高度なレベルのサービスを採用したりしなくても、Azure の Docker コンテナーを簡単に作成、管理できます。 このクイックスタートでは、Azure でコンテナーを作成し、パブリック IP アドレスを使用してインターネットに公開します。 この操作は、1 つのコマンドで完結します。 わずか数秒で、お使いのブラウザーに次のように表示されます。

![Azure Container Instances を使用してデプロイされたアプリのブラウザーでの表示][aci-app-browser]

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント][azure-account]を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure Cloud Shell または Azure CLI のローカル インストールを使用して、このクイック スタートを完了できます。 CLI をローカルにインストールして使用する場合、このクイックスタートを実施するには、Azure CLI バージョン 2.0.21 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール][azure-cli-install]」をご覧ください。

## <a name="create-a-resource-group"></a>リソース グループの作成

Azure Container Instances は、Azure のリソースであり、Azure リソース グループに配置する必要があります。Azure リソース グループとは、Azure のリソースがデプロイおよび管理される論理的なコレクションです。

[az group create][az-group-create] コマンドでリソース グループを作成します。

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>コンテナーを作成する

コンテナーを作成するには、名前、Docker イメージ、および Azure リソース グループを [az container create][az-container-create] コマンドに指定します。 必要に応じて、DNS 名ラベルを指定してコンテナーをインターネットに公開できます。 このクイック スタートでは、[Node.js][node-js] で記述された小型の Web アプリをホストするコンテナーをデプロイします。

次のコマンドを実行して、コンテナー インスタンスを開始します。 `--dns-name-label` の値は、インスタンスを作成する Azure リージョン内で一意である必要があります。そのため、場合によっては一意性を確保するためにこの値を変更する必要があります。

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/aci-helloworld --dns-name-label aci-demo --ports 80
```

数秒のうちに、要求に対する応答が得られます。 最初に、コンテナーは**作成中**の状態ですが、数秒のうちに起動されます。 [az container show][az-container-show] コマンドを使用して状態を確認することができます。

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

コマンドを実行すると、コンテナーの完全修飾ドメイン名 (FQDN) とそのプロビジョニング状態が表示されます。

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

コンテナーが**成功**状態に推移すると、FQDN に移動してお使いのブラウザー内でこのコンテナーにアクセスできます。

![Azure コンテナー インスタンスで実行されているアプリケーションを示すブラウザー スクリーンショット][aci-app-browser]

## <a name="pull-the-container-logs"></a>コンテナー ログを取り込む

[az container logs][az-container-logs] コマンドを使用して、作成したコンテナーのログを取り込むことができます。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

出力:

```bash
listening on port 80
::ffff:10.240.255.107 - - [29/Nov/2017:20:48:50 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36"
::ffff:10.240.255.107 - - [29/Nov/2017:20:48:50 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://52.224.178.107/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36"
```

## <a name="delete-the-container"></a>コンテナーを削除する

コンテナーを使い終えたら、[az container delete][az-container-delete] コマンドを使用してそのコンテナーを削除することができます。

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

コンテナーが削除されたことを確認するには、[az container list](/cli/azure/container#az_container_list) コマンドを実行します。

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

**mycontainer** コンテナーは、コマンドの出力に表示されません。 リソース グループに別のコンテナーがない場合、出力は表示されません。

## <a name="next-steps"></a>次の手順

このクイックスタートで使用したコンテナーのコードはすべて、その Dockerfile と共に [GitHub][app-github-repo] で入手できます。 Azure Container Registry を使用してビルドと Azure Container Instances へのデプロイを自分で試す場合は、Azure Container Instances のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Container Instances のチュートリアル](./container-instances-tutorial-prepare-app.md)

Azure のオーケストレーション システムでコンテナーを実行するためのオプションを試すには、[Service Fabric][ service-fabric] または [Azure Container Service (AKS)][ container-service] のクイック スタートを参照してください。

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[node-js]: http://nodejs.org

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group?view=azure-cli-latest#az_group_create
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-delete]: /cli/azure/container?view=azure-cli-latest#az_container_delete
[az-container-list]: /cli/azure/container?view=azure-cli-latest#az_container_list
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md

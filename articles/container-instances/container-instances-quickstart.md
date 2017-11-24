---
title: "クイックスタート - 最初の Azure Container Instances コンテナーの作成"
description: "Azure Container Instances でのデプロイおよび作業の開始"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 160ba84d2c022ca3af2eb13a9689a282b4a6b198
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Azure Container Instances での最初のコンテナーの作成
Azure Container Instances を使用すると、仮想マシンをプロビジョニングしたり、より高度なレベルのサービスを採用したりしなくても、Azure の Docker コンテナーを簡単に作成、管理できます。 このクイックスタートでは、Azure でコンテナーを作成し、パブリック IP アドレスを使用してインターネットに公開します。 この操作は、1 つのコマンドで完結します。 わずか数秒で、お使いのブラウザーに次のように表示されます。

![Azure Container Instances を使用してデプロイされたアプリのブラウザーでの表示][aci-app-browser]

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure Cloud Shell または Azure CLI のローカル インストールを使用して、このクイック スタートを完了できます。 CLI をローカルにインストールして使用する場合、このクイックスタートを実施するには、Azure CLI バージョン 2.0.21 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成

Azure Container Instances は、Azure のリソースであり、Azure リソース グループに配置する必要があります。Azure リソース グループとは、Azure のリソースがデプロイおよび管理される論理的なコレクションです。

[az group create][az-group-create] コマンドでリソース グループを作成します。

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>コンテナーを作成する

コンテナーを作成するには、名前、Docker イメージ、および Azure リソース グループを [az container create][az-container-create] コマンドに指定します。 コンテナーは、必要に応じて、パブリック IP アドレスを使用してインターネットに公開できます。 このクイック スタートでは、[Node.js](http://nodejs.org) で記述された小型の Web アプリをホストするコンテナーをデプロイします。

```azurecli-interactive
az container create --name mycontainer --image microsoft/aci-helloworld --resource-group myResourceGroup --ip-address public --ports 80
```

数秒のうちに、要求に対する応答が得られます。 最初に、コンテナーは**作成中**の状態ですが、数秒のうちに起動されます。 [az container show][az-container-show] コマンドを使用して状態を確認することができます。

```azurecli-interactive
az container show --name mycontainer --resource-group myResourceGroup
```

出力の下部に、コンテナーのプロビジョニングの状態とその IP アドレスが表示されます。

```json
...
"ipAddress": {
      "ip": "13.88.176.27",
      "ports": [
        {
          "port": 80,
          "protocol": "TCP"
        }
      ]
    },
    "osType": "Linux",
    "provisioningState": "Succeeded"
...
```

コンテナーが**成功**状態に推移すると、指定した IP アドレスを使用してお使いのブラウザー内でこのコンテナーにアクセスすることができます。

![Azure Container Instances を使用してデプロイされたアプリのブラウザーでの表示][aci-app-browser]

## <a name="pull-the-container-logs"></a>コンテナー ログを取り込む

[az container logs][az-container-logs] コマンドを使用して、作成したコンテナーのログを取り込むことができます。

```azurecli-interactive
az container logs --name mycontainer --resource-group myResourceGroup
```

出力:

```bash
Server running...
10.240.255.107 - - [20/Nov/2017:19:16:28 +0000] "GET / HTTP/1.1" 200 1663 "" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36"
10.240.255.107 - - [20/Nov/2017:19:16:28 +0000] "GET / HTTP/1.1" 200 1663
10.240.255.107 - - [20/Nov/2017:19:16:28 +0000] "GET /favicon.ico HTTP/1.1" 404 19
```

## <a name="delete-the-container"></a>コンテナーを削除する

コンテナーを使い終えたら、[az container delete][az-container-delete] コマンドを使用してそのコンテナーを削除することができます。

```azurecli-interactive
az container delete --name mycontainer --resource-group myResourceGroup
```

コンテナーが削除されたことを確認するには、[az container list](/cli/azure/container#az_container_list) コマンドを実行します。

```azurecli-interactive
az container list --resource-group myResourceGroup -o table
```

**mycontainer** コンテナーは、コマンドの出力に表示されません。 リソース グループに別のコンテナーがない場合、出力は表示されません。

## <a name="next-steps"></a>次のステップ

このクイックスタートで使用したコンテナーのコードはすべて、その Dockerfile と共に [GitHub][app-github-repo] で入手できます。 Azure Container Registry を使用してビルドと Azure Container Instances へのデプロイを自分で試す場合は、Azure Container Instances のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Container Instances のチュートリアル](./container-instances-tutorial-prepare-app.md)

Azure のオーケストレーション システムでコンテナーを実行するためのオプションを試すには、[Service Fabric][ service-fabric] または [Azure Container Service (AKS)][ container-service] のクイック スタートを参照してください。

<!-- LINKS -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[az-group-create]: /cli/azure/group?view=azure-cli-latest#az_group_create
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-delete]: /cli/azure/container?view=azure-cli-latest#az_container_delete
[az-container-list]: /cli/azure/container?view=azure-cli-latest#az_container_list
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
[container-service]: ../aks/kubernetes-walkthrough.md


<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

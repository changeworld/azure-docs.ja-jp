---
title: クイック スタート - Azure Container Instances でアプリケーションを実行する - CLI
description: このクイック スタートでは、Azure CLI を使用して、Azure Container Instances の分離されたコンテナーで実行されるように Docker コンテナー アプリケーションをデプロイします
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 70d1bc9003d98f0154b9f38738f1b8e82b0c506d
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53189610"
---
# <a name="quickstart-run-a-container-application-in-azure-container-instances-with-the-azure-cli"></a>クイック スタート:Azure CLI を使って Azure Container Instances でコンテナー アプリケーションを実行する

Docker コンテナーを Azure で簡単にすばやく実行するには、Azure Container Instances を使用します。 仮想マシンをデプロイしたり、Kubernetes などの完全なコンテナー オーケストレーション プラットフォームを使用したりする必要はありません。 このクイック スタートでは、Azure CLI を使用して Azure でコンテナーを作成し、そのアプリケーションを完全修飾ドメイン名 (FQDN) を介して使用できるようにします。 1 つのデプロイ コマンドを実行して数秒後には、実行中のアプリケーションを閲覧できます。

![Azure Container Instances にデプロイされたアプリのブラウザーでの表示][aci-app-browser]

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント][azure-account]を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure Cloud Shell または Azure CLI のローカル インストールを使用して、このクイック スタートを完了できます。 Azure CLI をローカルで使用する場合は、バージョン 2.0.27 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成

Azure のコンテナー インスタンスは、すべての Azure リソースと同様に、リソース グループにデプロイする必要があります。 リソース グループを使用すると、関連する Azure リソースを整理して管理できます。

まず、次の [az group create][az-group-create] コマンドを使用して、*myResourceGroup* という名前のリソース グループを *eastus* の場所に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>コンテナーを作成する

リソース グループを作成すると、Azure でコンテナーを実行できます。 Azure CLI を使用してコンテナー インスタンスを作成するには、リソース グループ名、コンテナー インスタンス名、および Docker コンテナー イメージを [az container create][az-container-create] コマンドに渡します。 1 つまたは複数の開くポート、DNS 名ラベル、またはその両方を指定することで、コンテナーをインターネットに公開することができます。 このクイック スタートでは、DNS 名ラベルを指定して、Node.js で記述された小型の Web アプリをホストするコンテナーをデプロイします。

次のコマンドを実行して、コンテナー インスタンスを開始します。 `--dns-name-label` の値は、インスタンスを作成する Azure リージョン内で一意である必要があります。 エラー メッセージ "DNS 名ラベルは利用できません" が表示された場合は、別の DNS 名ラベルを試してください。

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/aci-helloworld --dns-name-label aci-demo --ports 80
```

数秒以内に、Azure CLI からデプロイが完了したことを示す応答を受信します。 [az container show][az-container-show] コマンドを使用して、コンテナーの状態を確認します。

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

コンテナーの `ProvisioningState` が **[Succeeded]\(成功\)** と表示されている場合は、ブラウザーでその FQDN に移動します。 次のような Web ページが表示されたら成功です。 Docker コンテナーで実行されているアプリケーションが Azure に正常にデプロイされました。

![Azure コンテナー インスタンスで実行されているアプリケーションを示すブラウザー スクリーンショット][aci-app-browser]

最初にアプリケーションが表示されない場合は、DNS が反映されるまで数秒待った後、ブラウザーを更新してみてください。

## <a name="pull-the-container-logs"></a>コンテナー ログを取り込む

コンテナーまたはコンテナーで実行されているアプリケーションのトラブルシューティングを行うことが必要になった場合 (またはその出力を確認するだけの場合) は、コンテナー インスタンスのログを表示することから始めます。

[az container logs][az-container-logs] コマンドを使用して、コンテナー インスタンスのログをプルします。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

出力はコンテナーのログを表示し、ブラウザーでアプリケーションを表示したときに生成された HTTP GET 要求が表示されます。

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
listening on port 80
::ffff:10.240.255.105 - - [01/Oct/2018:18:25:51 +0000] "GET / HTTP/1.0" 200 1663 "-" "-"
::ffff:10.240.255.106 - - [01/Oct/2018:18:31:04 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36"
::ffff:10.240.255.106 - - [01/Oct/2018:18:31:04 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36"
```

## <a name="attach-output-streams"></a>出力ストリームのアタッチ

ログを表示するだけでなく、ローカルの標準出力ストリームと標準エラー ストリームをコンテナーのストリームにアタッチすることができます。

最初に [az container attach][az-container-attach] コマンドを実行して、ローカルのコンソールをコンテナーの出力ストリームにアタッチします。

```azurecli-interactive
az container attach --resource-group myResourceGroup -n mycontainer
```

アタッチしたら、ブラウザーを数回更新して、追加の出力を生成します。 完了したら、`Control+C` キーを押してコンソールをデタッチします。 次のような出力が表示されます。

```console
$ az container attach --resource-group myResourceGroup -n mycontainer
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-15 21:17:59+00:00) pulling image "microsoft/aci-helloworld"
(count: 1) (last timestamp: 2018-03-15 21:18:05+00:00) Successfully pulled image "microsoft/aci-helloworld"
(count: 1) (last timestamp: 2018-03-15 21:18:05+00:00) Created container with id 3534a1e2ee392d6f47b2c158ce8c1808d1686fc54f17de3a953d356cf5f26a45
(count: 1) (last timestamp: 2018-03-15 21:18:06+00:00) Started container with id 3534a1e2ee392d6f47b2c158ce8c1808d1686fc54f17de3a953d356cf5f26a45

Start streaming logs:
listening on port 80
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.107 - - [15/Mar/2018:21:18:44 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.107 - - [15/Mar/2018:21:18:47 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

コンテナーを使い終えたら、[az container delete][az-container-delete] コマンドを使用して、そのコンテナーを削除します。

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

コンテナーが削除されたことを確認するには、[az container list](/cli/azure/container#az-container-list) コマンドを実行します。

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

**mycontainer** コンテナーは、コマンドの出力に表示されません。 リソース グループに別のコンテナーがない場合、出力は表示されません。

*myResourceGroup* リソース グループとそこに含まれるすべてのリソースの操作が終了したら、[az group delete][az-group-delete] コマンドを使用してそれを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、パブリック Docker Hub レジストリ内のイメージを使用して Azure コンテナー インスタンスを作成しました。 コンテナー イメージをビルドし、プライベート Azure コンテナー レジストリからデプロイする場合は、Azure Container Instances のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Container Instances のチュートリアル](./container-instances-tutorial-prepare-app.md)

Azure のオーケストレーション システムでコンテナーを実行するためのオプションを試すには、[Service Fabric][service-fabric] または [Azure Kubernetes Service (AKS)][container-service] のクイック スタートを参照してください。

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: http://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-container-list]: /cli/azure/container#az-container-list
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md

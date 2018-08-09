---
title: Docker イメージをプライベート Azure レジストリにプッシュする
description: Docker CLI を使用した、Azure のプライベート コンテナー レジストリに対する Docker イメージのプッシュとプル
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 11/29/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 24cccd4745d611196046168f0125e7ef2a184e15
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576493"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Docker CLI を使用してプライベート Docker コンテナー レジストリに最初のイメージをプッシュする

[Docker Hub](https://hub.docker.com/) で公開 Docker イメージを格納するように、Azure コンテナー レジストリではプライベート [Docker](http://hub.docker.com) コンテナー イメージを格納および管理します。 コンテナー レジストリに対する[ログイン](https://docs.docker.com/engine/reference/commandline/login/)、[プッシュ](https://docs.docker.com/engine/reference/commandline/push/)、[プル](https://docs.docker.com/engine/reference/commandline/pull/)などの操作には、[Docker コマンド ライン インターフェイス](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) を使用できます。

以下の手順では、公式の [Nginx イメージ](https://store.docker.com/images/nginx)を公開 Docker Hub レジストリからダウンロードし、プライベート Azure コンテナー レジストリにタグ付けしてレジストリにプッシュした後、レジストリからもう一度プルします。

## <a name="prerequisites"></a>前提条件

* **Azure コンテナー レジストリ** - コンテナー レジストリは、Azure サブスクリプションに作成します。 たとえば、[Azure Portal](container-registry-get-started-portal.md) または [Azure CLI](container-registry-get-started-azure-cli.md) を使用します。
* **Docker CLI** - ローカル コンピューターを Docker ホストとして設定し、Docker CLI コマンドにアクセスするには、[Docker](https://docs.docker.com/engine/installation/) をインストールします。

## <a name="log-in-to-a-registry"></a>レジストリへのログイン

プライベート コンテナー レジストリで[認証するさまざまな方法](container-registry-authentication.md)があります。 コマンド ラインで作業するときに推奨される方法は、Azure CLI コマンドの [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login)を使用することです。 たとえば、*myregistry* という名前のレジストリにログインするには、次のように入力します。

```azurecli
az acr login --name myregistry
```

[docker login](https://docs.docker.com/engine/reference/commandline/login/) でログインすることもできます。 次の例では、Azure Active Directory [サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)の ID とパスワードを渡します。 たとえば、オートメーション シナリオで、[レジストリにサービス プリンシパルを割り当てる](container-registry-authentication.md#service-principal)ことができます。

```Bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

どちらのコマンドも、完了すると `Login Succeeded` が返されます。 `docker login` を使用した場合は、`--password-stdin` パラメーターの使用を推奨するセキュリティ警告が表示されることもあります。 このパラメーターの使用について、ここでは説明していませんが、このベスト プラクティスに従うことをお勧めします。 詳細については、[docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドのリファレンスを参照してください。

> [!TIP]
> `docker login` を使用する場合とレジストリにプッシュするために画像にタグ付けする場合は、常にレジストリの完全修飾名 (すべて小文字) を指定してください。 この記事の例では、完全修飾名は *myregistry.azurecr.io* です。

## <a name="pull-the-official-nginx-image"></a>公式の Nginx イメージをプルする

まず、公開 Nginx イメージをローカル コンピューターにプルします。

```Bash
docker pull nginx
```

## <a name="run-the-container-locally"></a>コンテナーをローカルで実行する

次の [docker run](https://docs.docker.com/engine/reference/run/) コマンドを実行して、Nginx コンテナーのローカル インスタンスを 対話形式でポート 8080 で起動します (`-it`)。 `--rm` 引数は、コンテナーが停止されたときに、それを削除するように指定します。

```Bash
docker run -it --rm -p 8080:80 nginx
```

[http://localhost:8080](http://localhost:8080) に移動して、実行中のコンテナーの Nginx によって提供される既定の Web ページを表示します。 次のようなページが表示されます。

![ローカル コンピューター上の Nginx](./media/container-registry-get-started-docker-cli/nginx.png)

`-it` を指定して対話形式でコンテナーを開始したため、ブラウザーでそれに移動すると、コマンド ラインに Nginx サーバーの出力が表示されます。

コンテナーを停止して削除するには、`Control`+`C` を押します。

## <a name="create-an-alias-of-the-image"></a>イメージのエイリアスを作成する

[docker tag](https://docs.docker.com/engine/reference/commandline/tag/) でレジストリへの完全修飾パスを使用して、イメージのエイリアスを作成します。 この例では、レジストリのルートが煩雑にならないように、`samples` 名前空間を指定しています。

```Bash
docker tag nginx myregistry.azurecr.io/samples/nginx
```

名前空間を持つタグ付けの詳細については、「[Azure Container Registry のベスト プラクティス](container-registry-best-practices.md)」の「[リポジトリの名前空間](container-registry-best-practices.md#repository-namespaces)」セクションを参照してください。

## <a name="push-the-image-to-your-registry"></a>イメージをレジストリにプッシュする

これで、完全修飾パスを使用してイメージにプライベート レジストリへのタグが付けられたので、[docker push](https://docs.docker.com/engine/reference/commandline/push/) を使用してレジストリにプッシュできます。

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>レジストリからイメージをプルする

レジストリからイメージをプルするには、[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用します。

```Bash
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Nginx コンテナーを起動する

レジストリからプルしたイメージを実行するには、[docker run](https://docs.docker.com/engine/reference/run/) コマンドを使用します。

```Bash
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

ブラウザーで [http://localhost:8080](http://localhost:8080) に移動して、実行中のコンテナーを表示します。

コンテナーを停止して削除するには、`Control`+`C` を押します。

## <a name="remove-the-image-optional"></a>イメージを削除する (任意指定)

Nginx イメージが不要になった場合は、[docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) コマンドを使用して、ローカルに削除できます。

```Bash
docker rmi myregistry.azurecr.io/samples/nginx
```

Azure コンテナー レジストリからイメージを削除するには、Azure CLI コマンド [az acr repository delete](/cli/azure/acr/repository#az-acr-repository-delete) を使用できます。 たとえば、次のコマンドは、タグ、タグによって参照されるマニフェスト、関連付けられているレイヤー データ、およびマニフェストを参照するその他すべてのタグを削除します。

```azurecli
az acr repository delete --name myregistry --repository samples/nginx --tag latest --manifest
```

## <a name="next-steps"></a>次の手順

基本を理解したので、レジストリの使用を開始する準備ができました。 レジストリから次の宛先にコンテナー イメージをデプロイします。

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

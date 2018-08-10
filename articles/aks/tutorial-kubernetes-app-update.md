---
title: Kubernetes on Azure のチュートリアル - アプリケーションの更新
description: AKS チュートリアル - アプリケーションの更新
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/24/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 2fcb2f5041b97b7e267f55340bf0cb0b8d2f457b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449385"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>チュートリアル: Azure Kubernetes Service (AKS) でのアプリケーションの更新

Kubernetes でアプリケーションをデプロイした後で、新しいコンテナー イメージまたはイメージ バージョンを指定することによってアプリケーションを更新できます。 アプリケーションを更新するときは、デプロイの一部だけが同時に更新されるように、更新がステージングされます。 この段階的な更新プログラムを使用すると、アプリケーションの更新中も引き続きアプリケーションを実行することができます。 デプロイ エラーが発生した場合のロールバック メカニズムも提供されています。

この 7 部構成の 6 番目のチュートリアルでは、サンプルの Azure Vote アプリを更新します。 以下のタスクを行います。

> [!div class="checklist"]
> * フロントエンド アプリケーション コードの更新
> * 更新後のコンテナー イメージの作成
> * Azure Container Registry へのコンテナー イメージのプッシュ
> * 更新したコンテナー イメージのデプロイ

## <a name="before-you-begin"></a>開始する前に

これまでのチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化し、このイメージを Azure Container Registry にアップロードして、Kubernetes クラスターを作成しました。 その後、Kubernetes クラスターでアプリケーションを実行しました。

アプリケーション リポジトリも複製しましたが、それにはアプリケーションのソース コードと、このチュートリアルで使用する事前作成された Docker Compose ファイルが含まれています。 リポジトリの複製が作成されていること、およびディレクトリが複製されたディレクトリに変更されていることを確認します。 内部には、`azure-vote` という名前のディレクトリと `docker-compose.yaml` という名前のファイルがあります。

これらの手順を実行していない場合で、順番に進めたい場合は、「[チュートリアル 1 – コンテナー イメージを作成する][aks-tutorial-prepare-app]」に戻ってください。

## <a name="update-application"></a>アプリケーションを更新する

このチュートリアルでは、アプリケーションを変更し、更新したアプリケーションを Kubernetes クラスターにデプロイします。

アプリケーションのソース コードは、`azure-vote` ディレクトリ内にあります。 適当なコード エディターまたはテキスト エディターで `config_file.cfg` ファイルを開きます。 この例では、 `vi` が使用されます。

```console
vi azure-vote/azure-vote/config_file.cfg
```

`VOTE1VALUE` と `VOTE2VALUE` の値を変更して、ファイルを保存します。

```console
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

ファイルを保存して閉じます。

## <a name="update-container-image"></a>コンテナー イメージの更新

[docker-compose][docker-compose] を使用してフロントエンド イメージを再作成し、更新したアプリケーションを実行します。 引数 `--build` は、Docker Compose にアプリケーション イメージの再作成を指示するために使用されます。

```console
docker-compose up --build -d
```

## <a name="test-application-locally"></a>ローカルでアプリケーションをテストする

http://localhost:8080 に移動し、更新したアプリケーションを確認します。

![Azure 上の Kubernetes クラスターの図](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>イメージにタグを付けてプッシュする

コンテナー レジストリの loginServer で `azure-vote-front` イメージにタグを付けます。

[az acr list](/cli/azure/acr#az-acr-list) コマンドを使用して、ログイン サーバー名を取得します。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

[docker tag][docker-tag] を使用してイメージにタグを付けます。 `<acrLoginServer>` を、Azure Container Registry のログイン サーバー名またはパブリック レジストリのホスト名で置き換えます。 また、イメージのバージョンは `v2` に更新されます。

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

[docker push][docker-push] を使用してレジストリにイメージをアップロードします。 `<acrLoginServer>` を Azure Container Registry のログイン サーバー名に置き換えます。 ACR レジストリにプッシュする際に問題が発生する場合は、[az acr login][az-acr-login] コマンドを実行していることを確認してください。

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-update-application"></a>更新したアプリケーションをデプロイする

最大限のアップタイムを保証するには、アプリケーション ポッドの複数のインスタンスを実行する必要があります。 [kubectl get pod][kubectl-get] コマンドでこの構成を確認します。

```
kubectl get pod
```

出力:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

azure-vote-front イメージを複数のポッドで実行していない場合は、`azure-vote-front` のデプロイを拡張します。


```azurecli
kubectl scale --replicas=3 deployment/azure-vote-front
```

アプリケーションを更新するには、[kubectl set][kubectl-set] コマンドを使用します。 コンテナー レジストリのログイン サーバー名またはホスト名で、`<acrLoginServer>` を更新します。

```azurecli
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

デプロイを監視するには、[kubectl get pod][kubectl-get] コマンドを使います。 更新されたアプリケーションがデプロイされると、ポッドが終了されて、新しいコンテナー イメージで再作成されます。

```azurecli
kubectl get pod
```

出力:

```
NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-updated-application"></a>更新されたアプリケーションをテストする

`azure-vote-front` サービスの外部 IP アドレスを取得します。

```azurecli
kubectl get service azure-vote-front
```

その IP アドレスに移動し、更新したアプリケーションを確認します。

![Azure 上の Kubernetes クラスターの図](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、アプリケーションを更新し、この更新を Kubernetes クラスターにロールアウトしました。 次のタスクを完了しました。

> [!div class="checklist"]
> * フロントエンド アプリケーション コードの更新
> * 更新後のコンテナー イメージの作成
> * Azure Container Registry へのコンテナー イメージのプッシュ
> * 更新したアプリケーションのデプロイ

次のチュートリアルに進んで、Kubernetes の新しいバージョンへのアップグレードについて学習してください。

> [!div class="nextstepaction"]
> [Kubernetes のアップグレード][aks-tutorial-upgrade]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az-acr-login
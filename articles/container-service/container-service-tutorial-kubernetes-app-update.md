---
title: "Azure Container Service チュートリアル - アプリケーションの更新 | Microsoft Docs"
description: "Azure Container Service チュートリアル - アプリケーションの更新"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: ce4a88a7958116890ec17eb2405ba809487b9c0f
ms.contentlocale: ja-jp
ms.lasthandoff: 07/06/2017

---

# <a name="update-an-application-in-kubernetes"></a>Kubernetes でアプリケーションを更新する

Kubernetes でアプリケーションをデプロイした後は、新しいコンテナー イメージまたはイメージ バージョンを指定することによってアプリケーションを更新できます。 アプリケーションを更新するときは、デプロイの一部だけが同時に更新されるように、更新のロールアウトがステージングされます。 

このステージングされた更新により、アプリケーションは更新中も実行を続けることができ、デプロイで問題が発生した場合のロールバック メカニズムが提供されます。 

このチュートリアルでは、サンプルの Azure Vote アプリを更新します。 以下のタスクを行います。

> [!div class="checklist"]
> * フロントエンド アプリケーション コードの更新。
> * 更新されるコンテナー イメージの作成。
> * Azure Container Registry へのコンテナー イメージのプッシュ。
> * 更新されたアプリケーションのデプロイ。

## <a name="before-you-begin"></a>開始する前に

前のチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化し、イメージを Azure Container Registry にアップロードして、Kubernetes クラスターを作成しました。 その後、Kubernetes クラスターでアプリケーションを実行しました。 

これらの手順を実行していない場合で、今行いたい場合は、「[チュートリアル 1 – コンテナー イメージを作成する](./container-service-tutorial-kubernetes-prepare-app.md)」に戻ってください。 

このチュートリアルでは、少なくとも実行中のアプリケーションがある Kubernetes クラスターが必要です。

## <a name="update-application"></a>アプリケーションを更新する

このチュートリアルの手順を実行するには、Azure Vote アプリケーションのコピーを複製しておく必要があります。 必要な場合は、次のコマンドでこの複製コピーを作成します。

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

適当なコード エディターまたはテキスト エディターで `config_file.cfg` ファイルを開きます。 このファイルは、複製されたリポジトリの次のディレクトリにあります。

```bash
 /azure-voting-app/azure-vote/azure-vote/config_file.cfg
```

`VOTE1VALUE` と `VOTE2VALUE` の値を変更して、ファイルを保存します。

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Half Full'
VOTE2VALUE = 'Half Empty'
SHOWHOST = 'false'
```

`docker build` を使って、フロントエンドのイメージを再作成します。

```bash
docker build --no-cache ./azure-voting-app/azure-vote -t azure-vote-front:v2
```

## <a name="test-application"></a>アプリケーションをテストする

Docker ネットワークを作成します。 このネットワークは、コンテナー間の通信に使われます。  

```bash
docker network create azure-vote
```

`docker run` コマンドを使って、バックエンド コンテナー イメージのインスタンスを実行します。

```bash
docker run -p 3306:3306 --name azure-vote-back -d --network azure-vote -e MYSQL_ROOT_PASSWORD=Password12 -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote azure-vote-back 
```

フロントエンド コンテナー イメージのインスタンスを実行します。

```bash
docker run -d -p 8080:80 --name azure-vote-front --network=azure-vote -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote -e MYSQL_HOST=azure-vote-back azure-vote-front:v2
```

`http://localhost:8080` に移動し、更新されたアプリケーションを確認します。 アプリケーションの初期化に数秒かかります。 エラーが発生した場合は、もう一度やり直してください。

![Azure 上の Kubernetes クラスターの図](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>イメージにタグを付けてプッシュする

コンテナー レジストリの loginServer で *azure-vote-front* イメージにタグを付けます。

Azure Container Registry を使っている場合は、[az acr list](/cli/azure/acr#list) コマンドでログイン サーバー名を取得します。

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

[docker tag](https://docs.docker.com/engine/reference/commandline/tag/) を使ってイメージにタグを付けます。忘れずに、実際の Azure Container Registry ログイン サーバー名またはパブリック レジストリのホスト名でコマンドを更新してください。

```bash
docker tag azure-vote-front:v2 <acrLoginServer>/azure-vote-front:v2
```

レジストリにイメージをプッシュします。 `<acrLoginServer>` を、Azure Container Registry のログイン サーバー名またはパブリック レジストリのホスト名で置き換えます。

```bash
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-update-to-kubernetes"></a>Kubernetes に更新をデプロイする

### <a name="verify-multiple-pod-replicas"></a>複数の POD レプリカを確認する

最大限のアップタイムを保証するには、アプリケーション ポッドの複数のインスタンスを実行する必要があります。 [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) コマンドでこの構成を確認します。

```bash
kubectl get pod
```

出力:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

azure-vote-front イメージを複数のポッドで実行していない場合は、*azure-vote-front* のデプロイを拡張します。


```bash
kubectl scale --replicas=3 deployment/azure-vote-front
```

### <a name="update-application"></a>アプリケーションを更新する

アプリケーションを更新するには、次のコマンドを実行します。 コンテナー レジストリのログイン サーバー名またはホスト名で、`<acrLoginServer>` を更新します。

```bash
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

デプロイを監視するには、[kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) コマンドを使います。 更新されたアプリケーションがデプロイされると、ポッドが終了されて、新しいコンテナー イメージで再作成されます。

```bash
kubectl get pod
```

出力:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>更新されたアプリケーションをテストする

*azure-vote-front* サービスの外部 IP アドレスを取得します。

```bash
kubectl get service
```

その IP アドレスに移動し、更新されたアプリケーションを確認します。

![Azure 上の Kubernetes クラスターの図](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、アプリケーションを更新し、この更新を Kubernetes クラスターにロールアウトしました。 次のタスクを行いました。  

> [!div class="checklist"]
> * フロントエンド アプリケーション コードを更新しました。
> * 更新されるコンテナー イメージを作成しました。
> * Azure Container Registry にコンテナー イメージをプッシュしました。
> * 更新されたアプリケーションをデプロイしました。

次のチュートリアルに進み、Operations Management Suite で Kubernetes を監視する方法について学習してください。

> [!div class="nextstepaction"]
> [OMS で Kubernetes を監視する](./container-service-tutorial-kubernetes-monitor.md)

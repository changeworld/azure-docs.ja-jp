---
title: "Kubernetes on Azure のチュートリアル - クラスターのデプロイ"
description: "AKS チュートリアル - クラスターのデプロイ"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: bb8ad6d9defcbaef255065b20a9a9b542e74d73d
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>Azure Container Service (AKS) クラスターのデプロイ

Kubernetes には、コンテナー化されたアプリケーション用の分散プラットフォームが用意されています。 AKS を使うと、運用開始準備の整った Kubernetes クラスターのプロビジョニングを簡単かつ迅速に行うことができます。 このチュートリアル (8 部構成の第 3 部) では、Kubernetes クラスターが AKS にアップグレードされます。 手順は次のとおりです。

> [!div class="checklist"]
> * Kubernetes AKS クラスターのデプロイ
> * Kubernetes CLI (kubectl) のインストール
> * kubectl の構成

この後のチュートリアルでは、Azure Vote アプリケーションをクラスターにデプロイしてから拡張および更新し、Operations Management Suite を Kubernetes クラスターを監視するように構成します。

## <a name="before-you-begin"></a>開始する前に

これまでのチュートリアルでは、コンテナー イメージを作成して、Azure Container Registry インスタンスにアップロードしました。 これらの手順を完了しておらず、手順を実行する場合は、「[チュートリアル 1 - コンテナー イメージを作成する][aks-tutorial-prepare-app]」に戻ってください。

## <a name="enable-aks-preview"></a>AKS プレビューの有効化

AKS がプレビューである間、新しいクラスターを作成するには、サブスクリプションに機能フラグが必要です。 使用する任意の数のサブスクリプションに対して、この機能を要求することができます。 AKS プロバイダーを登録するには、次のように `az provider register` コマンドを使用します。

```azurecli
az provider register -n Microsoft.ContainerService
```

登録すると、AKS で Kubernetes クラスターを作成できるようになります。

## <a name="create-kubernetes-cluster"></a>Kubernetes クラスターを作成する

次の例では、`myAKSCluster` という名前のクラスターを `myResourceGroup` という名前のリソース グループに作成します。 このリソース グループは、[前のチュートリアル][aks-tutorial-prepare-acr]で作成しました。

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

数分してデプロイが完了すると、この AKS デプロイに関する情報が JSON 形式で表示されます。

```azurecli
{
  "additionalProperties": {},
  "agentPoolProfiles": [
    {
      "additionalProperties": {},
      "count": 1,
      "dnsPrefix": null,
      "fqdn": null,
      "name": "nodepool1",
      "osDiskSizeGb": null,
      "osType": "Linux",
      "ports": null,
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
      "vnetSubnetId": null
    }
    ...
```

## <a name="getting-information-about-your-cluster"></a>クラスターに関する情報を取得する

クラスターがデプロイされた後は、`az aks show` を使用してクラスターを照会し、重要な情報を取得することができます。 このデータは、より複雑な操作をクラスターに対して実行するときに、パラメーターとして使用することができます。 たとえば、クラスターで実行されている Linux プロファイルについての情報を知りたい場合、次のコマンドを実行します。

```azurecli
az aks show --name myAKSCluster --resource-group myResourceGroup --query "linuxProfile"

{
  "additionalProperties": {},
  "adminUsername": "azureuser",
  "ssh": {
    "additionalProperties": {},
    "publicKeys": [
      {
        "additionalProperties": {},
        "keyData": "ssh-rsa AAAAB3NzaC1yc2EAAAADA...
      }
    ]
  }
}
```

このコマンドを実行すると、管理者ユーザーと SSH 公開キーに関する情報が表示されます。 また、次のように、JSON のプロパティをクエリ文字列に追加することで、さらに詳しいクエリを実行することもできます。

```azurecli
az aks show -n myakscluster  -g my-group --query "{name:agentPoolProfiles[0].name, nodeCount:agentPoolProfiles[0].count}"
{
  "name": "nodepool1",
  "nodeCount": 1
}
```
これを利用すれば、デプロイされているクラスターについてのデータにすばやくアクセスすることができます。 JMESPath のクエリについて詳しくは、[こちら](http://jmespath.org/tutorial.html)をご覧ください。

## <a name="install-the-kubectl-cli"></a>kubectl CLI をインストールする

クライアント コンピューターから Kubernetes クラスターに接続するには、[kubectl][kubectl] (Kubernetes コマンドライン クライアント) を使用します。

Azure CloudShell を使用している場合、kubectl は既にインストールされています。 ローカルにインストールする場合は、次のコマンドを実行します。

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>kubectl を使用して接続する

Kubernetes クラスターに接続するように kubectl を構成するには、次のコマンドを実行します。

```azurecli
az aks get-credentials --resource-group=myResourceGroup --name=myAKSCluster
```

クラスターへの接続を確認するには、[kubectl get nodes][kubectl-get] コマンドを実行します。

```azurecli
kubectl get nodes
```

出力:

```
NAME                          STATUS    AGE       VERSION
k8s-myAKSCluster-36346190-0   Ready     49m       v1.7.9
```

チュートリアルが完了し、AKS クラスターはワークロードで使用できるようになりました。 後のチュートリアルでは、マルチコンテナー アプリケーションのクラスターへのデプロイ、スケールアウト、更新、監視を行います。

## <a name="configure-acr-authentication"></a>ACR 認証を構成する

AKS クラスターと ACR レジストリとの間で認証が構成されている必要があります。 その際、ACR レジストリからイメージをプルするための適切な権限を ACS の ID に付与することになります。

まず、AKS に対して構成されているサービス プリンシパルの ID を取得します。 リソース グループの名前と AKS クラスターの名前は、お使いの環境に合わせて更新してください。

```azurecli
$CLIENT_ID = $(az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId" --output tsv)
```

ACR レジストリのリソース ID を取得します。レジストリ名は実際の ACR レジストリの名前に、またリソース グループは、その ACR レジストリが存在するリソース グループに置き換えてください。

```azurecli
$ACR_ID = $(az acr show --name myACRRegistry --resource-group myResourceGroup --query "id" --output tsv)
```

適切なアクセス権を付与するロールの割り当てを作成します。

```azurecli
az role assignment create --assignee $CLIENT_ID --role Contributor --scope $ACR_ID
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Kubernetes クラスターが AKS にデプロイされました。 次の手順を完了しました。

> [!div class="checklist"]
> * Kubernetes AKS クラスターのデプロイ
> * Kubernetes CLI (kubectl) のインストール
> * kubectl の構成

次のチュートリアルに進んで、クラスターでのアプリケーションの実行について学習してください。

> [!div class="nextstepaction"]
> [Kubernetes でアプリケーションをデプロイする][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
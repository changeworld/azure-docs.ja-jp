---
title: Web UI を使用した Azure Kubernetes クラスターの管理
description: Azure Kubernetes Service (AKS) で組み込みの Kubernetes Web UI ダッシュボードを使用する方法について説明します
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 65525114f46002c5b9300f6bbabcee06cc27ef3a
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39091140"
---
# <a name="access-the-kubernetes-dashboard-with-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で Kubernetes ダッシュボードにアクセスする

Kubernetes には、基本的な管理操作に使用できる Web ダッシュボードが含まれています。 この記事では、Azure CLI を使用して Kubernetes ダッシュボードにアクセスする方法と、基本的なダッシュボード操作の手順について説明します。 Kubernetes ダッシュボードの詳細については、[Kubernetes の Web UI ダッシュボード][kubernetes-dashboard]に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

このドキュメントで詳しく説明する手順では、AKS クラスターを作成済みで、そのクラスターとの `kubectl` 接続が確立されていることを想定しています。 AKS クラスターを作成する必要がある場合は、[AKS クイック スタート][aks-quickstart]を参照してください。

また、Azure CLI バージョン 2.0.27 以降がインストールされ、構成されていることも必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

## <a name="start-kubernetes-dashboard"></a>Kubernetes ダッシュボードを起動する

Kubernetes ダッシュボードを起動するには、[az aks browse][az-aks-browse] コマンドを使用します。 次の例では、*myResourceGroup* という名前のリソース グループに *myAKSCluster* という名前のクラスターのダッシュボードを開きます。

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

このコマンドは、開発システムと Kubernetes API の間にプロキシを作成し、Kubernetes ダッシュボードへの Web ブラウザーを開きます。

### <a name="for-rbac-enabled-clusters"></a>RBAC 対応クラスターの場合

AKS クラスターが RBAC を使用する場合は、ダッシュボードに正しくアクセスする前に *ClusterRoleBinding* を作成する必要があります。 バインドを作成するには、次の例のように、[kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] コマンドを使用します。 

> [!WARNING]
> このサンプル バインドは、追加の認証コンポーネントを適用しないため、安全性に欠ける可能性があります。 Kubernetes ダッシュボードは、URL にアクセスできるすべてのユーザーに公開されています。 Kubernetes ダッシュボードは、一般に公開しないでください。
>
> ベアラー トークンやユーザー名/パスワードなどのメカニズムを使用して、ダッシュボードにアクセスできるユーザーとそのアクセス許可を制御することができます。 これにより、ダッシュボードをより安全に使用することができます。 さまざまな認証方法の使用の詳細については、Kubernetes ダッシュボード wiki で[アクセス コントロール][dashboard-authentication]に関するページを参照してください。

```console
kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
```

これで、RBAC 対応クラスター内の Kubernetes ダッシュボードにアクセスできるようになりました。 Kubernetes ダッシュボードを起動するには、前の手順で説明したように [az aks browse][az-aks-browse] コマンドを使用します。

## <a name="run-an-application"></a>アプリケーションの実行

Kubernetes ダッシュボードで、右上のウィンドウにある **[作成]** ボタンをクリックします。 デプロイに名前`nginx`をつけて、コンテナーイメージ名に`nginx:latest`を入力します。 **[サービス]** で、**[外部]** を選択し、ポートとターゲット ポートの両方に `80` を入力します。

準備ができたら、**[デプロイ]** をクリックしてデプロイを作成します。

![Kubernetes サービスの [作成] ダイアログ](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>アプリケーションを表示する

アプリケーションに関する状態は、Kubernetes ダッシュボードで確認できます。 アプリケーションが実行されると、各コンポーネントの横に緑色のチェックボックスが表示されます。

![Kubernetes ポッド](./media/container-service-kubernetes-ui/complete-deployment.png)

アプリケーション ポッドに関するより詳細な情報を表示するには、左側のメニューにある **ポッド** をクリックしてから、**NGINX** ポッドを選択します。 ここでは、リソース使用量などのポッド固有の情報を確認できます。

![Kubernetes リソース](./media/container-service-kubernetes-ui/running-pods.png)

アプリケーションの IP アドレスを見つけるには、左側のメニューにある **[サービス]** をクリックしてから、**NGINX** サービスを選択します。

![nginx ビュー](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>アプリケーションを編集する

アプリケーションの作成および表示に加えて、Kubernetes ダッシュボードはアプリケーションのデプロイの編集や更新にも使用できます。

デプロイを編集するには、左側のメニューにある **[デプロイメント]** をクリックしてから、**NGINX** デプロイを選択します。 最後に、右上のナビゲーション バーにある **[編集]** を選択します。

![Kubernetes 編集](./media/container-service-kubernetes-ui/view-deployment.png)

`spec.replica` 値を見つけ (これは 1 であるはずです)、この値を 3 に変更します。 そうすることにより、NGINX デプロイのレプリカ カウントは 1 から 3 に増加します。

準備ができたら、**[更新]** を選択します。

![Kubernetes 編集](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>次の手順

Kubernetes ダッシュボードの詳細については、Kubernetes のドキュメントを参照してください。

> [!div class="nextstepaction"]
> [Kubernetes Web UI ダッシュボード][kubernetes-dashboard]

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-browse]: /cli/azure/aks#az-aks-browse

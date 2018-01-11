---
title: "Web UI を使用した Azure Kubernetes クラスターの管理"
description: "AKS での Kubernetes ダッシュボードの使用"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ca828dab7bdb47e41596be2717598cfe828953ca
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="kubernetes-dashboard-with-azure-container-service-aks"></a>Azure Container Service (AKS) での Kubernetes ダッシュボード

Kubernetes ダッシュボードを起動するために Azure CLI を使用できます。 このドキュメントでは、Azure CLI での Kubernetes ダッシュボードの起動について説明します。また、いくつかの基本的なダッシュボード操作についても説明します。 Kubernetes ダッシュボードについて詳しくは、[Kubernetes の Web UI ダッシュボード][kubernetes-dashboard]に関するページをご覧ください。

## <a name="before-you-begin"></a>開始する前に

このドキュメントで詳しく説明する手順では、AKS クラスターを作成済みで、そのクラスターとの kubectl 接続が確立されていることを想定しています。 これらの項目が必要な場合は、[AKS のクイック スタート][aks-quickstart]を参照してください。

また、Azure CLI バージョン 2.0.21 以降がインストールされ、構成されていることも必要です。 バージョンを確認するには、az --version を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

## <a name="start-kubernetes-dashboard"></a>Kubernetes ダッシュボードを起動する

Kubernetes ダッシュボードを起動するには、`az aks browse` コマンドを使用します。 このコマンドを実行する場合は、リソース グループとクラスター名を置き換えます。

```azurecli
az aks browse --resource-group myResourceGroup --name myK8SCluster
```

このコマンドは、開発システムと Kubernetes API の間にプロキシを作成し、Kubernetes ダッシュボードへの Web ブラウザーを開きます。

## <a name="run-an-application"></a>アプリケーションの実行

Kubernetes ダッシュボードで、右上のウィンドウにある **[作成]** ボタンをクリックします。 デプロイに `nginx` という名前を付け、イメージの名前として `nginx:latest` を入力します。 **[サービス]** で、**[外部]** を選択し、ポートとターゲット ポートの両方に `80` を入力します。

準備ができたら、**[デプロイ]** をクリックしてデプロイを作成します。

![Kubernetes サービスの [作成] ダイアログ](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>アプリケーションを表示する

アプリケーションに関する状態は、Kubernetes ダッシュボードで確認できます。 アプリケーションが実行されると、各コンポーネントの横に緑色のチェックボックスが表示されます。

![Kubernetes ポッド](./media/container-service-kubernetes-ui/complete-deployment.png)

アプリケーション ポッドに関するより詳細な情報を表示するには、左側のメニューにある **[Pods] (ポッド)** をクリックしてから、**NGINX** ポッドを選択します。 ここでは、リソース使用量などのポッド固有の情報を確認できます。

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

## <a name="next-steps"></a>次のステップ

Kubernetes ダッシュボードの詳細については、Kubernetes のドキュメントを参照してください。

> [!div class="nextstepaction"]
> [Kubernetes Web UI ダッシュボード][kubernetes-dashboard]

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
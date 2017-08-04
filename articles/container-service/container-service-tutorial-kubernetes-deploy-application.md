---
title: "Azure Container Service チュートリアル - アプリケーションのデプロイ | Microsoft Docs"
description: "Azure Container Service チュートリアル - アプリケーションのデプロイ"
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
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 94e1eacd6864a7fcaf753a11e855f6e69aee0f98
ms.contentlocale: ja-jp
ms.lasthandoff: 07/08/2017

---

# <a name="run-applications-in-kubernetes"></a>Kubernetes でアプリケーションを実行する

このチュートリアルでは、サンプル アプリケーションを Kubernetes クラスターにデプロイします。 手順は次のとおりです。

> [!div class="checklist"]
> * Kubernetes オブジェクトの概要
> * Kubernetes マニフェスト ファイルをダウンロードする
> * Kubernetes でアプリケーションを実行する
> * アプリケーションをテストする

後のチュートリアルでは、このアプリケーションをスケールアウトおよび更新し、Kubernetes クラスターを監視します。

## <a name="before-you-begin"></a>開始する前に

前のチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化し、イメージを Azure Container Registry にアップロードして、Kubernetes クラスターを作成しました。 これらの手順を実行していない場合で、行いたい場合は、「[チュートリアル 1 – コンテナー イメージを作成する](./container-service-tutorial-kubernetes-prepare-app.md)」に戻ってください。 

このチュートリアルでは、少なくとも Kubernetes クラスターが必要です。

## <a name="kubernetes-objects"></a>Kubernetes のオブジェクト

コンテナー化されたアプリケーションを Kubernetes にデプロイすると、多数の異なる Kubernetes オブジェクトが作成されます。 各オブジェクトは、クラスターの望ましい状態を表します。 たとえば、簡単なアプリケーションは、密接に関連するコンテナーをグループ化したものであるポッド、ネットワーク化されたストレージである永続ボリューム、アプリケーションの状態を管理するデプロイで構成できます。 

すべての Kubernetes オブジェクトについて詳しくは、kubernetes.io で [Kubernetes の概念](https://kubernetes.io/docs/concepts/)をご覧ください。

## <a name="get-manifest-files"></a>マニフェスト ファイルを取得する

このチュートリアルでは、Kubernetes マニフェストを使って Kubernetes オブジェクトをデプロイします。 Kubernetes マニフェストは、オブジェクトの構成命令を含む YAML ファイルです。

このチュートリアルの各オブジェクトのマニフェスト ファイルは、前のチュートリアルで複製した Azure Vote アプリケーション リポジトリで入手できます。 まだ行っていない場合は、次のコマンドでリポジトリを複製してください。 

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

マニフェスト ファイルは、複製されたリポジトリの次のディレクトリにあります。

```bash
/azure-voting-app/kubernetes-manifests/
```

## <a name="run-application"></a>アプリケーションを実行する

### <a name="storage-objects"></a>ストレージ オブジェクト

Azure Vote アプリケーションには MySQL データベースが含まれるので、ポッド間で共有できるボリュームにデータベース ファイルを置く必要があります。 この構成では、MySQL ポッドが再作成された場合、データベース ファイルはそのまま維持されます。

`storage-resources.yaml` マニフェスト ファイルで作成される[ストレージ クラス オブジェクト](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#storageclasses)では、永続ボリュームの作成方法と場所が定義されています。 Kubernetes では複数のボリューム プラグインを利用できます。 ここでは、[Azure ディスク](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#azure-disk) プラグインを使います。 

[永続ボリューム要求](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)も作成され、これでは (ストレージ クラスを使って) ストレージが構成されて、ポッドに割り当てられます。

ストレージ オブジェクトを作成するには以下を実行します。

```bash
kubectl create -f storage-resources.yaml
```

完了すると、仮想ディスクが作成されて、Kubernetes ポッドにアタッチされます。 仮想ディスクは、Kubernetes クラスターと同じリソース グループに存在するストレージ アカウントに、ストレージ クラス オブジェクト (Standard_LRS) と同じ構成で、自動的に作成されます。

### <a name="secure-sensitive-values"></a>機微な値をセキュリティで保護する

[Kubernetes のシークレット](https://kubernetes.io/docs/concepts/configuration/secret/)は、機微な情報のストレージに対するセキュリティ保護を提供します。 Azure Vote データベースの資格情報は、`pod-secrets.yaml` ファイルを使ってシークレットに保存されます。 

シークレット オブジェクトを作成するには以下を実行します。

```bash
kubectl create -f pod-secrets.yaml
```

### <a name="create-deployments"></a>デプロイを作成する

[Kubernetes のデプロイ](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)は、Kubernetes ポッドの状態を管理します。 この管理には、必要なレプリカ数を実行されていること、ボリュームがマウントされていること、適切なコンテナー イメージが使われていることの保証といったことが含まれます。

`azure-vote-deployment.yaml` マニフェスト ファイルは、Azure Vote アプリケーションのフロントエンド部分およびバックエンド部分のデプロイを作成します。

#### <a name="update-image-names"></a>イメージ名を更新する

Azure Container Registry を使ってイメージを格納する場合、イメージ名の前に ACR ログイン サーバー名を付加する必要があります。

ACR ログイン サーバー名を取得するには、[az acr list](/cli/azure/acr#list) コマンドを使います。

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

`azure-vote-deployment.yaml` ファイルで、*azure-vote-front* と *azure-vote-back* のコンテナー イメージ名を更新します。

フロントエンド イメージ名の例:

```yaml
containers:
      - name: azure-vote-front
        image: <acrLoginServer>/azure-vote-front:v1
```

バックエンド イメージ名の例:

```yaml
containers:
      - name: azure-vote-back
        image: <acrLoginServer>/azure-vote-front:v1
```

#### <a name="create-deployment-objects"></a>デプロイ オブジェクトを作成する

[kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) を実行して、Azure Vote アプリケーションを開始します。

```bash
kubectl create -f azure-vote-deployment.yaml
```

### <a name="expose-application"></a>アプリケーションを公開する

[Kubernetes サービス](https://kubernetes.io/docs/concepts/services-networking/service/)では、ポッドへのアクセス方法が定義されています。 Azure Vote アプリでは、デプロイ名によってバックエンド デプロイに内部的にアクセスできる必要があります。 フロントエンド デプロイには、インターネット経由でアクセスできる必要があります。 Azure Vote アプリ サービスの構成は、`services.yaml` マニフェスト ファイルで定義されています。

サービスを作成するには以下を実行します。

```bash
kubectl create -f services.yaml
```

## <a name="test-application"></a>アプリケーションをテストする

すべてのオブジェクトを作成した後は、azure-vote-front サービスの外部 IP アドレスを介してアプリケーションにアクセスできます。 このサービスの作成には数分かかる場合があります。 サービス作成プロセスを監視するには、次のコマンドを実行します。 *azure-vote-front* サービスの *EXTERNAL-IP* の値が *pending* から IP アドレスに変わると、アプリケーションは準備が完了し、外部 IP アドレスでアクセスできるようになります。

```bash
kubectl get service -w
```

出力:

```bash
NAME               CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
azure-vote-back    10.0.77.30    <none>          3306/TCP       4m
azure-vote-front   10.0.120.96   40.71.227.124   80:31482/TCP   4m
kubernetes         10.0.0.1      <none>          443/TCP        7m
```

サービスの準備ができた後は、`CTRL-C` を実行して kubectl の監視を終了します。

アプリケーションを表示するには、返された外部 IP アドレスを参照します。

![Azure 上の Kubernetes クラスターの図](media/container-service-kubernetes-tutorials/vote-app-external.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Vote アプリケーションを Azure Container Service の Kubernetes クラスターにデプロイしました。 次のタスクを行いました。  

> [!div class="checklist"]
> * Kubernetes オブジェクトの概要
> * Kubernetes マニフェスト ファイルをダウンロードする
> * Kubernetes でアプリケーションを実行する
> * アプリケーションをテストする

次のチュートリアルに進んで、Kubernetes アプリケーションとその基になっている Kubernetes インフラストラクチャ両方のスケーリングに関して学習してください。 

> [!div class="nextstepaction"]
> [Kubernetes ポッドと Kubernetes インフラストラクチャをスケーリングする](./container-service-tutorial-kubernetes-scale.md)

---
title: Cloud Controller Manager を有効にする (プレビュー)
description: ツリー外クラウド プロバイダーを有効にする方法について説明します
services: container-service
ms.topic: article
ms.date: 8/25/2021
ms.author: juda
ms.openlocfilehash: 98f8fe2e00a7671078da7dae2174401b6c33fd30
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667618"
---
# <a name="enable-cloud-controller-manager-preview"></a>Cloud Controller Manager を有効にする (プレビュー)

Microsoft Azure は、クラウド プロバイダーとして、ユーザーに代わってインフラストラクチャをサポートするために Kubernetes コミュニティと密接に連携しています。

現在、Kubernetes 内でのクラウド プロバイダーの統合は "ツリー内" です。ここでは、クラウド固有の機能に対する変更は、標準の Kubernetes リリース サイクルに従う必要があります。  問題を見つけ、修正し、強化機能をロールアウトする必要がある場合は、Kubernetes コミュニティのリリース サイクル内でこれを行う必要があります。

現在 Kubernetes コミュニティでは、クラウド プロバイダーが、[cloud-provider-azure][cloud-provider-azure] コンポーネントを通じて中心となる Kubernetes リリース スケジュールとは別にリリースを制御する "ツリー外" モデルを採用しています。  Kubernetes バージョン 1.21 以降では、Cloud Storage Interface (CSI) ドライバーが既定として既にロールアウトされています。

> [!Note]
> AKS クラスターで Cloud Controller Manager を有効にすると、ツリー外の CSI ドライバーも有効になります。

Cloud Controller Manager は、AKS でサポートされている Kubernetes 1.22 の既定のコントローラーになります。


[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>開始する前に

次のリソースがインストールされている必要があります。

* Azure CLI
* `aks-preview` 拡張機能バージョン 0.5.5 以降
* Kubernetes バージョン 1.20.x 以降


### <a name="register-the-enablecloudcontrollermanager-feature-flag"></a>`EnableCloudControllerManager` 機能フラグを登録する

Cloud Controller Manager 機能を使用するには、サブスクリプションで `EnableCloudControllerManager` 機能フラグを有効にする必要があります。 

```azurecli
az feature register --name EnableCloudControllerManager --namespace Microsoft.ContainerService
```
[az feature list][az-feature-list] コマンドを使用して登録状態を確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableCloudControllerManager')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-cloud-controller-manager"></a>Cloud Controller Manager を使用して AKS クラスターを作成する

Cloud Controller Manager を使用してクラスターを作成するには、Azure CLI を使用して、`EnableCloudControllerManager=True` を顧客ヘッダーとして Azure API に渡します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -n aks -g myResourceGroup --aks-custom-headers EnableCloudControllerManager=True
```

## <a name="upgrade-an-aks-cluster-to-cloud-controller-manager"></a>AKS クラスターを Cloud Controller Manager にアップグレードする

Cloud Controller Manager を使用するためのクラスターをアップグレードするには、Azure CLI を使用して、`EnableCloudControllerManager=True` を顧客ヘッダーとして Azure API に渡します。

```azurecli-interactive
az aks upgrade -n aks -g myResourceGroup --aks-custom-headers EnableCloudControllerManager=True
```

## <a name="next-steps"></a>次のステップ

- CSI ドライバーの詳細と、Kubernetes 1.21 以降のバージョンの既定の動作については、[ドキュメント][csi-docs]を参照してください。

- ツリー外のプロバイダーに関する Kubernetes コミュニティ指示の詳細については、[コミュニティのブログ投稿][community-blog]を参照してください。


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[csi-docs]: csi-storage-drivers.md

<!-- LINKS - External -->
[community-blog]: https://kubernetes.io/blog/2019/04/17/the-future-of-cloud-providers-in-kubernetes
[cloud-provider-azure]: https://github.com/kubernetes-sigs/cloud-provider-azure

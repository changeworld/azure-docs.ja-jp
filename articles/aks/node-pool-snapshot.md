---
title: Azure Kubernetes Service (AKS) ノード プールのスナップショットを作成する (プレビュー)
description: AKS クラスター ノード プールのスナップショットを作成する方法、およびスナップショットからクラスターとノード プールを作成する方法について説明します。
ms.service: container-service
ms.topic: article
ms.date: 09/11/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: b547b65fc3f3086e90da073fb898de3980938147
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858365"
---
# <a name="azure-kubernetes-service-aks-node-pool-snapshot-preview"></a>Azure Kubernetes Service (AKS) ノード プールのスナップショット (プレビュー)

AKS は新しいノード イメージを毎週リリースします。新しいクラスター、新しいノード プール、またはアップグレード クラスターはすべて、最新のイメージを常に受け取ります。このイメージを使用すると、環境の一貫性を維持することや、反復可能な環境を保持することが困難になる場合があります。

ノード プール スナップショットを使用すると、ノード プールの構成スナップショットを作成できます。その後、その構成と Kubernetes バージョンがサポートされている限り、そのスナップショットに基づいて新しいノード プールや新しいクラスターを作成できます。 サポート可能な Windows について詳しくは、[AKS でサポートされている Kubernetes のバージョン][supported-versions]に関するページを参照してください。

スナップショットは、ノード イメージのバージョン、Kubernetes のバージョン、OS の種類、OS SKU など、ソース ノード プールからの構成情報を含む Azure リソースです。 このスナップショット リソースとその構成の各値を参照して、それに基づいて新しいノード プールやクラスターを作成できます。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

### <a name="limitations"></a>制限事項

- スナップショットから作成されたノード プールまたはクラスターでは、スナップショットと同じ仮想マシン ファミリの VM を使用する必要があります。たとえば、D シリーズ ノード プールからキャプチャされたスナップショットに基づいて新しい N シリーズ ノード プールを作成することはできません。これは、このような場合のノード イメージが構造的に異なるためです。
- プレビュー期間中は、スナップショットを作成し、ソース ノード プールと同じリージョンで使用する必要があります。

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 拡張機能をインストールする

"*aks-preview*" Azure CLI 拡張機能バージョン 0.5.40 以降も必要です。 *aks-preview* Azure CLI 拡張機能は、[az extension add][az-extension-add] コマンドを使用してインストールします。 または、[az extension update][az-extension-update] コマンドを使用すると、使用可能な更新プログラムをインストールできます。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-snapshotpreview-preview-feature"></a>`SnapshotPreview` プレビュー機能を登録する

この機能を使用するには、サブスクリプションで `SnapshotPreview` 機能フラグも有効にする必要があります。

`SnapshotPreview` 機能フラグは、次の例のとおり、[az feature register][az-feature-register] コマンドを使用して登録します。

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "SnapshotPreview"
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録の状態は、[az feature list][az-feature-list] コマンドで確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'microsoft.ContainerService/SnapshotPreview')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="take-a-node-pool-snapshot"></a>ノード プールのスナップショットを作成する

ノード プールからスナップショットを初めて作成する場合、ノード プール リソース ID が必要になります。この ID は、次のコマンドで取得できます。

```azurecli-interactive
NODEPOOL_ID=$(az aks nodepool show --name nodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --query id -o tsv)
```

> [!IMPORTANT]
> AKS ノード プールからスナップショットを作成するには、その AKS ノード プールを 2021 年 11 月 10 日以後に作成またはアップグレードする必要があります。

次に、以前のノード プールからスナップショットを作成するために、`az aks snapshot` CLI コマンドを使用します。

```azurecli-interactive
az aks snapshot create --name MySnapshot --resource-group MyResourceGroup --nodepool-id $NODEPOOL_ID --location eastus
```

## <a name="create-a-node-pool-from-a-snapshot"></a>スナップショットからノード プールを作成する

最初に、以前に作成したスナップショットのリソース ID が必要になります。これは、次のコマンドで取得できます。

```azurecli-interactive
SNAPSHOT_ID=$(az aks snapshot show --name MySnapshot --resource-group myResourceGroup --query id -o tsv)
```

次に、以下のコマンドを使用して、このスナップショットに基づいて新しいノード プールを追加できます。

```azurecli-interactive
az aks nodepool add --name np2 --cluster-name myAKSCluster --resource-group myResourceGroup --snapshot-id $SNAPSHOT_ID
```

## <a name="upgrading-a-node-pool-to-a-snapshot"></a>ノード プールをスナップショットにアップグレードする

スナップショットの Kubernetes バージョンとノード イメージのバージョンが現在のノード プールのバージョンよりも新しい場合は、ノード プールをスナップショット構成にアップグレードできます。

最初に、以前に作成したスナップショットのリソース ID が必要になります。これは、次のコマンドで取得できます。

```azurecli-interactive
SNAPSHOT_ID=$(az aks snapshot show --name MySnapshot --resource-group myResourceGroup --query id -o tsv)
```

次に、このコマンドを使用して、このノード プールをこのスナップショット構成にアップグレードできます。

```azurecli-interactive
az aks nodepool upgrade --name nodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --snapshot-id $SNAPSHOT_ID
```

> [!NOTE]
> ノード プール イメージのバージョンは、スナップショットに含まれているものと同じになり、どのスケール操作でも同じままになります。 ただし、このノード プールがアップグレードされた場合、またはスナップショット ID を指定せずにノード イメージのアップグレードが実行された場合、ノード イメージは最新にアップグレードされます。

## <a name="create-a-cluster-from-a-snapshot"></a>スナップショットからクラスターを作成する

スナップショットからクラスターを作成すると、クラスターの元のシステム プールがスナップショット構成から作成されます。

最初に、以前に作成したスナップショットのリソース ID が必要になります。これは、次のコマンドで取得できます。

```azurecli-interactive
SNAPSHOT_ID=$(az aks snapshot show --name MySnapshot --resource-group myResourceGroup --query id -o tsv)
```

次に、このコマンドを使用して、スナップショット構成からこのクラスターを作成できます。

```azurecli-interactive
az aks cluster create --name myAKSCluster2 --resource-group myResourceGroup --snapshot-id $SNAPSHOT_ID
```

## <a name="next-steps"></a>次のステップ

- 最新のノード イメージの詳細については、[AKS のリリース ノート](https://github.com/Azure/AKS/releases)をご覧ください。
- 「[AKS クラスターのアップグレード][upgrade-cluster]」によって Kubernetes バージョンをアップグレードする方法を確認します。
- 「[ノード イメージのアップグレード][node-image-upgrade]」で、ノード イメージのバージョンをアップグレードする方法を確認します
- 複数のノード プールの詳細と、[複数のノード プールの作成と管理][use-multiple-node-pools]によってノード プールをアップグレードする方法を確認します。

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[supported-versions]: supported-kubernetes-versions.md
[upgrade-cluster]: upgrade-cluster.md
[node-image-upgrade]: node-image-upgrade.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
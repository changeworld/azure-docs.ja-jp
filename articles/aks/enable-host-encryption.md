---
title: Azure Kubernetes Service (AKS) でホストベースの暗号化を有効にする
description: Azure Kubernetes Service (AKS) クラスターでホストベースの暗号化を構成する方法について説明します
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7eb3215aeb1f7c6508092d18fbebd90f852efe63
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772921"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Azure Kubernetes Service (AKS) でのホストベースの暗号化 (プレビュー)

ホストベースの暗号化では、AKS エージェント ノードの VM の VM ホストに保存されたデータは、保存時に暗号化され、暗号化された状態でストレージ サービスに送られます。 つまり、一時ディスクは、プラットフォーム マネージド キーを使用して保存時に暗号化されます。 OS ディスクとデータ ディスクのキャッシュは、ディスクに設定された暗号化のタイプに応じて、プラットフォーム マネージド キーまたはカスタマー マネージド キーのどちらかを使用して保存時に暗号化されます。 既定では、AKS を使用する場合、OS ディスクとデータ ディスクはプラットフォーム マネージド キーを使用して保存時に暗号化されます。つまり、これらのディスクのキャッシュも既定で、プラットフォーム マネージド キーを使用して保存時に暗号化されます。  「[Azure Kubernetes Service (AKS) の Azure ディスクに独自のキー (BYOK) を使用する](azure-disk-customer-managed-keys.md)」の説明に従って、独自のマネージド キーを指定できます。 以後、これらのディスクのキャッシュも、この手順で指定するキーを使用して暗号化されるようになります。


## <a name="before-you-begin"></a>開始する前に

この機能は、クラスターの作成時またはノード プールの作成時にのみ設定できます。

> [!NOTE]
> ホストベースの暗号化は、Azure マネージド ディスクのサーバー側暗号化がサポートされている [Azure リージョン][supported-regions]で使用できますが、特定の[サポートされている VM サイズ][supported-sizes]しか使用できません。

### <a name="prerequisites"></a>前提条件

- `aks-preview` CLI 拡張機能 v0.4.73 以降のバージョンがインストールされていることを確認します。
- `Microsoft.ContainerService` の `EnableEncryptionAtHostPreview` 機能フラグが有効になっていることを確認します。

Azure Kubernetes Service クラスターに対して EncryptionAtHost プロパティを使用する前に、サブスクリプションに対してこの機能を有効にする必要があります。 下の手順に従って、お使いのサブスクリプションに対してこの機能を有効にしてください。

1. 次のコマンドを実行して、お使いのサブスクリプションにこの機能を登録します

```azurecli-interactive
Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"
```
2. この機能を試す前に、下のコマンドを使用して登録状態が Registered であることを確認してください (数分かかります)。

```azurecli-interactive
Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"
```

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 拡張機能をインストールする

ホストベースの暗号化に対応した AKS クラスターを作成するには、最新の *aks-preview* CLI 拡張機能が必要です。 [az extension add][az-extension-add] コマンドを使用して *aks-preview* Azure CLI 拡張機能をインストールするか、[az extension update][az-extension-update] コマンドを使用して使用可能な更新プログラムがあるかどうかを確認します。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>制限事項

- 新しいノード プールでのみ有効にできます。
- Azure マネージド ディスクのサーバー側暗号化がサポートされている [Azure リージョン][supported-regions]で、特定の[サポートされている VM サイズ][supported-sizes]のみを使用して有効にできます。
- Virtual Machine Scale Sets (VMSS) の *VM セット タイプ* に基づいた AKS クラスターとノード プールが必要です。

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>新しいクラスターでホストベースの暗号化を使用する (プレビュー)

クラスターの作成時に、ホストベースの暗号化を使用するようにクラスター エージェント ノードを構成します。 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

ホストベースの暗号化を使用せずにクラスターを作成する場合は、`--enable-encryption-at-host` パラメーターを省略できます。

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>既存のクラスターでホストベースの暗号化を使用する (プレビュー)

新しいノード プールをクラスターに追加することによって、既存のクラスターでホストベースの暗号化を有効にできます。 `--enable-encryption-at-host` パラメーターを使用して、ホストベースの暗号化を使用するように新しいノード プールを構成します。

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

ホストベースの暗号化機能を使用せずに新しいノード プールを作成する場合は、`--enable-encryption-at-host` パラメーターを省略できます。

## <a name="next-steps"></a>次のステップ

[AKS クラスターのセキュリティのベスト プラクティス][best-practices-security]を確認します。[ホストベースの暗号化](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)の詳細を確認します。


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register

---
title: Azure CLI を使用してフレキシブルなスケール セットに仮想マシンを作成する
description: Azure CLI を使用して、フレキシブル オーケストレーション モードで仮想マシン スケール セットを作成する方法について説明します。
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 09f0d1f3f39a43ece445863023fd2ff7772404b9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122868403"
---
# <a name="preview-create-virtual-machines-in-a-flexible-scale-set-using-azure-cli"></a>プレビュー: Azure CLI を使用してフレキシブルなスケール セットに仮想マシンを作成する

**適用対象:** :heavy_check_mark: フレキシブルなスケール セット


この記事では、Azure CLI を使用して、フレキシブル オーケストレーション モードで仮想マシン スケール セットを作成する方法について説明します。 フレキシブル スケール セットの詳細については、[仮想マシン スケール セットのフレキシブル オーケストレーション モード](flexible-virtual-machine-scale-sets.md)に関するページを参照してください。 

[Azure CLI](/cli/azure/install-az-cli2) の最新版がインストールされていること、および [az login](/cli/azure/reference-index) で Azure アカウントにログインしていることを確認します。


> [!IMPORTANT]
> フレキシブル オーケストレーション モードの仮想マシン スケール セットは現在、パブリック プレビュー段階です。 後述するパブリック プレビュー機能を使用するためには、オプトイン手順が必要です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


> [!CAUTION]
> オーケストレーション モードは、スケール セットを作成するときに定義され、後で変更または更新することはできません。


## <a name="register-for-flexible-orchestration-mode"></a>フレキシブル オーケストレーション モードの登録

フレキシブル オーケストレーション モードで仮想マシン スケール セットをデプロイするには、最初にプレビュー機能に対してサブスクリプションを登録しておく必要があります。 登録が完了するまでに数分かかる場合があります。

[az feature register](/cli/azure/feature#az_feature_register) を使用して、サブスクリプションでのプレビューを有効にします。

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD
az feature register --namespace Microsoft.Compute --name VMScaleSetFlexPreview 
az feature register --namespace Microsoft.Compute --name SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview
```

機能の登録には最大で 15 分かかる場合があります。 登録状態を確認するには:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD
```


## <a name="get-started-with-flexible-scale-sets"></a>フレキシブル スケール セットの使用を開始する

Azure CLI を使用してフレキシブルの仮想マシン スケール セットを作成します。

### <a name="add-multiple-vms-to-a-scale-set"></a>複数の VM をスケール セットに追加する

次の例では、仮想マシン プロファイル (VM の種類、ネットワーク構成など)、および作成するインスタンスの数 (インスタンス数 = 2) を指定します。  

```azurecli-interactive
az vmss create
--resource-group $rg
--name $vmssName
--single-placement-group false
--orchestration-mode flexible
--platform-fault-domain-count 1
--image UbuntuLTS
--admin-username azureuser
--instance-count 2
--vm-sku 'Standard_D2s_v3'
--network-api-version '2020-11-01'
--computer-name-prefix $computerNamePrefix
--vnet-name $vnetName
--subnet $subnetName
--public-ip-per-vm
```

### <a name="add-a-single-vm-to-a-scale-set"></a>1 つの VM をスケール セットに追加する

次の例では、VM プロファイルを指定せずにフレキシブルなスケール セットを作成する方法を示します。この場合、障害ドメイン数は 1 に設定されます。 仮想マシンが作成され、フレキシブルなスケール セットに追加されます。

```azurecli-interactive
vmoname="my-vmss-vmo"
vmname="myVM"
rg="my-resource-group"

az group create -n "$rg" -l $location
az vmss create -n "$vmoname" -g "$rg" -l $location --orchestration-mode vm --platform-fault-domain-count 1
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmoname --image UbuntuLTS
``` 


## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Azure portal でフレキシブルなスケール セットを作成する方法について学習する。](flexible-virtual-machine-scale-sets-portal.md)
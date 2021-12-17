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
ms.openlocfilehash: a0c6d8dc1f9e032ca76393c52be76d1803873f8b
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166409"
---
# <a name="create-virtual-machines-in-a-flexible-scale-set-using-azure-cli"></a>Azure CLI を使用してフレキシブルなスケール セットに仮想マシンを作成する

**適用対象:** :heavy_check_mark: フレキシブルなスケール セット


この記事では、Azure CLI を使用して、フレキシブル オーケストレーション モードで仮想マシン スケール セットを作成する方法について説明します。 フレキシブル スケール セットの詳細については、[仮想マシン スケール セットのフレキシブル オーケストレーション モード](flexible-virtual-machine-scale-sets.md)に関するページを参照してください。 

[Azure CLI](/cli/azure/install-az-cli2) の最新版がインストールされていること、および [az login](/cli/azure/reference-index) で Azure アカウントにログインしていることを確認します。


> [!CAUTION]
> オーケストレーション モードは、スケール セットを作成するときに定義され、後で変更または更新することはできません。


## <a name="get-started-with-flexible-scale-sets"></a>フレキシブル スケール セットの使用を開始する

Azure CLI を使用してフレキシブルの仮想マシン スケール セットを作成します。

### <a name="add-multiple-vms-to-a-scale-set"></a>複数の VM をスケール セットに追加する

次の例では、仮想マシン プロファイル (VM の種類、ネットワーク構成など)、および作成するインスタンスの数 (インスタンス数 = 2) を指定します。  

```azurecli-interactive
az vmss create
--name $vmssName
--resource-group $rg
--image UbuntuLTS
--instance-count 2
--orchestration-mode flexible
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
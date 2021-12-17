---
title: Azure CLI を使用して Azure 仮想マシン スケール セットで OS イメージをアップグレードするためのメンテナンス コントロール
description: メンテナンス コントロールと Azure CLI を使用して、OS イメージの自動アップグレードが Azure 仮想マシン スケール セットにロール アウトされるタイミングを制御する方法について説明します。
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2021
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e00341b38cca73ccd351b4e16ce71317a8d0c751
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112073167"
---
# <a name="maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-azure-cli"></a>Azure CLI を使用して Azure 仮想マシン スケール セットで OS イメージをアップグレードするためのメンテナンス コントロール

メンテナンス コントロールを使用すると、ゲスト OS イメージの自動アップグレードを仮想マシン スケール セットに適用するタイミングを決定できます。 このトピックでは、メンテナンス コントロール用の Azure CLI オプションについて説明します。 メンテナンス コントロールの使用方法の詳細については、「[Azure 仮想マシン スケール セットのメンテナンス コントロール](virtual-machine-scale-sets-maintenance-control.md)」を参照してください。


## <a name="create-a-maintenance-configuration"></a>メンテナンス構成を作成する

`az maintenance configuration create` を使用して、メンテナンス構成を作成します。 この例では、OS イメージを対象とした *myConfig* という名前のメンテナンス構成を作成します。 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope osimage\
   --location eastus
```

後で使用するために、出力から構成 ID をコピーします。

`--maintenance-scope osimage` を使用すると、そのメンテナンス構成がゲスト OS に対する更新をコントロールするために確実に使用されているかを確認できます。

同じ名前の構成を別の場所に作成しようとすると、エラーが発生します。 構成名は、リソース グループに対して一意である必要があります。

`az maintenance configuration list` を使用すると、使用可能なメンテナンス構成に対してクエリを実行できます。

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

### <a name="create-a-maintenance-configuration-with-a-scheduled-window"></a>日程計画された期間でメンテナンス構成を作成する
Azure でリソースに更新プログラムを適用する日程計画された期間を宣言することもできます。 この例では、毎月第 4 月曜日に 5 時間という日程計画で *myConfig* という名前のメンテナンス構成が作成されます。 日程計画された期間を作成すると、更新プログラムを手動で適用する必要がなくなります。

> [!IMPORTANT]
> メンテナンスの **期間** は、*5 時間* 以上である必要があります。 メンテナンスの **繰り返し** は *Day* に設定する必要があります。

```azurecli-interactive
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope osimage \
   --location eastus \
   --maintenance-window-duration "05:00" \
   --maintenance-window-recur-every "Day" \
   --maintenance-window-start-date-time "2020-12-30 08:00" \
   --maintenance-window-time-zone "Pacific Standard Time"
```

## <a name="assign-the-configuration"></a>構成を割り当てる

`az maintenance assignment create` を使用して、ご自身の仮想マシン スケール セットに構成を割り当てます。


## <a name="enable-automatic-os-upgrade"></a>OS の自動アップグレードを有効にする

メンテナンス コントロールを使用する仮想マシン スケール セットごとに、OS の自動アップグレードを有効にすることができます。 仮想マシン スケール セットでの OS の自動アップグレードの有効化については、「[Azure 仮想マシン スケール セットによる OS イメージの自動アップグレード](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure で実行されている仮想マシンのメンテナンスと更新について確認する](maintenance-and-updates.md)

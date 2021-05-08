---
title: Linux 仮想マシン スケール セットの Azure ハイブリッド特典
description: Azure で実行される Linux 仮想マシンのコストを削減するために、仮想マシン スケール セットに対して Azure ハイブリッド特典を適用する方法について説明します。
services: virtual-machine-scale-sets
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machine-scale-sets
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/20/2021
ms.author: mathapli
ms.openlocfilehash: a714434c39a0c40c2e908f2d0c424f02851921a6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933680"
---
# <a name="azure-hybrid-benefit-for-linux-virtual-machine-scale-set-public-preview"></a>Linux 仮想マシン スケール セットの Azure ハイブリッド特典 (パブリック プレビュー)

**Linux 仮想マシン スケール セットの Azure ハイブリッド特典は、現在パブリック プレビューの段階です**。 AHB 特典により、RHEL および SLES [仮想マシン スケール セット](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)を実行するためのコストを削減できます。

この特典により、スケール セットのインフラストラクチャ コストのみ支払うだけで済みます。 この特典は、すべての RHEL および SLES Marketplace の従量課金制 (PAYG) イメージに利用できます。


>[!NOTE]
> この記事では、Linux VMSS の Azure ハイブリッド特典について説明します。 [こちら](https://docs.microsoft.com/azure/virtual-machines/linux/azure-hybrid-benefit-linux)に Linux VM の AHB に関する個別の記事があります。これは、2020 年 11 月以降、Azure のお客様は既に使用しています。

## <a name="benefit-description"></a>特典の説明
Azure ハイブリッドでは、Red Hat または SUSE の既存のクラウド アクセス ライセンスを使用して、仮想マシン スケール セット インスタンスをサブスクリプション持ち込み (BYOS) 課金に柔軟に変換できます。 

PAYG マーケットプレース イメージからデプロイされた仮想マシン スケール セットでは、インフラストラクチャとソフトウェアの両方の料金が請求されます。 AHB を使用すると、PAYG インスタンスを再デプロイせずに BYOS 課金モデルに変換できます。

:::image type="content" source="./media/azure-hybrid-benefit-linux/azure-hybrid-benefit-linux-cost.png" alt-text="Linux VM での Azure ハイブリッド特典コストの視覚化。":::

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux"></a>Linux の Azure ハイブリッド特典の対象範囲
Azure ハイブリッド特典は、Azure Marketplace のすべての RHEL および SLES PAYG イメージで使用できます。 この特典は、現時点では Azure Marketplace の RHEL または SLES の BYOS イメージまたはカスタム イメージでは使用できません。

Linux VM でAzure Dedicated Host インスタンスおよび SQL ハイブリッド特典を既に使用している場合は、Azure ハイブリッド特典の対象になりません。

## <a name="get-started"></a>はじめに

### <a name="red-hat-customers"></a>Red Hat のお客様

RHEL の Azure ハイブリッド特典は、次の両方の条件を満たす Red Hat のお客様が利用できます。

- Azure での使用に適した、アクティブまたは未使用の RHEL サブスクリプションがある
- [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) プログラムを使用して、Azure で使用する 1 つ以上のサブスクリプションを有効にしている

> [!IMPORTANT]
> [cloud-access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) プログラムで正しいサブスクリプションを確実に有効にします。

Red Hat の特典の使用を開始するには:

1. [Red Hat Cloud Access のカスタマー インターフェイス](https://access.redhat.com/management/cloud)を使用して、対象となる RHEL サブスクリプションを Azure で有効にします。

   Red Hat Cloud Access の有効化プロセス中に指定した Azure サブスクリプションが、Azure ハイブリッド特典機能を使用できるようになります。
1. 既存および新規の RHEL PAYG 仮想マシン スケール セットに Azure ハイブリッド特典を適用します。 Azure portal または Azure CLI を使用して特典を有効にすることができます。
1. RHEL VM の更新ソースの構成、および RHEL サブスクリプションのコンプライアンス ガイドラインについては、[次の推奨される手順](https://access.redhat.com/articles/5419341)に従ってください。


### <a name="suse-customers"></a>SUSE のお客様

SUSE の特典の使用を開始するには:

1. SUSE Public Cloud プログラムに登録します。
1. Azure portal または Azure CLI を使用して、新しく作成した、または既存の仮想マシン スケール セットに特典を適用します。
1. 特典を受けた VM を別の更新ソースに登録します。


## <a name="enable-and-disable-the-benefit-on-azure-portal"></a>Azure portal で特典を有効または無効にする 
仮想マシン スケール セットに対して AHB を有効または無効にするためのポータル操作は、**現在使用できません**。

## <a name="enable-and-disable-the-benefit-using-azure-cli"></a>Azure CLI を使用して特典を有効または無効にする

`az vmss update` コマンドを使用して、既存の VM を更新します。 RHEL VM の場合は、`--license-type` パラメーターとして `RHEL_BYOS` を指定してコマンドを実行します。 SLES VM の場合は、`--license-type` パラメーターとして `SLES_BYOS` を指定してコマンドを実行します。

### <a name="cli-example-to-enable-the-benefit"></a>特典を有効にする CLI の例
```azurecli
# This will enable the benefit on a RHEL VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>特典を無効にする CLI の例
特典を無効にするには、`--license-type` の値に `None` を使用します。

```azurecli
# This will disable the benefit on a VM
az vmss update -g myResourceGroup -n myVmName --license-type None
```

>[!NOTE]
> スケール セットには、VM を最新のスケール セット モデルで最新の状態にする方法を決定する ["アップグレード ポリシー"](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) があります。 そのため、VMSS に "自動" アップグレード ポリシーがある場合、VM インスタンスの更新に合わせて AHB 特典が自動的に適用されます。 VMSS に、スケジュールされた更新に基づく "ローリング" アップグレード ポリシーがある場合、AHB が適用されます。
"手動" アップグレード ポリシーの場合、既存の各 VM の "手動アップグレード" を実行する必要があります。  

### <a name="cli-example-to-upgrade-virtual-machine-scale-set-instances-in-case-of-manual-upgrade-policy"></a>"手動アップグレード" ポリシーの場合に仮想マシン スケール セット インスタンスをアップグレードする CLI の例 
```azurecli
# This will bring VMSS instances up to date with latest VMSS model 
az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
```

## <a name="apply-the-azure-hybrid-benefit-at-virtual-machine-scale-set-create-time"></a>仮想マシン スケール セットの作成時に Azure ハイブリッド特典を適用する 
既存の従量課金制仮想マシン スケール セットに Azure ハイブリッド特典を適用することに加え、仮想マシン スケール セットの作成時にそれを呼び出すこともできます。 これを行うことにより利点は 3 倍になります。
- 同じイメージとプロセスを使用することで、PAYG と BYOS の両方の仮想マシン スケール セット インスタンスをプロビジョニングできます。
- これにより、将来のライセンス モードの変更や、BYOS のみのイメージでは利用できないものが有効になります。
- 仮想マシン スケール セット インスタンスは、既定で Red Hat Update Infrastructure (RHUI) に接続されるため、最新の状態に保たれ安全です。 デプロイ後、更新したメカニズムはいつでも変更できます。

### <a name="cli-example-to-create-virtual-machine-scale-set-with-ahb-benefit"></a>AHB 特典を使用して仮想マシン スケール セットを作成するための CLI の例
```azurecli
# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type RHEL_BYOS 

# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type SLES_BYOS
```

## <a name="next-steps"></a>次のステップ
* [Azure CLI を使用して、VM を作成および更新し、Azure ハイブリッド特典のライセンスの種類 (RHEL_BYOS、SLES_BYOS) を追加する方法を確認する](/cli/azure/vmss)

---
title: "Azure の Linux VM 向け可用性セットのチュートリアル | Microsoft Docs"
description: "Azure の Linux VM 向け可用性セットについて説明します。"
documentationcenter: 
services: virtual-machines-linux
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: d082b37a2e070136178259c54ada8dc141f81e13
ms.contentlocale: ja-jp
ms.lasthandoff: 05/03/2017


---

# <a name="how-to-use-availability-sets"></a>可用性セットの使用方法

このチュートリアルでは、可用性セットと呼ばれる論理的なグループに仮想マシン (VM) を配置することで、その可用性を高める方法について説明します。 可用性セット内で VM を作成する場合、Azure プラットフォームにより、基になるインフラストラクチャ全体に VM が分散されます。 ハードウェア障害が発生したり、プラットフォームで計画的なメンテナンスが実施されたりした場合、可用性セットを使用していると、少なくとも 1 つの VM を必ず動作させることができます。

このチュートリアルの手順は、最新バージョンの [Azure CLI 2.0](/cli/azure/install-azure-cli) を使用して行うことができます。

## <a name="availability-set-overview"></a>可用性セットの概要

仮想マシンは、基になる Azure データセンターのハードウェアの論理的なグループにまたがって作成することができます。 複数の VM を作成すると、コンピューティング リソースとストレージ リソースは、サーバー、ネットワーク スイッチ、ストレージなどのハードウェアにまたがって分散されます。 万一ハードウェア コンポーネントにメンテナンスが必要な状況が生じても、この分散によってアプリの稼働率が保たれます。 可用性セットでは、この論理的なグループ化を定義できます。

可用性セットによって、VM の高可用性が実現します。 アプリケーションが障害やメンテナンス イベントを必ず許容するように設計することも必要になります。

## <a name="create-an-availability-set"></a>可用性セットの作成

可用性セットは、[az vm availability-set create](/cli/azure/availability-set#create) を使用して作成できます。 この例では、*myResourceGroupAvailability* リソース グループ内の *myAvailabilitySet* という名前の可用性セットに対して、更新ドメインと障害ドメインの両方の数として *2* を設定します。

```azurecli
az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

## <a name="create-vms-inside-an-availability-set"></a>可用性セット内の VM の作成

VM は、ハードウェア全体で適切に分散させるために、可用性セット内で作成する必要があります。 可用性セットを作成した後に、既存の VM を追加することはできません。 

1 つの場所にあるハードウェアは、複数の更新ドメインと障害ドメインに分割されます。 **更新ドメイン**は、VM と、同時に再起動できる基になる物理ハードウェアのグループです。 同じ**障害ドメイン**内の VM は、共通の電源とネットワーク スイッチだけでなく、共通のストレージも共有します。 

[az vm create](/cli/azure/vm#create) を使用して VM を作成するときに、`--availability-set` パラメーターを使用して可用性セットを指定し、可用性セットの名前を指定します。

```azurecli
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --image Canonical:UbuntuServer:14.04.4-LTS:latest \
     --admin-username azureuser \
     --generate-ssh-keys \
     --no-wait
done 
```

これで、基になるハードウェア全体に 2 つの仮想マシンが分散されるようになります。 

## <a name="check-for-available-vm-sizes"></a>使用可能な VM のサイズのチェック 

可用性セットには後で VM をさらに追加することができますが、そのハードウェアで使用可能な VM のサイズを把握しておく必要があります。 ハードウェア クラスターで可用性セットに使用可能なすべてのサイズを一覧表示するには、[az vm availability-set list-sizes](/cli/azure/availability-set#list-sizes) を使用します。

```azurecli
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table  
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、可用性セットの使用方法について説明しました。 次のチュートリアルに進み、仮想マシンのスケール セットについて学習してください。

[VM スケール セットの作成](tutorial-create-vmss.md)



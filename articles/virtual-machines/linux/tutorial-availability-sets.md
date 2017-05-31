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
ms.date: 05/08/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: b3bb7d9d14293aae78c3a1c2ac6badcc48dcbe2c
ms.contentlocale: ja-jp
ms.lasthandoff: 05/17/2017


---

# <a name="how-to-use-availability-sets"></a>可用性セットの使用方法


このチュートリアルでは、可用性セットと呼ばれる機能を使用して、Azure で仮想マシン ソリューションの可用性と信頼性を向上させる方法を学習します。 可用性セットは、Azure にデプロイする VM を、複数の分離されたハードウェア クラスターに分散します。 これにより、Azure 内でハードウェアまたはソフトウェアの障害が発生した場合に影響を受けるのは VM のサブ セットに限定され、ソリューションを使用している顧客から見れば、ソリューション全体は引き続き利用可能であり、運用可能であることが保証されます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 可用性セットを作成する
> * 可用性セット内に VM を作成する
> * 使用可能な VM のサイズを確認する

このチュートリアルには、Azure CLI バージョン 2.0.4 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 アップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 ブラウザーから [Cloud Shell](/azure/cloud-shell/quickstart) を使用することもできます。

## <a name="availability-set-overview"></a>可用性セットの概要

可用性セットは、Azure で使用できる論理グループ作成機能であり、グループに配置された VM リソースは、Azure データ センター内にデプロイされるときに互いに分離されます。 Azure では、可用性セット内に配置された VM は、複数の物理サーバー、コンピューティング ラック、ストレージ ユニット、およびネットワーク スイッチ間で実行されます。 これにより、ハードウェアまたは Azure ソフトウェアの障害が発生した場合に影響を受けるのは VM のサブ セットに限定され、アプリケーション全体が停止することはなく、顧客は引き続きアプリケーションを利用できることが保証されます。 可用性セットの使用は、信頼性の高いクラウド ソリューションを構築する際に活用する不可欠の機能です。

4 台のフロント エンド Web サーバーとデータベースをホストする 2 台のバック エンド VM を使用する一般的な VM ベースのソリューションについて考えてみましょう。 Azure で VM をデプロイする前に、2 つの可用性セット ("Web" 階層用に 1 つ、"データベース" 層用に 1 つ) を定義します。 新しい VM を作成するときに、az vm create コマンドのパラメーターとして可用性セットを指定でき、可用性セット内に作成した VM は、Azure によって複数の物理的なハードウェア リソースに自動的に分離されます。 これは、いずれかの Web サーバーまたはデータベース サーバー VM で問題が発生した場合でも、Web サーバーとデータベース サーバー VM の他のインスタンスは別のハードウェアで実行されているため、正常に稼働し続けることを意味します。

Azure 内で信頼性の高い VM ベースのソリューションをデプロイする場合は、常に可用性セットを使用する必要があります。


## <a name="create-an-availability-set"></a>可用性セットの作成

可用性セットは、[az vm availability-set create](/cli/azure/availability-set#create) を使用して作成できます。 この例では、*myResourceGroupAvailability* リソース グループ内の *myAvailabilitySet* という名前の可用性セットに対して、更新ドメインと障害ドメインの両方の数として *2* を設定します。

リソース グループを作成します。

```azurecli
az group create --name myResourceGroupAvailability --location eastus
```


```azurecli
az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

可用性セットでは、"障害ドメイン" と "更新ドメイン" を分離できます。 **障害ドメイン**は、サーバーとネットワークとストレージ リソースの分離されたコレクションを表します。 前の例では、VM がデプロイされる際に、少なくとも 2 つの障害ドメインに可用性セットを分散することを指示しています。 さらに、可用性セットを 2 つの**更新ドメイン**に分散することも指示しています。  2 つの更新ドメインは、Azure でソフトウェアの更新が実行される際に VM リソースが分離され、VM の下で実行されているすべてのソフトウェアが同時に更新されることがないようにします。

## <a name="create-vms-inside-an-availability-set"></a>可用性セット内の VM の作成

VM は、ハードウェア全体で適切に分散させるために、可用性セット内に作成する必要があります。 可用性セットを作成した後に、既存の VM を追加することはできません。 

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

これで、新しく作成された可用性セット内に 2 つの仮想マシンが作成されます。 それらは同じ可用性セットに属しているため、VM と (データ ディスクを含む) すべてのリソースは、複数の分離された物理ハードウェアに分散されます。 この分散によって、VM ソリューション全体の可用性が大きく向上します。

VM を追加するときに発生することの 1 つは、特定の VM サイズを可用性セット内で使用できなくなることです。 この問題は、可用性セットに適用される分離規則を維持しながら追加するには、容量が不足している場合に発生する可能性があります。 `--availability-set list-sizes` パラメーターを使用して、既存の可用性セット内で使用できる VM サイズを確認できます。

## <a name="check-for-available-vm-sizes"></a>使用可能な VM のサイズのチェック 

可用性セットには後で VM をさらに追加することができますが、そのハードウェアで使用可能な VM のサイズを把握しておく必要があります。 ハードウェア クラスターで可用性セットに使用可能なすべてのサイズを一覧表示するには、[az vm availability-set list-sizes](/cli/azure/availability-set#list-sizes) を使用します。

```azurecli
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table  
```

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 可用性セットの作成
> * 可用性セット内に VM を作成する
> * 使用可能な VM のサイズを確認する

次のチュートリアルに進み、仮想マシンのスケール セットについて学習してください。

> [!div class="nextstepaction"]
> [VM スケール セットの作成](tutorial-create-vmss.md)



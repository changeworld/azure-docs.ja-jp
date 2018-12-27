---
title: Azure での Linux HPC Pack クラスター オプション | Microsoft Docs
description: Microsoft HPC Pack を使用して Azure クラウドで Linux ハイ パフォーマンス コンピューティング (HPC) クラスターを作成および管理するためのオプションについて学習します。
services: virtual-machines-linux,cloud-services
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: ac60624e-aefa-40c3-8bc1-ef6d5c0ef1a2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 79600909387b1876b112219b5b9b1e45ba4054b7
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340077"
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-linux-hpc-workloads-in-azure"></a>Azure で Linux HPC ワークロード用のクラスターを作成して管理するための HPC Pack のオプション
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

この記事では、HPC Pack を使用して Linux ワークロードを実行する Azure のオプションについて説明します。 [HPC Pack を使用して Windows HPC ワークロードを実行する](../windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)オプションもあります。

## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Azure VM と VM スケール セットの HPC Pack クラスター
### <a name="azure-resource-manager-templates"></a>Azure Resource Manager のテンプレート
* (GitHub) [HPC Pack 2016 Update 1 クラスター テンプレート](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [HPC Pack 2012 R2 クラスター テンプレート](https://github.com/MsHpcPack/HPCPack2012R2)
* (クイック スタート) [Linux コンピューティング ノードがある HPC Pack 2012 R2 クラスターを作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="azure-vm-images"></a>Azure VM イメージ
Azure で独自の HPC Pack クラスターを作成する場合は、[Azure Marketplace の HPC Pack イメージ](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22)を参照してください。

## <a name="hpc-pack-2012-r2-cluster-in-classic-deployment-model"></a>クラシック デプロイ モデルでの HPC Pack 2012 R2 クラスター
* [Create an HPC cluster with the Linux HPC Pack IaaS deployment script (HPC Pack IaaS デプロイメント スクリプトを使用した Linux HPC クラスターの作成)](../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [チュートリアル: Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [チュートリアル: Azure の Linux コンピューティング ノード上で Microsoft HPC Pack を使用して NAMD を実行する](classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [チュートリアル: Azure の Linux RDMA クラスター上で Microsoft HPC Pack を使用して OpenFOAM を実行する](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Tutorial: Run STAR-CCM+ with Microsoft HPC Pack on a Linux RDMA cluster in Azure (チュートリアル: Azure の Linux RDMA クラスターで Microsoft HPC Pack を使用して STAR-CCM+ を実行する)](classic/hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="job-submisssion"></a>ジョブの送信
* [オンプレミス コンピューターから Azure にデプロイされた HPC Pack クラスターに HPC ジョブを送信する](../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)





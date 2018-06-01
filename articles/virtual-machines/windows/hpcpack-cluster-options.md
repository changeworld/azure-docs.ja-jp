---
title: Azure での Windows HPC Pack クラスター オプション | Microsoft Docs
description: Microsoft HPC Pack を使用して Azure クラウドで Windows ハイ パフォーマンス コンピューティング (HPC) クラスターを作成および管理するためのオプションについて学習します。
services: virtual-machines-windows,cloud-services,batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 02c5566d-2129-483c-9ecf-0d61030442d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 1232228d5e2fcd05f41a096a933072dffcca2119
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165759"
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-windows-hpc-workloads-in-azure"></a>Azure で Windows HPC ワークロード用のクラスターを作成して管理するための HPC Pack のオプション
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

この記事では、Windows ワークロードの実行に使用する HPC Pack クラスターを作成するための Azure のオプションについて説明します。 また、[Linux HPC ワークロード](../linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を実行するのに使用する HPC Pack クラスターを作成するためのオプションもあります。


## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Azure VM と VM スケール セットの HPC Pack クラスター
### <a name="azure-resource-manager-templates"></a>Azure Resource Manager のテンプレート
* (GitHub) [HPC Pack 2016 Update 1 クラスター テンプレート](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [HPC Pack 2012 R2 クラスター テンプレート](https://github.com/MsHpcPack/HPCPack2012R2)
* (クイック スタート) [HPC Pack 2012 R2 クラスターを作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (クイック スタート) [カスタム コンピューティング ノード イメージを使用して HPC Pack 2012 R2 クラスターを作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Azure VM イメージ
Azure で独自の HPC Pack クラスターを作成する場合は、[Azure Marketplace の HPC Pack イメージ](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22)を参照してください。


### <a name="tutorials"></a>チュートリアル
* [チュートリアル: Azure に HPC Pack 2016 クラスターをデプロイする](hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure Active Directory を使用して Azure の HPC Pack 2016 クラスターを管理する](hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="hpc-pack-2012-r2-cluster-deployment-in-the-classic-deployment-model"></a>クラシック デプロイ モデルでの HPC Pack 2012 R2 クラスターのデプロイ
* [HPC Pack IaaS デプロイ スクリプトを使用し、Windows VM でハイ パフォーマンス コンピューティング (HPC) クラスターを作成する](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [チュートリアル: Azure の HPC Pack クラスターで Excel と SOA ワークロードを実行する](excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure の HPC Pack クラスターのコンピューティング ノードの数と可用性を管理する](classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [クラスターのワークロードに合わせて Azure の HPC Pack クラスター リソースを自動的に拡大縮小する](classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="burst-to-azure-from-hpc-pack-2012-r2"></a>Azure from HPC Pack 2012 R2 にバーストする
* [Microsoft HPC Pack を使用して Azure ワーカー インスタンスにバーストする](https://technet.microsoft.com/library/gg481749.aspx)
* [HPC Pack を使用した Azure Batch へのバースト](https://technet.microsoft.com/library/mt612877.aspx)
* [Azure で HPC Pack を使用してハイブリッド クラスターをセットアップする](../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

## <a name="job-submission"></a>ジョブの送信

* [オンプレミス コンピューターから Azure にデプロイされた HPC Pack クラスターに HPC ジョブを送信する](hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)







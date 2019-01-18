---
title: Azure Service Fabric でのノード タイプのリモート処理の方法 | Microsoft Docs
description: Azure Service Fabric でノード タイプを削除する方法について説明します
services: service-fabric
documentationcenter: .net
author: v-steg
manager: JeanPaul.Connick
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/26/2018
ms.author: v-steg
ms.openlocfilehash: 3704a356763b16a30285baee1aabffdd3aa3f8aa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53981888"
---
# <a name="remove-a-service-fabric-node-type"></a>Service Fabric ノード タイプを削除する

[Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) を使用して Service Fabric ノード タイプを削除します。

Remove-AzureRmServiceFabricNodeType が呼び出されたときには、次の 2 つの操作が発生します。
1.  ノード タイプの背後にある仮想マシン スケール セット (VMSS) が削除されます。
2.  そのノード タイプに含まれるすべてのノードについて、そのノード全体の状態がシステムから削除されます。 そのノード上にサービスが存在する場合、それらのサービスは最初に別ノードに移動されます。 クラスター マネージャーで、レプリカ/サービス用のノードを見つけられない場合、操作は遅延またはブロックされます。

> [!NOTE]
> 運用環境のクラスターからノード タイプを削除するために Remove-AzureRmServiceFabricNodeType を使用することは、頻繁に使用する方法としては推奨されません。 このコマンドでは、基本的に、ノード タイプの背後にある仮想マシン スケール セット リソースが削除されるため、そのような場合には非常に危険なコマンドです。 Remove-AzureRmServiceFabricNodeType の呼び出し時には、削除の安全性についてユーザーが注意しているかどうか、システム側で知る方法がありません。 

## <a name="durability-characteristics"></a>耐久性の特性
Remove-AzureRmServiceFabricNodeType の使用時には、速度よりも安全性が優先されます。 [耐久性の特性](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)としてはシルバーまたはゴールドをお勧めします。
- ブロンズでは、状態情報の保存に関する保証が一切提供されしません。
- シルバーとゴールドの耐久性では、VMSS に対するすべての変更がトラップされます。
- ゴールドでは、VMSS の下にある Azure の更新に対するコントロールも与えられます。

Service Fabric では、データが失われないように、基になっている変更や更新が "調整" されます。 ただし、ブロンズの耐久性を持つノードを削除すると、状態情報が失われる可能性があります。 プライマリ ノード タイプを削除しようとしていて、アプリケーションがステートレスである場合は、ブロンズで問題ありません。 運用環境でステートフル ワークロードを実行する場合は、最小構成をシルバーにする必要があります。 同様に、運用環境であれば、プライマリ ノード タイプは常にシルバーまたはゴールドにする必要があります。

### <a name="more-about-bronze-durability"></a>ブロンズの耐久性に関する詳細

ブロンズであるノード タイプを削除すると、そのノード タイプに含まれるすべてのノードが直ちに停止します。 Service Fabric では、ブロンズ ノードの VMSS 更新は一切トラップされないため、すべての VM が直ちに停止します。 それらのノードに何かステートフルなものがあった場合、データは失われます。 ステートレスであった場合でも、Service Fabric 内にあるノードはすべてリングに参加しているため、近隣全体が失われる可能性があります。これは、クラスター自体に影響を及ぼすことがあります。

1 つのノードを削除することとは異なり、理論上は、一度に 1 つのノードを削除できます。そしてレプリカとサービスが移行されるのを待ち、システムが安定するのを待ってから、別のノードを削除します。これを続けるのです。  ただし、一度に複数のノードを同時に削除する場合、クラスターが停止する可能性があります (Service Fabric ではブロンズの耐久性を持つ VMSS の更新を一切トラップしないため)。

## <a name="recommended-node-type-removal-process"></a>推奨されるノード タイプの削除プロセス

最も安全かつ迅速な方法でノード タイプを削除するには:
1.  ブロンズの耐久性を使用している場合や、ノード タイプの削除の一部として失われる状態情報を含むアプリケーションをシステムで移動したくない場合は、最初に、ノード タイプの削除によって影響を受けるノードから、ステートフル データをなくします。
2.  削除する各ノードで [Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) を実行します。
3.  ノード タイプの削除の影響を受ける VM に対して [Remove-AzureRmVmss](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#remove-vms-from-a-scale-set) を実行します。
4. [Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) を実行してノード タイプを削除します。

## <a name="next-steps"></a>次の手順
- クラスターの[耐久性の特性](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)についてさらに学習します。
- [ノード タイプと Virtual Machine Scale Sets](service-fabric-cluster-nodetypes.md) についてさらに学習します。
- [Service Fabric クラスターのスケーリング](service-fabric-cluster-scaling.md)についてさらに学習します。
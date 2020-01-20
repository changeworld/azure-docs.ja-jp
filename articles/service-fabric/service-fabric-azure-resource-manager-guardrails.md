---
title: Service Fabric Azure Resource Manager デプロイのガードレール
description: この記事では、Azure Resource Manager を使用して Service Fabric クラスターをデプロイするときの一般的な間違いの概要と、それらを回避する方法について説明します。
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: pepogors
ms.openlocfilehash: fe5ff2a5eeb4b2c73165d1577702eb6af7079b61
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426733"
---
# <a name="service-fabric-guardrails"></a>Service Fabric のガードレール 
Service Fabric クラスターをデプロイするとき、ガードレールが設定されています。これにより、無効なクラスター構成の場合、Azure Resource Manager のデプロイは失敗します。 以下のセクションでは、クラスター構成に関する一般的な問題の概要と、これらの問題を軽減するために必要な手順について説明します。 

## <a name="durability-mismatch"></a>持続性の不一致
### <a name="overview"></a>概要
Service Fabric ノード タイプの持続性の値は、Azure Resource Manager テンプレートの 2 つの異なるセクションで定義されます。 仮想マシン スケール セット リソースの仮想マシン スケール セット拡張機能のセクションと、Service Fabric クラスター リソースのノード タイプのセクションです。 これらのセクションの持続性の値が一致している必要があります。そうしないと、リソースのデプロイは失敗します。

次のセクションでは、仮想マシン スケール セット拡張機能の持続性の設定と、Service Fabric ノード タイプの持続性の設定との、持続性の不一致の例を示します。  

**仮想マシン スケール セットの持続性の設定**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**Service Fabric ノード タイプの持続性の設定** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>エラー メッセージ
* Virtual Machine Scale Set durability mismatch does not match the current Service Fabric Node Type durability level (仮想マシン スケール セットの持続性の不一致で、現在の Service Fabric ノード タイプの持続性レベルと一致しません)
* Virtual Machine Scale Set durability does not match the target Service Fabric Node Type durability level (仮想マシン スケール セットの持続性がターゲットの Service Fabric ノード タイプの持続性レベルと一致しません)
* Virtual Machine Scale Set durability does match the current Service Fabric durability level or the target Service Fabric Node Type durability level (仮想マシン スケール セットの持続性が、現在の Service Fabric の持続性レベルまたはターゲットの Service Fabric ノード タイプの持続性レベルと一致しません) 


### <a name="mitigation"></a>対応策
上記のいずれかのエラー メッセージによって示される持続性の不一致を修正するには、次のようにします。
1. Azure Resource Manager テンプレートの仮想マシン スケール セット拡張機能または Service Fabric ノード タイプのセクションで、値が一致するように持続性レベルを更新します。
2. 更新された値を使用して Azure Resource Manager テンプレートを再デプロイします。

## <a name="next-steps"></a>次のステップ
* Windows Server を実行している VM またはコンピューター上にクラスターを作成する:[Windows Server 用の Service Fabric クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)
* Linux を実行している VM またはコンピューター上にクラスターを作成する:[Linux クラスターの作成](service-fabric-cluster-creation-via-portal.md)
* Service Fabric のトラブルシューティング:[トラブルシューティング ガイド](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)

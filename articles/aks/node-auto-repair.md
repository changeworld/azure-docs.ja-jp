---
title: Azure Kubernetes Service (AKS) ノードの自動修復
description: ノードの自動修復機能と壊れたワーカー ノードを AKS で修復するしくみについて説明します。
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284842"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Azure Kubernetes Service (AKS) ノードの自動修復

AKS によってワーカー ノードの正常性状態が継続的に確認され、正常ではなくなった場合、ノードが自動修復されます。 このドキュメントでは、Azure Kubernetes Service (AKS) でワーカー ノードを監視し、正常ではないワーカー ノードを修復するしくみについて説明します。  このドキュメントは、ノード修復機能の動作を AKS オペレーターに伝えるためのものです。 Azure プラットフォームでは、問題が発生した[仮想マシンが保守される][vm-updates]ことにも留意しておくことが重要です。 AKS と Azure が連携し、クラスターのサービス中断が最小限に抑えられます。

> [!Important]
> Windows Server ノード プールには現在、ノードの自動修復機能はサポートされていません。

## <a name="how-aks-checks-for-unhealthy-nodes"></a>正常ではないノードを AKS で確認する方法

> [!Note]
> AKS では、ユーザー アカウント **aks-remediator** を利用してノードが修復されます。

AKS では、ノードが正常な状態ではなく、修復が必要かどうかを、ルールを利用して決定します。 AKS では、自動修復が必要かどうかを、次のルールで決定します。

* 10 分という時間枠内で連続して確認した結果、**NotReady** という状態がノードから報告されます
* ノードから、10 分以内に状態が報告されません

kubectl を利用し、ノードの正常性状態を手動確認できます。 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>自動修復のしくみ

> [!Note]
> AKS では、ユーザー アカウント **aks-remediator** を利用してノードが修復されます。

この動作は**仮想マシン スケール セット**の場合です。  自動修復では、数回の手順を踏んで壊れたノードが修復されます。  ノードが正常ではないと判断された場合、AKS によっていくつかの修復手順が試行されます。  手順は次の順序で実行されます。

1. コンテナー ランタイムが 10 分間応答しないとき、失敗したランタイム サービスがノード上で再起動されます。
2. ノードの用意が 10 分以内に整わない場合、ノードが再起動されます。
3. ノードの用意が 30 分以内に整わない場合、ノードが再イメージ化されます。

> [!Note]
> 複数のノードが正常ではない場合、1 つずつ修復されます。

## <a name="next-steps"></a>次のステップ

[Availability Zones][availability-zones] を使用し、AKS クラスター ワークロードで高可用性を上げます。

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md

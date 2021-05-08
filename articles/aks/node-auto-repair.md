---
title: Azure Kubernetes Service (AKS) ノードの自動修復
description: ノードの自動修復機能と壊れたワーカー ノードを AKS で修復するしくみについて説明します。
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 341aef394a3784edbc0acd91dad396c9794da3d0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105206"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Azure Kubernetes Service (AKS) ノードの自動修復

AKS によってワーカー ノードの正常性状態が継続的に監視され、正常ではなくなった場合、ノードの自動修復が実行されます。 Azure 仮想マシン (VM) プラットフォームでは、問題が発生している [VM に対してメンテナンスが実行][vm-updates]されます。 

AKS と Azure VM が連携し、クラスターのサービス中断が最小限に抑えられます。

このドキュメントでは、Windows ノードと Linux ノードの両方の、ノードの自動修復機能について説明します。 

## <a name="how-aks-checks-for-unhealthy-nodes"></a>正常ではないノードを AKS で確認する方法

AKS では、ノードが正常ではなく、修復が必要かどうかを、次のルールを利用して判断します。 
* 10 分という時間枠内で連続して確認した結果、**NotReady** 状態がノードから報告されます。
* ノードからは、10 分以内に何の状態も報告されません。

kubectl を利用し、ノードの正常性状態を手動確認できます。

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>自動修復のしくみ

> [!Note]
> AKS が、ユーザー アカウント **AKS-remediator** を使用して修復操作を開始します。

AKS により 10 分間異常な状態にある異常なノードが識別されると、次のアクションが実行されます。

1. ノードを再起動します。
1. 再起動に失敗した場合は、ノードを再イメージ化します。
1. 再イメージ化に失敗した場合は、新しいノードを作成して再イメージ化します。

自動修復に失敗した場合、AKS エンジニアによって代替の修復が調査されます。 

正常性チェック中に AKS で複数の異常なノードが検出された場合、各ノードを個別に修復してから、別の修復が開始されます。

## <a name="next-steps"></a>次のステップ

[Availability Zones][availability-zones] を使用し、AKS クラスター ワークロードで高可用性を上げます。

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md

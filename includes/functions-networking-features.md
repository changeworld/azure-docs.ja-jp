---
ms.openlocfilehash: 82571d1a0e651f638dec29184f0ecdc88562b3ad
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578830"
---


| 特徴量 |[従量課金プラン](../articles/azure-functions/functions-scale.md#consumption-plan)|[Premium プラン](../articles/azure-functions/functions-scale.md#premium-plan)|[専用プラン](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[受信 IP の制限とプライベート サイト アクセス](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅はい|✅はい|✅はい|✅はい|✅はい|
|[仮想ネットワークの統合](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌いいえ|✅はい (リージョン)|✅はい (リージョンとゲートウェイ)|✅はい| ✅はい|
|[仮想ネットワーク トリガー (非 HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌いいえ| ✅はい |✅はい|✅はい|✅はい|
|[ハイブリッド接続](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (Windows のみ)|❌いいえ|✅はい|✅はい|✅はい|✅はい|
|[送信 IP の制限](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌いいえ| ✅はい|✅はい|✅はい|✅はい|
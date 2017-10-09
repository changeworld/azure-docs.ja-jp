---
title: "Azure Stack ネットワークの概要"
description: "Azure Stack ネットワークについて"
services: azure-stack
author: ScottNapolitan
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: victorh
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 02406ef396fdaa40cb4d46f32d0b2b00c4ec295e
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---
# <a name="introduction-to-azure-stack-networking"></a>Azure Stack ネットワークの概要

*適用先: Azure Stack 統合システムおよび Azure Stack Development Kit*

Azure Stack には、単独でまたは組み合わせて使用できるさまざまなネットワーク機能が用意されています。
- Azure Stack リソース間の接続: クラウド内の安全なプライベート仮想ネットワークで Azure のリソースを接続します。
- インターネット接続: インターネットを介して双方向で Azure Stack リソースと通信します。
- オンプレミス接続: インターネット上の仮想プライベート ネットワーク (VPN) を介して、または Azure Stack への専用接続を介して、オンプレミスのネットワークを Azure Stack リソースに接続します。
- 負荷分散とトラフィック誘導: 同じ場所のサーバー間でトラフィックを負荷分散したり、異なる場所のサーバーにトラフィックを誘導したりします。
- セキュリティ: ネットワーク サブネット間や個々の仮想マシン (VM) 間のネットワーク トラフィックをフィルタリングします。
- ルーティング: Azure Stack リソースとオンプレミス リソースとの間で既定のルーティングまたは完全制御のルーティングを使用します。
- 管理の容易性: Azure Stack のネットワーク リソースを監視したり管理したりします。
- デプロイ ツールと構成ツール: Web ベースのポータルまたはクロスプラットフォームのコマンド ライン ツールを使って、ネットワーク リソースのデプロイと構成を行います。


## <a name="next-steps"></a>次のステップ
* [Azure Stack ネットワークに関する考慮事項](azure-stack-network-differences.md)



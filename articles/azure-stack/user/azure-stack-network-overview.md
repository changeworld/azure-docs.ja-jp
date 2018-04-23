---
title: Azure Stack ネットワークの概要
description: Azure Stack ネットワークについて
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 04/09/2018
ms.author: mabrigg
ms.openlocfilehash: 6d08d11a3768b2db909920058dcb0b5a1fcb1534
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="introduction-to-azure-stack-networking"></a>Azure Stack ネットワークの概要

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack には、単独でまたは組み合わせて使用できるさまざまなネットワーク機能が用意されています。
- Azure Stack リソース間の接続: クラウド内の安全なプライベート仮想ネットワークで Azure のリソースを接続します。
- インターネット接続: インターネットを介して双方向で Azure Stack リソースと通信します。
- オンプレミス接続: インターネット上の仮想プライベート ネットワーク (VPN) を介して、または Azure Stack への専用接続を介して、オンプレミスのネットワークを Azure Stack リソースに接続します。
- 負荷分散とトラフィック誘導: 同じ場所のサーバー間でトラフィックを負荷分散したり、異なる場所のサーバーにトラフィックを誘導したりします。
- セキュリティ: ネットワーク サブネット間や個々の仮想マシン (VM) 間のネットワーク トラフィックをフィルタリングします。
- ルーティング: Azure Stack リソースとオンプレミス リソースとの間で既定のルーティングまたは完全制御のルーティングを使用します。
- 管理の容易性: Azure Stack のネットワーク リソースを監視したり管理したりします。
- デプロイ ツールと構成ツール: Web ベースのポータルまたはクロスプラットフォームのコマンド ライン ツールを使って、ネットワーク リソースのデプロイと構成を行います。


## <a name="next-steps"></a>次の手順
* [Azure Stack ネットワークに関する考慮事項](azure-stack-network-differences.md)


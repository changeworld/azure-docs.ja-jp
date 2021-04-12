---
title: Azure ネットワーク アーキテクチャに関するドキュメント
description: Azure ネットワーク サービスで使用できる参照アーキテクチャのドキュメントについて説明します。
services: networking
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 03/30/2021
ms.author: kumud
ms.openlocfilehash: 9b608312d66e6a3e7455c4577ea4644b33e4e82e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079816"
---
# <a name="azure-networking-architecture-documentation"></a>Azure ネットワーク アーキテクチャに関するドキュメント

この記事では、アプリケーションを構築するために使用できる Azure のさまざまなネットワーク サービスを調べるのに役立つアーキテクチャ ガイドに関する情報を提供します。

## <a name="networking-overview"></a>ネットワークの概要

次の表には、Azure の仮想データセンターとハブとスポークのトポロジに関するネットワークの概要を説明する記事が含まれています。

|タイトル |説明  |
|---------|---------|
|[仮想データセンター](/azure/architecture/vdc/networking-virtual-datacenter)   | Azure の仮想データセンターについて、ネットワークからの視点を示します。       |
|[ハブスポーク トポロジ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)  |Azure におけるハブとスポークのネットワーク トポロジの概要と、サブスクリプションの制限および複数のハブに関する情報を提供します。          |

## <a name="connect-to-azure-resources"></a>Azure リソースに接続する

次の表に、Azure リソース間の接続、オンプレミス ネットワークから Azure リソースへの接続、および Azure 内でブランチ間の接続を提供する Azure ネットワーク サービスに関する記事を示します。

|タイトル |説明  |
|---------|---------|
|[ピアリングされた仮想ネットワークに IP アドレス空間を追加する](/azure/architecture/networking/prefixes/add-ip-space-peered-vnet)     | ピアリングされた仮想ネットワークに IP アドレス空間を追加するのに役立つスクリプトを提供します。        |
|[Azure ネットワーク アダプターを使用したスタンドアロン サーバーの接続](/azure/architecture/hybrid/azure-network-adapter)   | Windows Admin Center を介してデプロイする Azure ネットワーク アダプターを使用して、オンプレミスのスタンドアロン サーバーを Microsoft Azure 仮想ネットワークに接続する方法について説明します。        |
|[仮想ネットワーク ピアリングと VPN ゲートウェイのいずれかを選択](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering)   | Azure 内で仮想ネットワークを接続するための 2 つの方法 (仮想ネットワーク ピアリングと VPN ゲートウェイ) を比較します。        |
|[オンプレミス ネットワークの Azure への接続](/azure/architecture/reference-architectures/hybrid-networking/)  | オンプレミス ネットワークを Azure Virtual Network (VNet) に接続するためのオプションを比較します。 各オプションには、さらに詳細な参照アーキテクチャが用意されています。        |
|[Azure Virtual WAN を使用した SD-WAN 接続アーキテクチャ](../../virtual-wan/sd-wan-connectivity-architecture.md)|プライベートの SD-WAN (Software Defined WAN) と Azure Virtual WAN を相互接続するためのさまざまな接続オプションについて説明します。|

## <a name="deploy-highly-available-applications"></a>高可用性アプリケーションをデプロイする

次の表に、Azure ネットワーク サービスの組み合わせを使用して高可用性を実現するためにアプリケーションをデプロイする方法を説明している記事を示します。

|タイトル |説明  |
|---------|---------|
|[マルチリージョン n 層アプリケーション](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)  | Traffic Manager を使用して受信要求をプライマリ リージョンにルーティングするマルチリージョンの n 層アプリケーションについて説明します。そのリージョンが使用できなくなった場合、Traffic Manager はセカンダリ リージョンにフェールオーバーします。      |
| [Azure のマルチテナント SaaS](https://docs.microsoft.com/azure/architecture/example-scenario/multi-saas/multitenant-saas)       |   Front Door と Application Gateway の組み合わせを含むマルチテナント ソリューションを使用します。  Front Door は、リージョン間でトラフィックを負荷分散します。Application Gateway は、アプリケーション内部で、クライアントのビジネス ニーズを満たすさまざまなサービスにトラフィックをルーティングおよび負荷分散します。  |
| [高可用性とディザスター リカバリー用にビルドされた多層 Web アプリケーション](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)        |      高可用性とディザスター リカバリーを実現するために構築された、回復性がある多層アプリケーションをデプロイします。 プライマリ リージョンが使用できなくなった場合、Traffic Manager はセカンダリ リージョンへのフェールオーバーを実行します。  |
|[IaaS:Web アプリケーションとリレーショナル データベース](/azure/architecture/high-availability/ref-arch-iaas-web-and-db)    |   複数のゾーンに分散したリソースを使用して、IaaS (サービスとしてのインフラストラクチャ) Web アプリケーションと SQL Server データベースをホストするための高可用性アーキテクチャを提供する方法について説明します。     |
|[低コストのサーバーレス Azure サービスを使用して、リアルタイムで位置情報を共有する](/azure/architecture/example-scenario/signalr/#azure-front-door)       |   Azure Front Door を使用して、1 つのリージョンにデプロイするよりも、アプリケーションの可用性を高めます。 地域的な停止がプライマリ リージョンに影響する場合は、Front Door を使用して、セカンダリ リージョンにフェールオーバーできます。      |
|[高可用性ネットワーク仮想アプライアンス](/azure/architecture/reference-architectures/dmz/nva-ha)     | 高可用性のネットワーク仮想アプライアンス (NVA) セットを Azure にデプロイする方法を示します。        |

## <a name="secure-your-network-resources"></a>ネットワーク リソースをセキュリティで保護する

次の表に、Azure ネットワーク サービスを使用してネットワーク リソースを保護する方法について説明している記事を示します。

|タイトル |説明  |
|---------|---------|
|[ネットワーク セキュリティのベスト プラクティス](../../security/fundamentals/network-best-practices.md) |お使いのネットワーク セキュリティを強化するための Azure のベスト プラクティスについて説明します。         |
[Azure Firewall アーキテクチャ ガイド](/azure/architecture/example-scenario/firewalls/) | サードパーティの仮想アプライアンスを使用して、Azure 内に高可用性ファイアウォールを設計するための体系的なアプローチについて説明します。        |
|[セキュリティ保護されたハイブリッド ネットワークを実装する](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)     | オンプレミス ネットワークと Azure の仮想ネットワークの間に DMZ (境界ネットワークとも呼ばれる) を実装するアーキテクチャについて説明します。 すべての受信トラフィックと送信トラフィックは Azure Firewall を通過します。        |
|[ネットワーク レベルのセグメント化を使用してワークロードをセキュリティで保護および管理する](/azure/architecture/reference-architectures/hybrid-networking/network-level-segmentation) | ネットワークの観点から、Azure でワークロードを整理するために使用される 3 つの一般的なパターンについて説明します。   これらの各パターンでは、さまざまな種類の分離と接続が提供されます。      |
|[仮想ネットワークのファイアウォールと Application Gateway](/azure/architecture/example-scenario/gateway/firewall-application-gateway) | Azure Firewall や Azure Application Gateway などの Azure Virtual Network セキュリティ サービス、各サービスを使用すべき状況、両方を組み合わせるネットワーク設計オプションについて説明します。      |

## <a name="next-steps"></a>次のステップ

[Azure Virtual Network](../../virtual-network/virtual-networks-overview.md) について説明します。

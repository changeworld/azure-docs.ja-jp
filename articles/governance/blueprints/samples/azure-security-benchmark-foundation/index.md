---
title: Azure セキュリティ ベンチマーク基盤ブループリント サンプルの概要
description: Azure セキュリティ ベンチマーク基盤ブループリント サンプルの概要とアーキテクチャ。
ms.date: 02/17/2020
ms.topic: sample
ms.openlocfilehash: ed497eff85e07b6a51939907bc751f3b40c99b30
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741873"
---
# <a name="overview-of-the-azure-security-benchmark-foundation-blueprint-sample"></a>Azure セキュリティ ベンチマーク基盤ブループリント サンプルの概要

Azure セキュリティ ベンチマーク基盤ブループリント サンプルは、コンプライアンスに準拠している安全な Azure 環境を構築するのに役立つ一連のベースライン インフラストラクチャ パターンを提供します。 このブループリントは、認定またはコンプライアンスの要件があるシナリオにソリューションを提供するクラウドベースのアーキテクチャをデプロイするのに役立ちます。 この基本ブループリント サンプルは、[Azure セキュリティ ベンチマーク ブループリント サンプル](../azure-security-benchmark.md)を拡張したものです。 これにより、[Azure セキュリティ ベンチマーク](../../../../security/benchmarks/index.yml)で定義されたポリシーとその他のガードレールに沿って、ネットワーク境界、監視、その他のリソースをデプロイして構成できます。

## <a name="architecture"></a>Architecture

このブループリント サンプルによって作成される基本環境は、[ハブ アンド スポーク モデル](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)というアーキテクチャ原則に基づいています。
このブループリントでは、一般的な共通のリソース、サービス、アーティファクトが含まれているハブ仮想ネットワークをデプロイします。たとえば、Azure Bastion や接続用のゲートウェイとファイアウォールのほか、管理、メンテナンス、運用、接続用のその他 (オプション) のインフラストラクチャをホストするための管理およびジャンプ ボックス サブネットなどが含まれます。 1 つ以上のスポーク仮想ネットワークがホスト アプリケーション ワークロード (Web やデータベースのサービスなど) にデプロイされます。 シームレスで安全な接続を実現するために、Azure 仮想ネットワーク ピアリングを使用してスポーク仮想ネットワークがハブ仮想ネットワークに接続されます。 サンプル ブループリントを再割り当てするか、Azure 仮想ネットワークを手動で作成してハブ仮想ネットワークとピアリングするかして、さらにスポークを追加することができます。 スポーク仮想ネットワークとサブネットへのすべての外部接続は、ハブ仮想ネットワーク、ファイアウォール、ゲートウェイ、管理ジャンプ ボックスを通じてルーティングされるよう構成されています。

:::image type="content" source="../../media/azure-security-benchmark-foundation/architecture.png" alt-text="Azure セキュリティ ベンチマーク基盤ブループリント サンプルのアーキテクチャ図" border="false":::

このブループリントでは、セキュリティで保護され、監視されたエンタープライズ対応の基盤を実現するために、複数の Azure サービスをデプロイします。 この環境は、以下で構成されます。

- [Azure Monitor ログ](../../../../azure-monitor/logs/data-platform-logs.md)と Azure ストレージ アカウント。クエリ、分析、アーカイブ、アラートを簡単に実行できるように、リソース ログ、アクティビティ ログ、メトリック、ネットワーク トラフィック フローを一元的な場所に確実に格納します。
- [Azure Security Center](../../../../security-center/security-center-introduction.md) (標準バージョン)。Azure リソースを脅威から保護します。
- [Azure Virtual Network](../../../../virtual-network/virtual-networks-overview.md) (ハブ内)。オンプレミス ネットワークに接続するためのサブネット、インターネット接続用のイングレス/エグレス スタック、追加の運用または管理サービスをデプロイするためのオプションのサブネットをサポートします。 スポーク内の仮想ネットワークには、アプリケーション ワークロードをホストするためのサブネットが含まれています。 該当するシナリオに対応するために、必要に応じてデプロイ後に追加のサブネットを作成することができます。
- [Azure Firewall](../../../../firewall/overview.md)。すべてのアウトバウンド インターネット トラフィックをルーティングして、ジャンプ ボックス経由でインバウンド インターネット トラフィックを有効にします。 (既定のファイアウォール ルールでは、インバウンドとアウトバウンドのインターネット トラフィックがすべてブロックされるため、デプロイ後に適宜、ルールを構成する必要があります。)
- [ネットワーク セキュリティ グループ](../../../../virtual-network/network-security-group-how-it-works.md) (NSG)。Azure Bastion、ゲートウェイ、Azure Firewall などのサービス用サブネットを除き、すべてのサブネットに割り当てられます。インバウンドとアウトバウンドのインターネット トラフィックをすべてブロックするよう構成されています。
- [アプリケーション セキュリティ グループ](../../../../virtual-network/application-security-groups.md)。共通のネットワーク セキュリティ ポリシーを適用するために、Azure 仮想マシンをグループ化します。
- [ルート テーブル](../../../../virtual-network/manage-route-table.md)。ファイアウォールを通じて、サブネットからのすべてのアウトバウンド インターネット トラフィックをルーティングします。 (デプロイ後、Azure Firewall と NSG ルールは接続を開くために構成する必要があります。)
- [Azure Network Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md)。Azure 仮想ネットワーク内のリソースの監視、診断、メトリックの表示を行います。
- [Azure DDoS Protection Standard](../../../../ddos-protection/ddos-protection-overview.md)。Azure リソースを DDoS 攻撃から保護します。
- [Azure Bastion](../../../../bastion/bastion-overview.md)。パブリック IP アドレス、エージェント、または特別なクライアント ソフトウェアなしでシームレスかつ安全に仮想マシンに接続できるようにします。
- [Azure VPN Gateway](../../../../vpn-gateway/vpn-gateway-about-vpngateways.md)。Azure 仮想ネットワークとオンプレミスの場所の間におけるパブリック インターネット経由のトラフィックを暗号化します。

> [!NOTE] 
> Azure セキュリティ ベンチマーク基盤は、ワークロードの基本アーキテクチャを示します。 上のアーキテクチャ図には、考えられるサブネットの使用方法を示すために現実的でないリソースがいくつか含まれています。 それでも、この基本アーキテクチャに基づいてワークロードをデプロイする必要があります。

## <a name="next-steps"></a>次のステップ

Azure セキュリティ ベンチマーク基盤ブループリント サンプルの概要とアーキテクチャを確認しました。

> [!div class="nextstepaction"]
> [Azure セキュリティ ベンチマーク基盤ブループリント - デプロイ手順](./deploy.md)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。
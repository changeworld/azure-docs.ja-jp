---
title: Azure Virtual Network (プレビュー) の IPv6 の概要
titlesuffix: Azure Virtual Network
description: Azure 仮想ネットワーク内の IPv6 エンドポイントおよびデータ パスを IPv6 の観点から説明します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/19/2019
ms.author: kumud
ms.openlocfilehash: 9214886f468a4a052328a99289845361a059b650
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780081"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Azure Virtual Network の IPv6 の概要 (プレビュー)

Azure Virtual Network (VNet) の IPv6 により、仮想ネットワークやインターネットで IPv6 接続や IPv4 接続を使用するアプリケーションを Azure 内でホストできます。 パブリック IPv4 アドレスが枯渇したことから、モビリティおよびモノのインターネット (IoT) 向けの新しいネットワークは、IPv6 をベースに構築されることが多くなっています。 老舗の ISP やモバイル ネットワークでも IPv6 への転換が進められています。 IPv4 専用のサービスは、既存の市場でも新興市場でも実際に不利になる場合があります。 デュアル スタック IPv4/IPv6 接続により、Azure でホストされるサービスは、このテクノロジのギャップを乗り越えることができます。これにより、既存の IPv4 用およびこの新しい IPv6 用のどちらのデバイスおよびネットワークにも簡単に接続できる、グローバルに使用可能なデュアル スタック サービスが実現されます。

Azure の最初の IPv6 接続により、Azure 内でホストされるアプリケーションでは、デュアル スタック (IPv4/IPv6) インターネット接続を簡単に利用できるようになりました。 接続がインバウンドで開始される場合でも、アウトバウンドで開始される場合でも、IPv6 接続が負荷分散されるため、VM のデプロイが簡単になります。 この機能は依然として使用できます。詳細については、[こちら](../load-balancer/load-balancer-ipv6-overview.md)を参照してください。
Azure 仮想ネットワークの IPv6 は、はるかに充実した機能を備えているため、Azure 内に IPv6 ソリューション アーキテクチャ全体をデプロイできます。

> [!Important]
> 現在、Azure Virtual Network の IPv6 は、パブリック プレビュー段階です。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。

次の図は、Azure 内のシンプルなデュアル スタック (IPv4/IPv6) デプロイメントを示しています。

![IPv6 ネットワーク デプロイメント図](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>メリット

Azure VNet の IPv6 の利点:

- Azure でホストされるアプリケーションが、成長中のモバイル市場やモノのインターネット市場で利用できるようになります。
- デュアル スタック IPv4/IPv6 VM により、サービスをきわめて柔軟にデプロイできます。 単一のサービス インスタンスから IPv4 対応のインターネット クライアントにも、IPv6 対応のインターネット クライアントにも接続できます。
- 安定した既存の Azure VM およびインターネット間の IPv6 接続を基礎にしています。
- インターネットに対する IPv6 接続は、デプロイメント内で明示的に要求して初めて確立されるため、既定で安全です。

## <a name="capabilities"></a>機能

Azure VNet の IPv6 には、以下の機能が含まれています。

- Azure のお客様は、アプリケーションやお客様のニーズを満たすように IPv6 仮想ネットワーク アドレス空間を定義できます。また、オンプレミス IP 空間にシームレスに統合するように IPv6 仮想ネットワーク アドレス空間を定義することもできます。
- デュアル スタック (IPv4 および IPv6) 仮想ネットワークとデュアル スタック サブネットにより、アプリケーションは、仮想ネットワークやインターネット内の IPv4 リソースと IPv6 リソースの両方に接続できます。
    > [!IMPORTANT]
    > IPv6 のサブネットは、正確に /64 のサイズである必要があります。  これにより、一部のルーターが /64 の IPv6 ルートしか受け付けることができないために、サブネットのオンプレミス ネットワークへのルーティングを有効にすることにした場合の将来の互換性が保証されます。  
- ネットワーク セキュリティ グループに対する IPv6 規則でリソースを保護します。
    - また、Azure プラットフォームによる分散型サービス拒否 (DDoS) の保護が、インターネットに接続されたパブリック IP アドレスに拡張されます
- 仮想ネットワーク内の IPv6 トラフィックのルーティングを、ユーザー定義ルートを使用してカスタマイズできます。これは特に、ネットワーク仮想アプライアンスを利用してアプリケーションを拡張しているときに便利です。
- Linux および Windows Virtual Machines はすべて、Azure VNET の IPv6 を使用できます
- 回復性があるスケーラブルなアプリケーションを作成するための [Standard IPv6 パブリック Load Balancer](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) のサポート。これには、次のものが含まれます。
    - IPv6 正常性プローブ (オプション)。どのバックエンド プール インスタンスが正常であり、新しいフローを受信できるかを判断します。
    - アウトバウンド規則 (オプション)。アウトバウンド接続の完全な宣言的制御を提供し、特定のニーズに合わせてこの機能をスケーリングおよび調整できます。
    - 複数のフロントエンド構成 (オプション)。これにより、単一のロード バランサーで複数の IPv6 パブリック IP アドレスを使用できます。フロントエンド アドレスをまたいで同じフロントエンド プロトコルとポートを再利用できます。
    - 負荷分散規則の *Floating IP* 機能を使用して、オプションの IPv6 ポートをバックエンド インスタンスで再利用できます 
- Azure VNET 内に弾力性のある多層アプリケーションを作成するための、[Standard IPv6 内部 Load Balancer](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) のサポート。  
- レガシ デプロイとの互換性のための Basic IPv6 パブリック Load Balancer のサポート
- [予約された IPv6 パブリック IP アドレスおよびアドレス範囲](ipv6-public-ip-address-prefix.md)により、安定した予測可能な IPv6 アドレスが提供されます。これにより、会社や顧客向けに Azure でホストされるアプリケーションを簡単にホワイトリストに登録できます。
- インスタンスレベル パブリック IP アドレスにより、個々の VM に対する IPv6 インターネットの直接接続が提供されます。
- [既存の IPv4 のみのデプロイに対する IPv6 の追加](ipv6-add-to-existing-vnet-powershell.md) - この機能を使うと、既存の IPv4 のみのデプロイに IPv6 接続を簡単に追加でき、デプロイを作成しなおす必要はありません。  このプロセスの間、IPv4 のネットワーク トラフィックは影響を受けないため、アプリケーションと OS によっては、ライブ サービスに対しても IPv6 を追加できる場合があります。    
- インターネット クライアントが、最適なプロトコルと、IPv6 (AAAA) レコードに対する Azure DNS サポートを使用して、デュアル スタック アプリケーションにシームレスにアクセスできるようにします。 
- IPv6 の仮想マシン スケール セットにより、負荷に応じて自動的に拡大縮小するデュアル スタック アプリケーションを作成できます。
- [仮想ネットワーク (VNET) ピアリング](virtual-network-peering-overview.md) - リージョン内ピアリングとグローバル ピアリングの両方。デュアル スタック VNET をシームレスに接続できます。ピアリングされたネットワーク内の VM 上の IPv4 エンドポイントと IPv6 エンドポイントの両方が、相互に通信できるようになります。 デプロイをデュアル スタックに移行するときに、IPv4 のみの VNET でデュアル スタックをピアリングすることもできます。 
- IPv6 のトラブルシューティングと診断は、ロード バランサーのメトリック/アラート、およびパケット キャプチャ、NSG フロー ログ、接続のトラブルシューティング、接続の監視などの Network Watcher の機能を利用して、行うことができます。   

## <a name="scope"></a>スコープ
Azure VNET の IPv6 は、お客様が Azure でデュアル スタック (IPv4 + IPv6) アプリケーションをホストできるようにするための基本機能セットです。  時間をかけてより多くの Azure ネットワーク機能に IPv6 のサポートを追加し、最終的には Azure PaaS サービスのデュアル スタック バージョンを提供する予定ですが、それまでの間は、デュアル スタック仮想マシンの IPv4 エンドポイント経由で、すべての Azure PaaS サービスにアクセスできます。   

## <a name="limitations"></a>制限事項
Azure 仮想ネットワークの IPv6 の現在のリリースには、次の制限があります。
- Azure 仮想ネットワーク (プレビュー) の IPv6 は、すべてのグローバル Azure リージョン内で利用できます。ただし、利用できるのは、グローバル Azure 内のみで、政府のクラウド内ではまだ利用できません。
- ExpressRoute および VPN ゲートウェイは、IPv6 が有効になっている VNET では、直接でも、"UseRemoteGateway" とのピアリングでも、使用できません。 
- Azure プラットフォーム (AKS など) では、コンテナーに対する IPv6 通信はサポートされていません。  

## <a name="pricing"></a>価格

Azure の IPv6 のリソースおよび帯域幅は、IPv4 と同じレートで課金されます。 IPv6 の場合であっても、料金の追加や変更はありません。 [パブリック IP アドレス](https://azure.microsoft.com/pricing/details/ip-addresses/)、[ネットワーク帯域幅](https://azure.microsoft.com/pricing/details/bandwidth/)、または [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/) の価格に関する詳細を確認してください。

## <a name="next-steps"></a>次のステップ

- [Azure PowerShell を使用して IPv6 デュアル スタック アプリケーションをデプロイする](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)方法を学習します。
- [Azure CLI を使用して IPv6 デュアル スタック アプリケーションをデプロイする](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)方法を学習します。
- [Resource Manager テンプレート (JSON) を使用して IPv6 デュアル スタック アプリケーションをデプロイする](ipv6-configure-standard-load-balancer-template-json.md)方法を学習します

---
title: Azure ExpressRoute Direct について
description: Azure ExpressRoute Direct の主な特徴と、利用できる SKU や技術的要件など、ExpressRoute Direct にオンボードするために必要な情報について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: duau
ms.openlocfilehash: 56d6a76991c4386be45b2c18f4edb3d363e58fa5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027144"
---
# <a name="about-expressroute-direct"></a>ExpressRoute Direct について

ExpressRoute Direct を使用すると、世界中に戦略的に分散されたピアリングの場所で Microsoft のグローバル ネットワークに直接接続できます。 ExpressRoute Direct では、大規模なアクティブ/アクティブ接続をサポートするデュアル 100 Gbps または 10 Gbps 接続が提供されます。 任意のサービス プロバイダーの ER Direct を操作できます。

以下に、ExpressRoute Direct で提供される主な機能の一部を示します。

* Storage や Cosmos DB などのサービスへの大規模なデータ インジェスト
* 規制対象となっていて、専用の分離された接続が必要な、銀行、政府機関、小売などの業種に向けた物理的な分離性
* 事業単位に基づいた回線配分の詳細な制御

## <a name="onboard-to-expressroute-direct"></a>ExpressRoute Direct へのオンボード

ExpressRoute Direct を利用する前に、まず、サブスクリプションを登録する必要があります。 登録するには、Azure PowerShell を使用して次のコマンドを実行します。

1.  Azure にサインインして、登録するサブスクリプションを選択します。

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. 次のコマンドを使用して、サブスクリプションをパブリック プレビューに登録します。
1. 
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

登録したら、**Microsoft.Network** リソース プロバイダーがサブスクリプションに登録されていることを確認します。 リソース プロバイダーの登録によって、サブスクリプションがリソース プロバイダーと連携するように構成されます。

1. 「[Azure リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)」の説明に従って、サブスクリプションの設定にアクセスします。

1. サブスクリプションで、**リソース プロバイダー** に関して、**Microsoft.Network** プロバイダーに **登録** 状態が表示されていることを確認します。 登録されているプロバイダーの一覧に、Microsoft.Network リソース プロバイダーが存在しない場合は、それを追加します。

ExpressRoute Direct の使用開始後、選択したピアリングの場所に利用可能なポートが見つからない場合は、ExpressRouteDirect@microsoft.com にメールを送信してインベントリの追加を依頼してください。

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>サービス プロバイダーを使用する ExpressRoute と ExpressRoute Direct

| **サービス プロバイダーを使用する ExpressRoute** | **ExpressRoute Direct** | 
| --- | --- |
| サービス プロバイダーを使用して、既存のインフラストラクチャへの高速オンボードと接続を有効にします | 100 Gbps/10 Gbps インフラストラクチャとすべてのレイヤーの完全な管理が必要です
| イーサネットや MPLS を含む、何百ものプロバイダーと統合されます | 規制対象の業種と大量のデータ インジェストのための直接/専用容量 |
| 50 Mbps ～ 10 Gbps 回線 SKU | 顧客は 100 Gbps ExpressRoute Direct で以下の回線 SKU の組み合わせを選択できます。 <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> 顧客は 10 Gbps ExpressRoute Direct で以下の回線 SKU の組み合わせを選択できます。<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| シングル テナント用に最適化 | 複数の事業単位と複数の作業環境を持つシングル テナント用に最適化

## <a name="expressroute-direct-circuits"></a>ExpressRoute Direct の回線

Microsoft Azure ExpressRoute を使用すると、接続プロバイダーによって容易になるプライベート接続を経由して、オンプレミスのネットワークを Microsoft Cloud に拡張できます。 ExpressRoute を使用すると、Microsoft Azure、Microsoft 365 などの Microsoft クラウド サービスへの接続を確立できます。

各ピアリングの場所からは Microsoft のグローバル ネットワークにアクセスできます。また、既定で地理的ゾーン内のすべてのリージョンにアクセス可能です。 Premium 回線を使用してすべてのグローバル リージョンにアクセスできます。  

ほとんどのシナリオの機能は、ExpressRoute サービス プロバイダーを使用して操作する回線と同じです。 ExpressRoute Direct を使用して提供される新しい機能をサポートするため、また細分性を高めるために、ExpressRoute Direct 回線に存在する特定の主な機能があります。

## <a name="circuit-skus"></a>回線 SKU

ExpressRoute Direct では、Azure Storage やその他のビッグ データ サービスへの大量のデータ インジェスト シナリオがサポートされます。 また、100 Gbps ExpressRoute Direct の ExpressRoute 回線でも、**40 Gbps** および **100 Gbps の回線 SKU がサポートされるようになりました。 物理ポート ペアは、**100 Gbps または 10 Gbps** のみであり、複数の仮想回線を備えることができます。 回線のサイズ:

| **100 Gbps ExpressRoute Direct** | **10 Gbps ExpressRoute Direct** | 
| --- | --- |
| **サブスクライブされた帯域幅**: 200 Gbps | **サブスクライブされた帯域幅**: 20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>技術的な要件

* Microsoft Enterprise Edge Router (MSEE) インターフェイス:
    * 10 ギガビットまたは 100 ギガビットのデュアル イーサネット ポート (ルーター ペア間のみ)
    * シングル モード LR ファイバー接続
    * IPv4 および IPv6
    * IP MTU 1500 バイト

* スイッチまたはルーターのレイヤー 2 およびレイヤー 3 接続:
    * 1 つの 802.1 Q (Dot1Q) タグまたは 2 つの Tag 802.1 Q (QinQ) タグのカプセル化をサポートする必要があります
    * Ethertype = 0x8100
    * Microsoft によって指定された VLAN ID に基づいて外部 VLAN タグ (STAG) を追加する必要があります - *QinQ のみに該当*
    * ポートおよびデバイスごとに複数の BGP セッション (VLAN) をサポートする必要があります
    * IPv4 と IPv6 の接続。 *IPv6 の場合、追加のサブインターフェイスは作成されません。IPv6 アドレスが既存のサブインターフェイスに追加されます*。 
    * 省略可能:ExpressRoute 回線のすべてのプライベート ピアリングで既定で構成される [BFD (Bidirectional Forwarding Detection)](./expressroute-bfd.md) のサポート

## <a name="vlan-tagging"></a>VLAN タグ付け

ExpressRoute Direct では、QinQ と Dot1Q の両方の VLAN タグ付けがサポートされます。

* **QinQ VLAN タグ付け** では、ExpressRoute 回線ごとにルーティング ドメインを分離することができます。 Azure では回線の作成時に S-Tag が動的に付与され、変更することはできません。 回線 (プライベートおよび Microsoft) の各ピアリングでは、VLAN として一意の C-Tag が使用されます。 C-Tag を、ExpressRoute Direct ポートの回線全体で一意にする必要はありません。

* **Dot1Q VLAN タグ付け** では、ExpressRoute Direct ポート ペアごとに 1 つの VLAN タグ付けが可能です。 ピアリングで使用される C-Tag は、ExpressRoute Direct ポート ペアのすべての回線とピアリング全体で一意である必要があります。

## <a name="workflow"></a>ワークフロー

[![ワークフロー](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct では、Microsoft グローバル ネットワークへのアクティブ/アクティブ冗長接続でも同じエンタープライズ レベルの SLA が提供されます。 ExpressRoute インフラストラクチャは冗長です。Microsoft グローバル ネットワークへの接続は冗長かつ多様であり、顧客要件のとおりにスケーリングされます。 

## <a name="next-steps"></a>次のステップ

[ExpressRoute Direct を構成する](expressroute-howto-erdirect.md)

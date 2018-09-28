---
title: Azure ExpressRoute Direct について | Microsoft Docs
description: このページでは、ExpressRoute Direct (プレビュー) の概要を示します。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: c33bec76fe17336221c873778c2993d75fec81e8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962234"
---
# <a name="about-expressroute-direct-preview"></a>ExpressRoute Direct (プレビュー) について

ExpressRoute Direct では、世界中に戦略的に分散されたピアリングの場所で Microsoft のグローバル ネットワークに直接接続する機能がお客様に提供されます。 ExpressRoute Direct では、大規模なアクティブ/アクティブ接続をサポートするデュアル 100Gbps 接続が提供されます。 

以下に、ExpressRoute Direct で提供される主な機能の一部を示します。

* Storage や Cosmos DB などのサービスへの大規模なデータ インジェスト 
* 規制対象となっていて、専用の分離された接続が必要とされる、銀行、政府機関、小売などの業種に向けた物理的な分離性 
* 事業単位に基づいた回線配分の詳細な制御

> [!IMPORTANT]
> ExpressRoute Direct は現在プレビューの段階です。
>
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。

## <a name="enroll-in-the-preview"></a>プレビューに登録する

ExpressRoute Direct を利用する前に、まず、プレビューにサブスクリプションを登録する必要があります。 登録するには、サブスクリプション ID を明記して、<ExpressRouteDirect@microsoft.com> にメールを送信します。 ExpressRoute Direct は、エンタープライズ レベルの機能です。 以下の追加の詳細を指定してください。

* **ExpressRoute Direct** で実行しようとしているシナリオ
* 場所設定。すべての場所の完全なリストについては、[パートナーとピアリングの場所](expressroute-locations-providers.md)に関するページを参照してください。
* 実装のタイムライン
* サービスに関する質問

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>サービス プロバイダーを使用する ExpressRoute と ExpressRoute Direct

| **サービス プロバイダーを使用する ExpressRoute** | **ExpressRoute Direct** | 
| --- | --- | 
| サービス プロバイダーを利用して、既存のインフラストラクチャへの高速オンボードと接続を有効にします | 100Gbps インフラストラクチャとすべてのレイヤーの完全な管理が必要です
| イーサネットや MPLS を含む、何百ものプロバイダーと統合されます | 規制対象の業種と大量のデータ インジェストのための直接/専用容量 | 
| 50Mbps から 10Gbps の回線 SKU | 1Gbps から 100Gbps の回線 SKU
| シングル テナント用に最適化 | シングル テナント/クラウド サービス プロバイダー/複数のビジネス ユニット用に最適化

## <a name="expressroute-direct-circuits"></a>ExpressRoute Direct の回線

Microsoft Azure ExpressRoute を利用すれば、接続プロバイダーが提供するプライベート接続で、オンプレミスのネットワークを Microsoft Cloud に拡張できます。 ExpressRoute では、Microsoft Azure、Office 365、Dynamics 365 などの Microsoft クラウド サービスへの接続を確立できます。  

各ピアリングの場所からは Microsoft のグローバル ネットワークにアクセスできます。また、既定で地理的ゾーン内のすべてのリージョンにアクセス可能であり、Premium 回線を使用してすべてのグローバル リージョンにアクセスすることができます。  

ほとんどのシナリオの機能は、ExpressRoute サービス プロバイダーを利用して操作する回線と同じです。 ExpressRoute Direct を使用して提供される新しい機能をサポートするため、また細分性を高めるために、ExpressRoute Direct 回線に存在する特定の主な機能があります。

## <a name="circuit-skus"></a>回線 SKU

ExpressRoute Direct では、Azure Storage やその他のビッグ データ サービスへの大量のデータ インジェスト シナリオがサポートされます。 また、ExpressRoute Direct の ExpressRoute 回線でも、**40G** および **100G** の回線 SKU がサポートされるようになりました。 

## <a name="vlan-tagging"></a>VLAN タグ付け

ExpressRoute Direct では、QinQ と Dot1Q の両方の VLAN タグ付けがサポートされます。

* **QinQ VLAN タグ付け**では、ExpressRoute 回線ごとにルーティング ドメインを分離することができます。 Azure では回線の作成時に S-Tag が動的に割り当てられ、変更することはできません。 回線 (プライベートおよび Microsoft) の各ピアリングでは、VLAN として一意の C-Tag が利用されます。 C-Tag を、ExpressRoute Direct ポートの回線全体で一意にする必要はありません。 

* **Dot1Q VLAN タグ付け**では、ExpressRoute Direct ポート ペアごとに 1 つの VLAN タグ付けが可能です。 ピアリングで使用される C-Tag は、ExpressRoute Direct ポート ペアのすべての回線とピアリング全体で一意である必要があります。

## <a name="workflow"></a>ワークフロー

![workflow](./media/expressroute-erdirect-about/workflow1.png)

## <a name="sla"></a>SLA

ExpressRoute Direct では、Microsoft グローバル ネットワークへのアクティブ/アクティブ冗長接続でも同じエンタープライズ レベルの SLA が提供されます。 ExpressRoute インフラストラクチャは冗長です。Microsoft グローバル ネットワークへの接続は冗長かつ多様であり、顧客要件に応じてスケーリングされます。 プレビュー期間中は、SLA はなく、非運用環境のワークロードの場合にのみ考慮する必要があります。

## <a name="next-steps"></a>次の手順

[ExpressRoute Direct を構成する](expressroute-howto-erdirect.md)
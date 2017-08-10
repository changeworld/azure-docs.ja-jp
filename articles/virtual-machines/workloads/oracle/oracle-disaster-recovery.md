---
title: "Azure 環境における Oracle ディザスター リカバリー シナリオの概要 | Microsoft Docs"
description: "Azure 環境内の Oracle Database 12c のディザスター リカバリー シナリオについて説明します。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/2/2017
ms.author: rclaus
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 4f6fc6ed79606f0510bc3d6258205d1e6105de25
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="disaster-recovery-dr-of-oracle-12c-database-on-an-azure-environment"></a>Azure 環境上の Oracle 12c データベースのディザスター リカバリー (DR)

## <a name="assumptions"></a>前提条件

- Oracle Data Guard の設計と Azure 環境について理解していること


## <a name="goals"></a>目標
- DR の要件を満たすトポロジと構成を設計すること。

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>シナリオ 1 (Azure 上のプライマリー サイトと DR サイト)

Oracle データベースがプライマリー サイトにセットアップされています。 DR サイトは別のリージョンにあります。 Oracle Data Guard はこれらのサイト間の迅速な回復に使用されます。 また、プライマリー サイトにはレポートの作成や他の用途のためにセカンダリ データベースが用意されています。 

### <a name="topology"></a>トポロジ

Azure セットアップの概要は次のとおりです。

- 2 つのサイト (プライマリ サイトと DR サイト)
- 2 つの Virtual Network (VNet)
- Data Guard を備えた 2 つの Oracle データベース (プライマリとスタンバイ)
- Golden Gate または Data Guard を備えた 2 つの Oracle データベース (プライマリ サイトのみ)
- プライマリ サイトに 2 つ、DR サイトに 1 つのアプリケーション サービス
- プライマリ サイト上のデータベースおよびアプリケーション サービスに '可用性セット' が使用されている
- 各サイトに 1 つのジャンプボックス (プライベート ネットワークへのアクセスを制限し、管理者によるサインインのみを許可)
- ジャンプボックス、アプリケーション サービス、データベース、および VPN ゲートウェイは別個のサブネット上にある
- アプリケーションおよびデータベースのサブネットに NSG が適用されている

![DR トポロジ ページのスクリーンショット](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>シナリオ 2 (オンプレミスのプライマリ サイトと Azure 上の DR サイト)

Oracle データベースがオンプレミスにセットアップされています (プライマリ サイト)。 DR サイトは Azure にあります。 Oracle Data Guard はこれらのサイト間の迅速な回復に使用されます。 また、プライマリー サイトにはレポートの作成や他の用途のためにセカンダリ データベースが用意されています。 

このセットアップには 2 つのアプローチがあります。

### <a name="1-direct-connections-between-on-premises-and-azure-required-open-tcp-ports-on-firewall-this-approach-is-not-recommended-as-it-expose-the-tcp-ports-to-outside-world"></a>1.オンプレミスと Azure を直接接続する。ファイアウォールの TCP ポートを開くために必要です。 TCP ポートが外部に公開されるため、このアプローチは推奨されません。

### <a name="topology"></a>トポロジ

Azure セットアップの概要は次のとおりです。

- 1 つのサイト (DR サイト)
- 1 つの Virtual Network (VNet)
- Data Guard を備えた 1 つの Oracle データベースと (アクティブ)
- DR サイトに 1 つのアプリケーション サービス
- 1 つのジャンプボックス (プライベート ネットワークへのアクセスを制限し、管理者によるサインインのみを許可)
- ジャンプボックス、アプリケーション サービス、データベース、および VPN ゲートウェイは別個のサブネット上にある
- アプリケーションおよびデータベースのサブネットに NSG が適用されている
- NSG ポリシー/ルールを追加して受信 TCP ポート 1521 (またはユーザーが定義) を許可する
- NSG ポリシー/ルールは、オンプレミス (DB またはアプリケーション) の IP アドレスから VNet へのアクセスのみを制限するときに追加します。

![DR トポロジ ページのスクリーンショット](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="2-a-better-approach-is-using-site-to-site-vpn-more-information-about-setting-up-vpn-is-available-from-this-linkhttpsdocsmicrosoftcomen-usazurevpn-gatewayvpn-gateway-howto-site-to-site-resource-manager-cli"></a>2.より優れたアプローチは、サイト間 VPN を使用します。 VPN をセットアップする方法について詳しくは、こちらの[リンク](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)をご覧ください。

### <a name="topology"></a>トポロジ

Azure セットアップの概要は次のとおりです。

- 1 つのサイト (DR サイト)
- 1 つの Virtual Network (VNet)
- Data Guard を備えた 1 つの Oracle データベースと (アクティブ)
- DR サイトに 1 つのアプリケーション サービス
- 1 つのジャンプボックス (プライベート ネットワークへのアクセスを制限し、管理者によるサインインのみを許可)
- ジャンプボックス、アプリケーション サービス、データベース、および VPN ゲートウェイは別個のサブネット上にある
- アプリケーションおよびデータベースのサブネットに NSG が適用されている
- オンプレミスと Azure 間のサイト間 VPN 接続

![DR トポロジ ページのスクリーンショット](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-readings"></a>こちらもご覧ください。

- [Azure での Oracle データベースの設計と実装](oracle-design.md)
- [Oracle Data Guard の構成](configure-oracle-dataguard.md)
- [Oracle Golden Gate の構成](configure-oracle-golden-gate.md)
- [Oracle のバックアップと回復](oracle-backup-recovery.md)


## <a name="next-steps"></a>次のステップ

[チュートリアル: 高可用性 VM の作成](../../linux/create-cli-complete.md)

[VM デプロイ Azure CLI サンプルを探索する](../../linux/cli-samples.md)


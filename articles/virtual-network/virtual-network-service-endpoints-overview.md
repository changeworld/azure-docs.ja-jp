---
title: "Azure 仮想ネットワークのサービス エンドポイント | Microsoft Docs"
description: "サービス エンドポイントを使って仮想ネットワークから Azure リソースへの直接アクセスを有効にする方法を説明します。"
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: anithaa
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 297c51cca31d981ffadd20cccce1cd3dd69dc81c
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="virtual-network-service-endpoints-preview"></a>仮想ネットワークのサービス エンドポイント (プレビュー)

仮想ネットワーク (VNet) のサービス エンドポイントは、直接接続によって仮想ネットワークのプライベート アドレス空間を拡張し、VNet の ID を Azure サービスに提供します。 エンドポイントを使用するとこで、重要な Azure サービス リソースへのアクセスを仮想ネットワークのみに限定することができます。 VNet から Azure サービスへのトラフィックは常に、Microsoft Azure のバックボーン ネットワーク上に残ります。

この機能は__プレビュー__段階で、次の Azure サービスとリージョンで提供されています。

__Azure Storage__: WestCentralUS、WestUS2、EastUS、WestUS、AustraliaEast、AustraliaSouthEast。

__Azure SQL Database__: WestCentralUS、WestUS2、EastUS。

このプレビュー機能に関する最新情報については、[Azure 仮想ネットワークの更新情報](https://azure.microsoft.com/updates/?product=virtual-network)ページをご覧ください。

>[!NOTE]
プレビュー期間は、一般公開リリースの機能と同じレベルの可用性と信頼性がない場合があります。 詳細については、[Microsoft Azure プレビューのMicrosoft Azure 追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="key-benefits"></a>主な利点

サービス エンドポイントには次の利点があります。

- __Azure サービス リソースのセキュリティ向上__

  サービス エンドポイントを使用すると、Azure サービス リソースへのアクセスを仮想ネットワークに限定することができます。 これらのリソースへのパブリック インターネット アクセスを完全に排除し、仮想ネットワークからのトラフィックのみを許可することにより、セキュリティが向上します。

- __仮想ネットワークからの Azure サービス トラフィックのルーティングを最適化__

  今日では、VNet 内のルートでオンプレミスまたは仮想アプライアンス経由のインターネット トラフィックが強制されている場合 (強制トンネリング)、Azure サービス トラフィックにもインターネット トラフィックと同じルートが強制されます。 サービス エンドポイントを使用することで、Azure トラフィックのルーティングを最適化することができます。 

  エンドポイントは常に、VNet から Microsoft Azure のバックボーン ネットワーク上のサービスへのサービス トラフィックを直接受け取ります。 これにより、サービス トラフィックに影響を与えることなく、強制トンネリングを通じて、VNet からの送信インターネット トラフィックの監査と監視を続けることができます。 詳細については、[ユーザー定義のルートと強制トンネリング](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)に関するページをご覧ください。

- __管理の手間がかからないシンプルな設定__

  VNet で予約済みのパブリック IP アドレスを使用することなく、IP ファイアウォールを通じて Azure リソースにアクセスできるようになりました。 サービス エンドポイントの設定に NAT やゲートウェイ用のデバイスは必要ありません。 サービス エンドポイントは、サブネット上での単純なクリック操作で構成できます。 エンドポイントを維持するために余分なコストはかかりません。

## <a name="limitations"></a>制限事項

- この機能は、Azure Resource Manager デプロイメント モデルを使ってデプロイされた VNet でのみ使用できます。
- エンドポイントは、Azure VNet に構成されたサブネット上で有効にします。 エンドポイントは、オンプレミスから Azure サービスへのトラフィックに対しては使用できません。 詳細については、「[オンプレミスから Azure サービスへのアクセスの保護](#securing-azure-services-to-virtual-networks)」をご覧ください。
- サービス エンドポイントは、VNet のリージョン内の Azure サービス トラフィックにのみ適用されます。 Azure Storage で RA-GRS および GRS のトラフィックに対応するため、VNet のデプロイ先のリージョンとペアになっているリージョンもこれに含まれます。 [Azure のペアになっているリージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)の詳細をご覧ください。

## <a name="securing-azure-services-to-virtual-networks"></a>Azure サービスへのアクセスを仮想ネットワークに限定する

- VNet のサービス エンドポイントは、VNet の ID を Azure サービスに提供します。 VNet でサービス エンドポイントが有効になったら、Azure サービス リソースに仮想ネットワーク ルールを追加することで、このリソースへのアクセスを VNet に限定することができます。
- 現時点では、VNet からの Azure サービス トラフィックは、パブリック IP アドレスを接続元 IP アドレスとして使用します。 サービス エンドポイントを使用すると、サービス トラフィックは、VNet から Azure サービスにアクセスするときに、VNet のプライベート アドレスを接続元 IP アドレスとして使用するよう切り替えます。 この切り替えにより、IP ファイアウォールで使用される予約済みのパブリック IP アドレスを使用することなく、サービスにアクセスすることができます。
- __オンプレミスから Azure サービスへのアクセスの保護__:
 
   既定では、仮想ネットワークからのアクセスに限定された Azure サービス リソースは、オンプレミスのネットワークからはアクセスできません。 オンプレミスからのトラフィックを許可する場合は、オンプレミスまたは ExpressRoute 回線からの NAT IP アドレスを許可する必要があります。 NAT IP アドレスは、Azure サービス リソースの IP ファイアウォールの構成を通じて追加できます。
  
   __ExpressRoute__: 

    オンプレミスから [ExpressRoute](/azure/expressroute/expressroute-introduction) (ER) を使用している場合、各 Expressroute 回線は 2 つの NAT IP アドレスを使用します。これは、トラフィックが Microsoft Azure のネットワーク バックボーンに入ったときに Azure サービス トラフィックに適用されます。  サービス リソースへのアクセスを許可するには、リソースの IP ファイアウォール設定でこの 2 つの IP アドレスを許可する必要があります。  ExpressRoute 回線の IP アドレスを確認するには、Azure Portal から [ExpressRoute のサポート チケットを開いて](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)ください。

![Azure サービスへのアクセスを仮想ネットワークに限定する](media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>構成

- サービス エンドポイントは VNet 内のサブネット上で構成されます。 エンドポイントは、そのサブネット内で実行されているどの種類のコンピューティング インスタンスでも動作します。
- 1 つのサブネットの特定のサービスに対して有効にできるサービス エンドポイントは 1 つだけです。 サブネット上のサポートされているすべての Azure サービス (Storage、Sql など) に対して複数のサービス エンドポイントを構成できます。
- VNet は、Azure サービス リソースと同じリージョンにある必要があります。 Azure Storage で GRS および RA-GRS のアカウントを使用している場合、プライマリ アカウントは VNet と同じリージョンにある必要があります。
- エンドポイントが構成されている VNet のサブスクリプションは、Azure サービス リソースのサブスクリプションと同じでも違っていてもかまいません。 エンドポイントを設定して Azure サービスのセキュリティを保護するために必要なアクセス許可の詳細については、以下の「[プロビジョニング](#Provisioning)」セクションをご覧ください。
- サポートされているサービスについては、サービス エンドポイントを使って新規または既存のリソースへのアクセスを VNet に限定することができます。

### <a name="considerations"></a>考慮事項

- サービス エンドポイントを有効にすると、サブネット内の仮想マシンの接続元 IP アドレスは、そのサブネットがサービスと通信するときに、パブリック IPv4 アドレスからプライベート IPv4 アドレスを使用するように切り替わります。
  - この切り替え中に、サービスに接続中の既存の TCP 接続はすべて閉じられます。 サービスに接続するサブネットのサービス エンドポイントを有効または無効にするときは、重要なタスクが実行されていないことを確認してください。 また、この IP アドレスの切り替えの後に、アプリケーションが自動的に Azure サービスに接続できること確認してください。
  - IP アドレスの切り替えが影響するのは、VNet からのサービス トラフィックのみです。 VM に割り当てられたパブリック IPV4 アドレスとの間でアドレス指定されるその他のトラフィックには影響しません。
  - Azure サービスについては、Azure のパブリック IP アドレスを使用する既存のファイアウォール ルールがある場合、これらのルールは VNet のプライベート アドレスへの切り替え時に停止します。
- サービス エンドポイントを使用しても、Azure サービスの DNS エントリは変わらず、引き続き Azure サービスに割り当てられている IP アドレスに解決されます。
- ネットワーク セキュリティ グループ (NSG) でのサービス エンドポイントの使用:
  - 既定では、NSG は送信インターネット トラフィックを許可するので、VNet から Azure サービスへのトラフィックも許可します。 これは、サービス エンドポイントを使用しても変わりません。
  - すべての送信インターネット トラフィックを拒否して特定の Azure サービスへのトラフィックだけを許可する場合は、NSG の __“Azure サービス タグ”__ を使用します。 NSG のルールで、サポートされている Azure サービスを接続先として指定することができ、各タグの基になる IP アドレスのメンテナンスは Azure によって提供されます。 詳細については、[NSG の Azure サービス タグ](https://aka.ms/servicetags)に関するページをご覧ください。

### <a name="scenarios"></a>シナリオ
- ピアリングされた VNet、接続された VNet、または複数の VNet:

Azure サービスへのアクセスを 1 つの VNet 内または複数の VNet にまたがる複数のサブネットに限定するには、サブネットごとに個別にサービス エンドポイントを有効にして、Azure サービス リソースへのアクセスをこれらのサブネットに限定します。

- VNet から Azure サービスへの送信トラフィックのフィルタリング:

仮想ネットワークから Azure サービスへのトラフィックを検査またはフィルター処理する場合は、その VNet 内にネットワーク仮想アプライアンス (NVA) をデプロイします。 その後、NVA がデプロイされているサブネットにサービス エンドポイントを適用し、このサブネットのみに Azure サービス リソースへのアクセスを限定します。 このシナリオは、NVA フィルタリングを使用して、VNet から Azure サービスへのアクセスを特定の Azure リソースにのみ制限する場合に役立ちます。 詳細については、[NVA でのエグレス](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/nva-ha#egress-with-layer-7-nvas)に関する記事をご覧ください。

- Azure リソースへのアクセスを VNet に直接デプロイされたサービスに限定する:

さまざまな Azure サービスを、VNet 内の特定のサブネットに直接デプロイできます。 管理されたサービスのサブネット上にサービス エンドポイントを設定することで、Azure サービス リソースへのアクセスを[管理されたサービス](virtual-network-for-azure-services.md)のサブネットに限定することができます。

### <a name="logging-and-troubleshooting"></a>ロギングおよびトラブルシューティング

特定のサービスに対してサービス エンドポイントを構成したら、そのサービス エンドポイントのルートが有効であることを次の方法で検証します。 

- __Azure サービス診断の監視__: サービス診断でサービス要求の "接続元 IP アドレス" を検証することにより、プライベート ネットワーク (つまり VNet) からアクセス要求が送られていることを確認することができます。 サービス エンドポイントを使った新しい要求では、VNet から要求を行うクライアントに割り当てられている、その要求の "接続元 IP アドレス" が VNet のプライベート アドレスとして表示されます。 エンドポイントを使用しない場合、このアドレスは Azure のパブリック IP になります。

- サブネット内の NIC 上の__有効なルート__は、そのサービスのプレフィックス範囲をアドレス指定するより具体的な “既定の” ルートを表示します。 このルートは "VirtualNetworkServiceEndpoint" の nextHopType を持ち、 強制トンネリングのルートと比べて、サービスへのより直接的な接続が有効であることを示します。

>[!NOTE]
サービス エンドポイントのルートは、Azure サービスとアドレス プレフィックスが一致する BGP または UDR のルートをオーバーライドします。 [有効なルートを使用したトラブルシューティング](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#using-effective-routes-to-troubleshoot-vm-traffic-flow)の詳細をご覧ください。

## <a name="provisioning"></a>プロビジョニング

サービス エンドポイントは、仮想ネットワークへの書き込みアクセス権を持つユーザーが仮想ネットワーク上で個別に構成できます。

Azure サービス リソースへのアクセスを VNet に限定するには、ユーザーが、追加されるサブネットの "Microsoft.Network/JoinServicetoaSubnet" へのアクセス許可を持っている必要があります。 このアクセス許可は、既定では組み込みのサービス管理者のロールに含まれ、カスタム ロールを作成することで変更できます。

[組み込みロール](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)と、特定のアクセス許可を[カスタム ロール](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-custom-roles)に割り当てる方法の詳細をご覧ください。

Vnet と Azure サービス リソースのサブスクリプションは、同じでも異なっていてもかまいません。 VNet と Azure サービス リソースのサブスクリプションが異なる場合、このプレビュー期間中、リソースは同じ Active Directory (AD) テナントの下に置かれている必要があります。 

## <a name="pricing-and-limits"></a>料金と制限

サービス エンドポイントの使用に追加料金はかかりません。 現時点では、Azure サービス (Azure Storage、Azure SQL Database) の現在の価格設定モデルが適用されます。

VNet 内のサービス エンドポイントの合計数に制限はありません。

Azure サービス リソース (ストレージ アカウントなど) については、リソースへのアクセスに使用されるサブネットの数がサービスによって制限される場合があります。 詳細については、「[次のステップ](#next%20steps)」の各種サービスに関するドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

- [VNet のサービス エンドポイントを構成する方法](virtual-network-service-endpoints-configure.md)を学習する
- [Azure Storage アカウントを仮想ネットワークに結び付ける方法](https://docs.microsoft.com/azure/storage/common/storage-network-security)の詳細を学習する
- [Azure SQL Database を仮想ネットワークに結び付ける方法](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)の詳細を学習する
- [仮想ネットワーク向けの Azure サービス統合の概要](virtual-network-for-azure-services.md)



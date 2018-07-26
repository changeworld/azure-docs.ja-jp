---
title: 'Azure SQL Database Managed Instance: アプリケーションの接続 | Microsoft Docs'
description: この記事では、Azure SQL Database Managed Instance にアプリケーションを接続する方法について説明します。
ms.service: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.custom: managed instance
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: c9d656908d265aeb6143e857b0ea4f635203bdd9
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258730"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance にアプリケーションを接続する

現在、アプリケーションをホストする方法や場所については、複数の選択肢があります。 
 
Azure App Service か、Azure の仮想ネットワーク (VNet) 統合オプション (Azure App Service Environment、仮想マシン、仮想マシン スケール セットなど) を使用して、アプリケーションをクラウドでホストすることもできますし、 ハイブリッド クラウドのアプローチを使用して、アプリケーションをオンプレミスに維持することもできます。 
 
どの方法を選択した場合でも、アプリケーションをマネージド インスタンス (プレビュー) に接続することは可能です。  

![高可用性](./media/sql-database-managed-instance/application-deployment-topologies.png)  

## <a name="connect-an-application-inside-the-same-vnet"></a>同じ VNet 内のアプリケーションを接続する 

このシナリオは、最もシンプルです。 同じ VNet 内の仮想マシンは、異なるサブネット内にあっても、直接相互接続できます。 つまり、Azure Application Environment または仮想マシン内でアプリケーションを接続する場合は、接続文字列を適切に設定するだけでよいということです。  
 
接続を確立できない場合は、アプリケーション サブネット上でネットワーク セキュリティ グループが設定されていることを確認してください。 その場合は、SQL ポート 1433 と、リダイレクト用のポート範囲 11000-12000 で、アウトバウンド接続を開く必要があります。 

## <a name="connect-an-application-inside-a-different-vnet"></a>異なる VNet 内のアプリケーションを接続する 

このシナリオは少し複雑です。マネージド インスタンスが、自身の VNet 内でプライベート IP アドレスを持つためです。 接続するには、マネージド インスタンスがデプロイされている VNet にアプリケーションがアクセスできるようにする必要があります。 そのため、まずアプリケーションとマネージド インスタンス VNet 間の接続を作成する必要があります。 このシナリオを達成するために、各 Vnet が同じサブスクリプションに属する必要はありません。 
 
Vnet を接続するには、次の 2 つのオプションがあります。 
- [Azure Virtual Network ピアリング](../virtual-network/virtual-network-peering-overview.md) 
- VNet 間 VPN ゲートウェイ ([Azure Portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)、[PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)、[Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)) 
 
望ましいオプションはピアリングです。ピアリングでは Microsoft バックボーン ネットワークが使用されるため、接続の観点から言えば、ピアリングされた VNet 内の仮想マシンであっても、同じ VNet 内の仮想マシンであっても、待機時間に顕著な違いはありません。 VNet ピアリングは、同じリージョン内のネットワークに制限されます。  
 
> [!IMPORTANT]
> Managed Instance の VNet ピアリング シナリオは、[グローバル仮想ネットワーク ピアリングの制約](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)により、同じリージョン内のネットワークに制限されます。 

## <a name="connect-an-on-premises-application"></a>オンプレミス アプリケーションを接続する 

マネージド インスタンスには、プライベート IP アドレスを介してのみアクセスできます。 オンプレミスからアクセスするには、アプリケーションとマネージド インスタンス VNet の間にサイト間接続を確立する必要があります。 
 
オンプレミスを Azure VNet に接続するには、次の 2 つのオプションがあります。 
- サイト間 VPN 接続 ([Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)、[PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)、[Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)) 
- [ExpressRoute](../expressroute/expressroute-introduction.md) 接続  
 
オンプレミスから Azure への接続を正常に確立したにもかかわらず、マネージド インスタンスへの接続を確立できない場合は、ファイアウォールで、SQL ポート 1433 とリダイレクト用のポート範囲 11000-12000 上に、オープンなアウトバウンド接続があることを確認してください。 

## <a name="connect-an-azure-app-service-hosted-application"></a>Azure App Service でホストされたアプリケーションを接続する 

マネージド インスタンスはプライベート IP アドレスを介してのみアクセスできるため、Azure App Service からアクセスするには、まずアプリケーションとマネージド インスタンス VNet の間に接続を確立する必要があります。 「[アプリを Azure 仮想ネットワークに統合する](../app-service/web-sites-integrate-with-vnet.md)」をご覧ください。  
 
トラブルシューティングについては、[Vnet とアプリケーションのトラブルシューティング](../app-service/web-sites-integrate-with-vnet.md#troubleshooting)に関する記事をご覧ください。 接続を確立できない場合は、[ネットワーク構成の同期](sql-database-managed-instance-sync-network-configuration.md)を試してください。 
 
Azure App Service をマネージド インスタンスに接続する場合の特殊なケースとして、マネージド インスタンス VNet にピアリングされたネットワークに Azure App Service を統合しているケースが考えられます。 その場合は、次の構成をセットアップする必要があります。 

- マネージド インスタンス VNet でゲートウェイを使用しない  
- マネージド インスタンス VNet で、リモート ゲートウェイを使用するオプションを設定する 
- ピアリングされた VNet で、[ゲートウェイ転送を許可する] オプションを設定する 
 
次の図は、このシナリオを説明したものです。

![統合アプリケーション ピアリング](./media/sql-database-managed-instance/integrated-app-peering.png)
 
## <a name="connect-an-application-on-the-developers-box"></a>開発者ボックス上のアプリケーションを接続する 

マネージド インスタンスはプライベート IP アドレスを介してのみアクセスできるため、開発者ボックスからアクセスするには、まず開発者ボックスとマネージド インスタンス VNet の間に接続を確立する必要があります。  
 
ネイティブ Azure 証明書認証に関する記事 ([Azure Portal](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)、[PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)、[Azure CLI](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)) に記載されている方法に従って、VNet へのポイント対サイト接続を構成してください。  

## <a name="next-steps"></a>次の手順

- マネージド インスタンスについては、「[What is a Managed Instance? (マネージド インスタンスとは)](sql-database-managed-instance.md)」をご覧ください。
- 新しいマネージド インスタンスの作成方法を紹介するチュートリアルが必要な場合、「[マネージド インスタンスを作成する](sql-database-managed-instance-create-tutorial-portal.md)」を参照してください。

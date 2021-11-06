---
title: SQL Managed Instance にアプリケーションを接続する
titleSuffix: Azure SQL Managed Instance
description: この記事では、Azure SQL Managed Instance にアプリケーションを接続する方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: connect
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: mathoma, bonova, vanto
ms.date: 08/20/2021
ms.openlocfilehash: d4c86b555502d662e681fcb5904426a7a891883d
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893464"
---
# <a name="connect-your-application-to-azure-sql-managed-instance"></a>Azure SQL Managed Instance にアプリケーションを接続する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

現在、アプリケーションをホストする方法や場所については、複数の選択肢があります。

Azure App Service か、または Azure の仮想ネットワーク統合オプション (Azure App Service Environment、Azure Virtual Machines、仮想マシン スケール セットなど) を使用して、アプリケーションをクラウドでホストすることができます。 ハイブリッド クラウドのアプローチを使用して、アプリケーションをオンプレミスに維持することもできます。

どの方法を選択した場合でも、Azure SQL Managed Instance に接続することが可能です。 

この記事では、さまざまなアプリケーション シナリオで仮想ネットワーク内からアプリケーションを Azure SQL Managed Instance に接続する方法について説明します。 

> [!IMPORTANT]
> また、仮想ネットワークの外部からのマネージド インスタンスへのデータ アクセスを有効にすることもできます。 Power BI、Azure App Service などのマルチテナント Azure サービスや、VPN に接続されていないオンプレミス ネットワークから、マネージド インスタンスのパブリック エンドポイントを使用して、マネージド インスタンスにアクセスすることができます。 マネージド インスタンス上でパブリック エンドポイントを有効にし、マネージド インスタンス サブネットに関連付けられたネットワーク セキュリティ グループ上でパブリック エンドポイントのトラフィックを許可する必要があります。 その他の重要な詳細情報については、「[Azure SQL Managed Instance のパブリック エンドポイントを構成する](./public-endpoint-configure.md)」を参照してください。 

![高可用性](./media/connect-application-instance/application-deployment-topologies.png)


## <a name="connect-inside-the-same-vnet"></a>同じ VNet 内の接続

SQL Managed Instance と同じ仮想ネットワーク内のアプリケーションを接続するのは、最も簡単なシナリオです。 同じ仮想ネットワーク内の仮想マシンは、それらが異なるサブネット内にあっても、直接相互接続できます。 つまり、App Service Environment または仮想マシン内のアプリケーションを接続する場合は、接続文字列を適切に設定するだけでよいということです。  

## <a name="connect-inside-a-different-vnet"></a>別の VNet 内での接続

アプリケーションが SQL Managed Instance とは別の仮想ネットワーク内に存在する場合の接続は、SQL Managed Instance が自分自身の仮想ネットワーク内でプライベート IP アドレスを持つため、もう少し複雑になります。 接続するには、アプリケーションは SQL Managed Instance が配置されている仮想ネットワークにアクセスする必要があります。 そのため、アプリケーションと SQL Managed Instance 仮想ネットワーク間の接続を確立する必要があります。 このシナリオを機能させるために、各仮想ネットワークが同じサブスクリプションに属している必要はありません。

仮想ネットワークを接続するには、次の 2 つのオプションがあります。

- [Azure VNet ピアリング](../../virtual-network/virtual-network-peering-overview.md)
- VNet 間 VPN ゲートウェイ ([Azure Portal](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)、[PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)、[Azure CLI](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

ピアリングでは Microsoft バックボーン ネットワークが使用されるため、接続性の観点から言えば、ピアリングされた仮想ネットワーク内の仮想マシンであっても、同じ仮想ネットワーク内の仮想マシンであっても、待機時間に顕著な違いはありません。そのため、ピアリングが推奨されます。 仮想ネットワーク ピアリングは、同じリージョン内のネットワーク間でサポートされます。 グローバル仮想ネットワーク ピアリングもサポート対象ですが、次の注記で説明する制限事項があります。  

> [!IMPORTANT]
> [2020 年 9 月 22 日に、新しく作成された仮想クラスターに対するグローバル仮想ネットワーク ピアリングのサポートが発表されました](https://azure.microsoft.com/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/)。 つまり、この発表日の後に空のサブネット内に作成された SQL Managed Instance のほか、これらのサブネット内に作成されたそれ以降のすべてのマネージド インスタンスでグローバル仮想ネットワーク ピアリングがサポートされます。 その他のすべての SQL Managed Instance では、[グローバル仮想ネットワーク ピアリングの制約](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)のために、ピアリングのサポートが同じリージョン内のネットワークに制限されます。 詳細については、[Azure Virtual Networks のよく寄せられる質問](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)に関する記事の関連セクションも参照してください。 この発表日の前に作成された仮想クラスターの SQL Managed Instance でグローバル仮想ネットワーク ピアリングを使用できるようにするには、そのインスタンス上で[メンテナンス期間](../database/maintenance-window.md)を構成することを検討してください。それにより、そのインスタンスが、グローバル仮想ネットワーク ピアリングをサポートする新しい仮想クラスターに移動されます。

## <a name="connect-from-on-premises"></a>オンプレミスからの接続 

オンプレミスのアプリケーションを仮想ネットワーク (プライベート IP アドレス) 経由で SQL Managed Instance に接続することもできます。 オンプレミスからアクセスするには、アプリケーションと SQL Managed Instance 仮想ネットワークの間にサイト間接続を確立する必要があります。 仮想ネットワークの外部からマネージド インスタンスにデータ アクセスするには、「[Azure SQL Managed Instance のパブリック エンドポイントを構成する](./public-endpoint-configure.md)」を参照してください。

オンプレミスを Azure 仮想ネットワークに接続する方法には、次の 2 つのオプションがあります。

- サイト間 VPN 接続 ([Azure portal](../../vpn-gateway/tutorial-site-to-site-portal.md)、[PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)、[Azure CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) 接続  

オンプレミスから Azure への接続は正常に確立されているが、SQL Managed Instance への接続が確立できない場合は、ファイアウォールでリダイレクト用に SQL ポート 1433 および範囲 11000-11999 のポート上に送信接続が開かれているかどうかを確認してください。

## <a name="connect-the-developer-box"></a>開発者ボックスの接続

また、開発者ボックスを SQL Managed Instance に接続することもできます。 開発者ボックスから仮想ネットワーク経由でアクセスするには、まず開発者ボックスと SQL Managed Instance 仮想ネットワーク間を接続する必要があります。 これを行うには、ネイティブ Azure 証明書認証を使用した仮想ネットワークへのポイント対サイト接続を構成します。 詳細については、「[オンプレミス コンピューターから Azure SQL Managed Instance に接続するようにポイント対サイト接続を構成する](point-to-site-p2s-configure.md)」を参照してください。

仮想ネットワークの外部からマネージド インスタンスにデータ アクセスするには、「[Azure SQL Managed Instance のパブリック エンドポイントを構成する](./public-endpoint-configure.md)」を参照してください。

## <a name="connect-with-vnet-peering"></a>VNet ピアリングを使用しての接続

お客様によって行われるもう 1 つのシナリオに、SQL Managed Instance をホストしているのとは別の仮想ネットワークおよびサブスクリプションに VPN ゲートウェイがインストールされているという場合があります。 その後 2 つの仮想ネットワークはピアリングされます。 次のアーキテクチャ図では、これの実装例を示します。

![仮想ネットワーク ピアリング](./media/connect-application-instance/vnet-peering.png)

基本のインフラストラクチャを構成したら、一部の設定を変更して、VPN ゲートウェイが SQL Managed Instance をホストする仮想ネットワークの IP アドレスを参照できるようにする必要があります。 これを行うには、 **[Peering settings]\(ピアリング設定\)** の下で次の具体的な変更を実行します。

1. VPN ゲートウェイをホストする仮想ネットワークで **[ピアリング]** に移動し、SQL Managed Instance 用のピアリングされた仮想ネットワーク接続に移動して、 **[ゲートウェイ転送を許可する]** をクリックします。
2. SQL Managed Instance をホストする仮想ネットワークで、 **[ピアリング]** に移動し、VPN ゲートウェイ用のピアリングされた仮想ネットワーク接続に移動して、 **[リモート ゲートウェイを使用する]** をクリックします。

## <a name="connect-azure-app-service"></a>Azure App Service の接続 

Azure App Service によってホストされているアプリケーションを接続することもできます。 Azure App Service から仮想ネットワーク経由でアクセスするには、まずそのアプリケーションと SQL Managed Instance 仮想ネットワーク間を接続する必要があります。 「[アプリを Azure 仮想ネットワークに統合する](../../app-service/overview-vnet-integration.md)」を参照してください。 仮想ネットワークの外部からマネージド インスタンスにデータ アクセスするには、「[Azure SQL Managed Instance のパブリック エンドポイントを構成する](./public-endpoint-configure.md)」を参照してください。 

仮想ネットワーク経由の Azure App Service アクセスのトラブルシューティングについては、[仮想ネットワークとアプリケーションのトラブルシューティング](../../app-service/overview-vnet-integration.md#troubleshooting)に関するセクションを参照してください。 接続を確立できない場合は、[ネットワーク構成の同期](azure-app-sync-network-configuration.md)を試してください。

Azure App Service を SQL Managed Instance に接続する場合の特殊なケースとして、SQL Managed Instance 仮想ネットワークにピアリングされたネットワークに Azure App Service を統合しているケースが考えられます。 その場合は、次の構成をセットアップする必要があります。

- SQL Managed Instance 仮想ネットワークにゲートウェイを設定することはできません  
- SQL Managed Instance 仮想ネットワークには、`Use remote gateways` オプションが設定されている必要があります
- ピアリングされた仮想ネットワークには `Allow gateway transit` オプションが設定されている必要があります

次の図は、このシナリオを説明したものです。

![統合アプリケーション ピアリング](./media/connect-application-instance/integrated-app-peering.png)

>[!NOTE]
>仮想ネットワーク統合機能では、アプリと ExpressRoute ゲートウェイを持つ仮想ネットワークは統合されません。 ExpressRoute ゲートウェイが共存モードで構成されている場合であっても、仮想ネットワーク統合は機能しません。 ExpressRoute 接続を通してリソースにアクセスする必要がある場合は、仮想ネットワークで実行される App Service Environment を使用できます。

## <a name="troubleshooting-connectivity-issues"></a>接続の問題のトラブルシューティング

接続の問題のトラブルシューティングには、次を参照してください。

- 同じ仮想ネットワーク内であるが別のサブネット内の Azure 仮想マシンから SQL Managed Instance に接続できない場合は、アクセスをブロックしている可能性のある VM サブネットにネットワーク セキュリティ グループが設定されているかどうかを確認します。 さらに、SQL ポート 1433 と 11000-11999 の範囲のポート上で送信接続を開きます。これは、Azure の境界内でのリダイレクト経由の接続に必要なためです。
- 仮想ネットワークと関連付けられているルート テーブルで、BGP 伝達が **[有効]** に設定されていることを確認します。
- P2S VPN を使用している場合、Azure portal の構成に **イングレスとエグレス** の数が表示されることを確認します。 ゼロ以外の数であるということは、Azure がトラフィックをオンプレミスからまたはオンプレミスにルーティングしていることを示します。

   ![イングレス/エグレスの数](./media/connect-application-instance/ingress-egress-numbers.png)

- (VPN クライアントを実行している) クライアント コンピューターに、アクセスする必要のあるすべての仮想ネットワークのルート エントリがあることを確認します。 ルートは、`%AppData%\Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt` に格納されています。

   ![route.txt](./media/connect-application-instance/route-txt.png)

   この図のとおり、関連する各仮想ネットワーク用に 2 つのエントリと、ポータルで構成されている VPN エンドポイント用に 3 つ目のエントリがあります。

   ルートを確認するもう 1 つの方法は、次のコマンドの使用です。 出力からは、さまざまなサブネットへのルートが示されています。

   ```cmd
   C:\ >route print -4
   ===========================================================================
   Interface List
   14...54 ee 75 67 6b 39 ......Intel(R) Ethernet Connection (3) I218-LM
   57...........................rndatavnet
   18...94 65 9c 7d e5 ce ......Intel(R) Dual Band Wireless-AC 7265
   1...........................Software Loopback Interface 1
   Adapter===========================================================================

   IPv4 Route Table
   ===========================================================================
   Active Routes:
   Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.83.72.1     10.83.74.112     35
         10.0.0.0    255.255.255.0         On-link       172.26.34.2     43
         10.4.0.0    255.255.255.0         On-link       172.26.34.2     43
   ===========================================================================
   Persistent Routes:
   None
   ```

- 仮想ネットワーク ピアリングを使用している場合、[ゲートウェイ転送を許可し、リモート ゲートウェイを使用する方法](#connect-from-on-premises)に関する設定の手順に必ず従ってください。

- 仮想ネットワーク ピアリングを使用して Azure App Service ホスト アプリケーションに接続していて、SQL Managed Instance 仮想ネットワークにパブリック IP アドレス範囲がある場合は、ホストされるアプリケーションの設定で送信トラフィックをパブリック IP ネットワークにルーティングできるようにしてください。 「[リージョンでの仮想ネットワーク統合](../../app-service/overview-vnet-integration.md#regional-virtual-network-integration)」の手順に従います。

## <a name="required-versions-of-drivers-and-tools"></a>ドライバーとツールの必要なバージョン

SQL Managed Instance に接続しようとする場合、ツールおよびドライバーの最小バージョンとして次をお勧めします。

| ドライバーとツール | Version |
| --- | --- |
|.NET Framework | 4.6.1 (または .NET Core) |
|ODBC ドライバー| v17 |
|PHP ドライバー| 5.2.0 |
|JDBC ドライバー| 6.4.0 |
|Node.js ドライバー| 2.1.1 |
|OLEDB ドライバー| 18.0.2.0 |
|SSMS| 18.0 [以降](/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) 以降 |

## <a name="next-steps"></a>次のステップ

- SQL Managed Instance については、[SQL Managed Instance の概要](sql-managed-instance-paas-overview.md)に関する記事を参照してください。
- 新しいマネージド インスタンスの作成方法を紹介するチュートリアルが必要な場合、[マネージド インスタンスの作成](instance-create-quickstart.md)に関するページを参照してください。
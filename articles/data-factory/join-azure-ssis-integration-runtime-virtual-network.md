---
title: Azure-SSIS Integration Runtime を仮想ネットワークに参加させる
description: Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる方法について説明します。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9766ed3ed870e41c44a2dd84bbaad578ec54509c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087877"
---
# <a name="join-azure-ssis-integration-runtime-to-a-virtual-network"></a>Azure-SSIS Integration Runtime を仮想ネットワークに参加させる

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory (ADF) で SQL Server Integration Services (SSIS) を使用する場合、次のシナリオでは、Azure-SSIS Integration Runtime (IR) を仮想ネットワークに参加させる必要があります。

- セルフホステッド IR をプロキシとして構成および管理することなく、Azure-SSIS IR 上で実行される SSIS パッケージからオンプレミス データ ストアまたはリソースにアクセスする必要がある。

- プライベート エンドポイント、IP ファイアウォール規則、仮想ネットワーク サービス エンドポイントで構成された Azure SQL Database サーバー、または仮想ネットワークに参加する Azure SQL Managed Instance を使用して SSIS カタログ データベース (SSISDB) をホストする必要がある。

- プライベート エンドポイント、IP ファイアウォール規則、仮想ネットワーク サービス エンドポイントで構成された Azure データ ストアまたはリソースに、Azure-SSIS IR で実行されている SSIS パッケージからアクセスする必要がある。

- Azure-SSIS IR 上で実行される SSIS パッケージから、IP ファイアウォール規則で構成されたその他のクラウド データ ストアまたはリソースにアクセスする必要がある。

ADF では、Azure Resource Manager またはクラシックのデプロイ モデルで作成された仮想ネットワークに Azure-SSIS IR を参加させることができます。

> [!IMPORTANT]
> 従来の仮想ネットワークは非推奨になるため、代わりに Azure Resource Manager 仮想ネットワークを使用してください。 既に従来の仮想ネットワークを使っている場合は、できるだけ早期に Azure Resource Manager 仮想ネットワークに切り替えます。

[仮想ネットワークに参加するために Azure-SSIS IR を構成する](tutorial-deploy-ssis-virtual-network.md)方法に関するチュートリアルでは、Azure portal または ADF UI を使用した高速仮想ネットワーク インジェクション方法の最低限の手順を示します。 この記事や、「[仮想ネットワークを構成して Azure-SSIS IR のインジェクションを行う](azure-ssis-integration-runtime-virtual-network-configuration.md)」などの他の記事では、このチュートリアルを拡張し、すべてのオプションの手順について説明します。

- 標準の仮想ネットワーク インジェクション方法を使用する。
- クラシック仮想ネットワークを使用する。
- Azure-SSIS IR に独自の静的パブリック IP アドレスを使用する (BYOIP)。
- 独自のドメイン ネーム システム (DNS) サーバーを使用する。
- ネットワーク セキュリティ グループ (NSG) を使用する。
- ユーザー定義ルート (UDR) を使用する。
- カスタマイズされた Azure-SSIS IR を使用する。
- Azure PowerShell を使用して Azure-SSIS IR をプロビジョニングする。

## <a name="access-to-on-premises-data-stores"></a>オンプレミスのデータ ストアにアクセスする

SSIS パッケージがオンプレミスのデータ ストアにアクセスする場合は、オンプレミスのネットワークに接続されている仮想ネットワークに Azure-SSIS IR を参加させることができます。 または、セルフホステッド IR を Azure-SSIS IR のプロキシとして構成して管理することができます。 詳細については、[セルフホステッド IR を Azure-SSIS IR のプロキシとして構成する](self-hosted-integration-runtime-proxy-ssis.md)方法に関する記事を参照してください。 

Azure-SSIS IR を仮想ネットワークに参加させる場合は、次の重要な点に注意してください。 

- 仮想ネットワークがオンプレミス ネットワークに接続されていない場合は、まず Azure-SSIS IR を参加させる [Azure Resource Manager 仮想ネットワーク](../virtual-network/quick-create-portal.md#create-a-virtual-network)を作成します。 次に、その仮想ネットワークからオンプレミス ネットワークへのサイト間 [VPN ゲートウェイ接続](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)または [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) 接続を構成します。 

- Azure Resource Manager 仮想ネットワークが既に Azure-SSIS IR と同じ場所にあるオンプレミス ネットワークに接続されている場合は、その仮想ネットワークに IR を参加させることができます。 

- 従来の仮想ネットワークが、Azure-SSIS IR とは異なる場所にあるオンプレミス ネットワークに既に接続されている場合は、Azure-SSIS IR を参加させる [Azure Resource Manager 仮想ネットワーク](../virtual-network/quick-create-portal.md#create-a-virtual-network)を作成できます。 次に、[クラシックと Azure Resource Manager の間の仮想ネットワーク](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)接続を構成します。 
 
- Azure Resource Manager 仮想ネットワークが Azure-SSIS IR とは異なる場所にあるオンプレミス ネットワークに既に接続されている場合は、まず Azure-SSIS IR を参加させる [Azure Resource Manager 仮想ネットワーク](../virtual-network/quick-create-portal.md#create-a-virtual-network)を作成できます。 次に、[Azure Resource Management と Azure Resource Manager 間の仮想ネットワーク](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)接続を構成します。 

## <a name="hosting-ssisdb-in-azure-sql-database-server-or-managed-instance"></a>Azure SQL Database サーバーまたはマネージド インスタンスでの SSISDB のホスティング

仮想ネットワーク サービス エンドポイントで構成されている Azure SQL Database で SSISDB をホストする場合は、Azure SSIS IR を、必ず同じ仮想ネットワークとサブネットに参加させるようにします。

仮想ネットワークに参加する Azure SQL Managed Instance で SSISDB をホストする場合は、必ず、Azure-SSIS IR を同じ仮想ネットワークに参加させる必要があります (ただし、サブネットはマネージド インスタンスと異なる)。 マネージド インスタンスとは異なる仮想ネットワークに Azure-SSIS IR を参加させるには、仮想ネットワーク ピアリング (同じリージョンに限定される)、または仮想ネットワークから仮想ネットワークへの接続のいずれかをお勧めします。 詳細については、「[Azure SQL Managed Instance にアプリケーションを接続する](../azure-sql/managed-instance/connect-application-instance.md)」を参照してください。

## <a name="access-to-azure-data-stores"></a>Azure データ ストアへのアクセス

SSIS パッケージで、[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)をサポートする Azure データ ストアまたはリソースにアクセスするときに、Azure-SSIS IR からのそれらのリソースへのアクセスをセキュリティで保護する場合は、仮想ネットワーク サービス エンドポイント用に構成された仮想ネットワーク サブネットに Azure-SSIS IR を参加させてから、同じサブネットからのアクセスを許可するために関連するリソースのファイアウォールに仮想ネットワーク規則を追加できます。

## <a name="access-to-other-cloud-data-stores"></a>他のクラウド データ ストアへのアクセス

SSIS パッケージで、特定の静的パブリック IP アドレスのみを許可する他のクラウド データ ストアおよびリソースにアクセスするときに、Azure-SSIS IR からのそれらのリソースへのアクセスをセキュリティで保護する必要がある場合は、Azure-SSIS IR を仮想ネットワークに参加させながら[パブリック IP アドレス](../virtual-network/virtual-network-public-ip-address.md)を関連付け、それらの IP アドレスからのアクセスを許可するために関連するリソースのファイアウォールに IP ファイアウォール規則を追加することができます。 これを行うには 2 つのオプションがあります。 

- Azure-SSIS IR を作成するとき、独自の静的パブリック IP アドレスを持ち込み、[ADF UI](join-azure-ssis-integration-runtime-virtual-network-ui.md) または [Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md) 経由でそれらをAzure-SSIS IR に関連付けます。 これらのパブリック IP アドレスが使用されるのは、Azure-SSIS IR からの送信インターネット接続のみであり、サブネット内の他のリソースでは使用されません。

- また、Azure-SSIS IR が参加するサブネット内の[仮想ネットワーク ネットワーク アドレス変換 (NAT)](../virtual-network/nat-gateway/nat-overview.md) を構成し、このサブネットからのすべての送信インターネット接続で、指定されたパブリック IP アドレスを使用することができます。

すべての場合で、仮想ネットワークは Azure Resource Manager デプロイ モデルでのみデプロイすることができます。

## <a name="next-steps"></a>次のステップ

- [仮想ネットワークを構成して Azure-SSIS IR のインジェクションを行う](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [高速仮想ネットワーク インジェクション方法](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [標準仮想ネットワーク インジェクション方法](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [ADF UI を使用して仮想ネットワークに Azure SSIS IR を参加させる](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Azure PowerShell を使用して仮想ネットワークに Azure SSIS IR を参加させる](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Azure-SSIS IR の詳細については、次の記事を参照してください。 

- 「[Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)」。 この記事では、Azure-SSIS IR を含め、IR に関する全般的な概念情報が説明されています。 
- [チュートリアル:Azure への SSIS パッケージのデプロイに関するチュートリアル](tutorial-deploy-ssis-packages-azure.md)の手順に従って作成します。 このチュートリアルでは、Azure-SSIS IR の作成手順を示しています。 ここでは、SSISDB をホストするために Azure SQL Database サーバーを使用しています。 
- 「[Azure-SSIS IR を作成する](create-azure-ssis-integration-runtime.md)」。 この記事は、このチュートリアルを拡張しています。 仮想ネットワーク サービス エンドポイント、IP ファイアウォール規則、プライベート エンドポイントで構成された Azure SQL Database サーバー、または仮想ネットワークに参加する Azure SQL Managed Instance を使用して SSISDB をホストする手順について説明しています。 Azure-SSIS IR を仮想ネットワークに参加させる方法について説明します。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR についての情報を取得して理解するための方法を示します。
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を停止、開始、または削除する方法を示しています。 また、ノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。

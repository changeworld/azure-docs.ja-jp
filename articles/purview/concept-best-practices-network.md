---
title: Purview ネットワーク アーキテクチャとベスト プラクティス
description: この記事では、Azure Purview ネットワーク アーキテクチャ オプションの例を提供し、ベスト プラクティスについて説明します。
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: b49709a5fb31e597b6a6f85ac0648018ea35a744
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661597"
---
# <a name="azure-purview-network-architecture-and-best-practices"></a>Azure Purview ネットワーク アーキテクチャとベスト プラクティス

Azure Purview は、サービスとしてのプラットフォーム データ ガバナンス ソリューションです。 

Azure Purview アカウントには、Purview に接続するためにインターネット経由でアクセスできるパブリック エンドポイントがあります。 ただし、すべてのエンドポイントは、きめ細かなアクセス制御を備えた AAD ログインと RBAC を使用してセキュリティ保護されます。
セキュリティ層の追加のために、Azure Purview アカウントのプライベート エンドポイントを作成することもできます。それにより、Azure の仮想ネットワークから Purview アカウントとその管理対象リソースにプライベート IP アドレスを割り当てます。 これにより、ユーザーが API、Purview Studio、またはスキャンとインジェストを操作している場合に、仮想ネットワークと Purview アカウント間のすべてのトラフィックをプライベート リンクに制限します。 現在、Azure Purview ファイアウォールによって、自分の purview アカウントのパブリック エンドポイントへのアクセスを制御できます。 ファイアウォールを使用して、プライベート エンドポイントの使用時に、パブリック エンドポイント経由のすべてのアクセスを許可またはブロックできます。 

ネットワークとセキュリティの要件に基づいて、Azure Purview アカウントを設定して、管理し、次のネットワーク オプションのいずれかに基づいて、Purview の基になるサービスやインジェストにアクセスできます。 

- [Azure パブリック エンドポイント](#option-1---use-public-endpoints)を使用する。 
- [プライベート エンドポイント](#option-2---use-private-endpoints)を使用する。 
- [プライベート エンドポイントを使用し、同じ Purview アカウントでパブリック アクセスを許可する](#option-3---use-both-private-endpoint-and-public-endpoints)。 

環境に最適なオプションを理解するには、まず次のアクションを実行することが推奨されます。 

- Azure Purview でデータ ソースを登録してスキャンする前に、ネットワーク トポロジとセキュリティ要件を確認します。 詳しくは、「[Azure のネットワーク トポロジを定義する](/cloud-adoption-framework/ready/azure-best-practices/define-an-azure-network-topology)」をご覧ください。 

- [PaaS サービスのネットワーク接続モデル](/cloud-adoption-framework/ready/azure-best-practices/connectivity-to-azure-paas-services)を定義します。 

## <a name="intended-audience"></a>対象ユーザー  

- データ アーキテクチャ チーム 
- ネットワーク チーム  
- データ セキュリティ チーム 

## <a name="why-do-you-need-to-consider-defining-a-networking-design-part-of-azure-purview-architecture"></a>Azure Purview アーキテクチャの一部であるネットワーク設計を定義することを検討する必要がある理由 

このベスト プラクティス ガイドを使用して、ネットワーク、接続、およびセキュリティ要件に基づいて、ネットワークまたはクラウド内のさまざまな場所から、Purview にアクセスし、データ ソースをスキャンできるように、Azure Purview のネットワーク環境を定義して準備します。

このガイドでは、Azure Purview の最も一般的なネットワーク アーキテクチャ シナリオをいくつか記載していますが、オプションは、このドキュメントに限定されません。 Purview アカウントのネットワークを計画する場合は、Purview の制限事項を確認してください。 

## <a name="option-1---use-public-endpoints"></a>オプション 1 - パブリック エンドポイントを使用する 

既定で、インターネットからアクセスできるパブリック エンドポイント経由で Azure Purview アカウントを使用できます。 次の要件がある場合、Purview アカウントでパブリック ネットワークを許可します。 

- Azure Purview エンドポイントをスキャンまたはそれに接続するときに、プライベート接続は必要ありません。 
- すべてのデータ ソースは SaaS アプリケーションだけです。 
- すべてのデータ ソースには、インターネット経由でアクセスできるパブリック エンドポイントがあります。 
- ビジネス ユーザーは、インターネット経由で Purview アカウントと Purview Studio にアクセスする必要があります。 

### <a name="integration-runtime-options"></a>Integration Runtime のオプション 

Purview アカウント ファイアウォールが [パブリック アクセスを許可します] に設定されている場合にデータ ソースをスキャンするには、[データ ソースのサポート可能性](manage-data-sources.md)に従って、Azure 統合ランタイムと[セルフホステッド統合ランタイム](./manage-integration-runtimes.md)の両方を使用できます。  

- Azure 統合ランタイムまたはセルフホステッド統合ランタイムを使用して、Azure SQL Database や Azure Blob Storage などの Azure データ ソースをスキャンできます。  

- コストと管理オーバーヘッドを削減するために、可能であれば Azure 統合ランタイムを使用して、Azure データ ソースをスキャンすることが推奨されます。 
  
- 複数の Azure データ ソースをスキャンするには、パブリック ネットワークと Azure 統合ランタイムを使用します。 次の手順に、Azure 統合ランタイムを使用して Azure のデータ ソースをスキャンする場合の通信フローをきわめて大まかに示しています。

  :::image type="content" source="media/concept-best-practices/network-azure-runtime.png" alt-text="Azure Purview、ランタイム、データ ソース間の接続フローを示すスクリーンショット。"lightbox="media/concept-best-practices/network-azure-runtime.png":::

  1. 手動または自動スキャンは、Azure 統合ランタイムを使用して Purview データ マップから開始されます。 
   
  2. Azure 統合ランタイムでは、データ ソースに接続してメタデータが抽出されます。

  3. メタデータは Azure Purview のマネージド ストレージのキューに登録され、Azure Blob Storage に格納されます。 

  4. メタデータは Azure Purview データ マップに送信されます。 

- オンプレミスと VM ベースのデータ ソースのスキャンには、常にセルフホステッド統合ランタイムを使用する必要があります。 Azure 統合ランタイムは、これらのデータ ソースではサポートされていません。 次の手順に、セルフホステッド統合ランタイムを使用してデータ ソースをスキャンする場合の通信フローをきわめて大まかに示しています。

  :::image type="content" source="media/concept-best-practices/network-self-hosted-runtime.png" alt-text="Azure Purview、セルフホステッド ランタイム、データ ソース間の接続フローを示すスクリーンショット。"lightbox="media/concept-best-practices/network-self-hosted-runtime.png":::

  1. 手動または自動スキャンがトリガーされます。 Azure Purview では、Azure Key Vault に接続して、データ ソースにアクセスするための資格情報が取得されます。
   
  2. セルフホステッド統合ランタイムを使用して Purview データ マップからスキャンが開始されます。 
   
  3. 仮想マシンからのセルフホステッド統合ランタイム サービスでは、データ ソースに接続してメタデータが抽出されます。

  4. メタデータは、セルフホステッド統合ランタイム VM メモリで処理されます。 メタデータは、Azure Purview のマネージド ストレージのキューに登録され、Azure Blob Storage に格納されます。 

  5. メタデータは Azure Purview データ マップに送信されます。 

### <a name="authentication-options"></a>認証オプション  

Azure Purview でデータ ソースをスキャンする場合は、Azure Purview が、宛先データ ソースで Azure 統合ランタイムを使用して資産のメタデータを読み取れるように、資格情報を指定する必要があります。 パブリック ネットワークを使用する場合、認証オプションと要件は次の要因によって異なります。 

- データ ソースの種類。 たとえば、データ ソースが Azure SQL Database の場合、各データベースへの db_datareader アクセス権で SQL 認証を使用する必要があります。 これには、Azure SQL Database に db_datareader として追加された Azure Active Directory 内のユーザーまたは Azure Purview マネージド ID またはサービス プリンシパルなどがあります。 データ ソースが Azure Blob Storage の場合は、Azure Storage アカウントで Storage Blob データ閲覧者ロールとして追加された Azure Active Directory 内の Azure Purview マネージド ID またはサービス プリンシパルを使用するか、または単に Storage Account のキーを使用します。  

- 管理オーバーヘッドを減らすために、可能であれば Azure Purview マネージド ID を使用して、Azure データ ソースをスキャンすることが推奨されます。 その他の認証の種類については、[Azure Purview 内でソース認証用の資格情報を設定する](manage-credentials.md)必要があります。 

  - Azure Key Vault 内にシークレットを生成します。 
  - Azure Purview 内にキー コンテナーを登録します。  
  - Azure Purview 内で、Azure Key Vault に保存されているシークレットを使用して新しい資格情報を作成します。 

- スキャンで使用されるランタイムの種類。 現在のところ、セルフホステッド統合ランタイムで Azure Purview マネージド ID を使用することはできません。 

### <a name="additional-considerations"></a>その他の注意点  

- パブリック エンドポイントを使用してデータ ソースをスキャンする場合は、オンプレミスまたは VM ベースのデータ ソースに Azure エンドポイントへの送信接続が必要です。 

- セルフホステッド統合ランタイム VM には、[Azure エンドポイントへの送信接続](manage-integration-runtimes.md#networking-requirements)が必要です。 

- Azure データ ソースではパブリック アクセスを許可する必要がありますが、データ ソースでサービス エンドポイントが有効にされている場合は、_信頼されているサービスの一覧にある Azure サービスが Azure データ ソースにアクセスすることを許可する_ ようにします。 サービス エンドポイントでは、VNet からのトラフィックが、最適なパスを経由して、Azure にルーティングされます。 

## <a name="option-2---use-private-endpoints"></a>オプション 2 - プライベート エンドポイントを使用する 

Azure 仮想ネットワーク内の Azure IaaS および PaaS データ ソースやオンプレミスのデータ ソースをプライベート接続経由でスキャンする必要がある場合や、仮想ネットワーク上のユーザーが [Private Link](../private-link/private-link-overview.md)経由で Azure Purview に安全にアクセスできるようにする必要がある場合は、Azure Purview アカウントに [Azure プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用できます。 

他のサービスとしてのプラットフォーム ソリューションと同様に、Azure Purview では、仮想ネットワークに直接デプロイすることがサポートされていません。そのため、一部のネットワーク機能は、ネットワーク セキュリティ グループ、ルート テーブル、その他のネットワーク依存アプライアンス (Azure Firewall など) のようなオファリングのリソースで利用できません。 代わりに、仮想ネットワークで有効にできるプライベート エンドポイントを使用し、パブリック インターネット アクセスを無効にして Purview に安全に接続できます。 

次のいずれかの要件を満たす必要がある場合は、Azure Purview アカウントにプライベート エンドポイントを使用する必要があります。 

- Purview アカウントとデータ ソースにはエンドツーエンドのネットワークの分離を使用する必要があります。 

- Purview アカウントへの[パブリック アクセスをブロックする](./catalog-private-link-end-to-end.md#firewalls-to-restrict-public-access)必要があります。 

- PaaS データ ソースはプライベート エンドポイントを使用してデプロイされており、パブリック エンドポイント経由のすべてのアクセスをブロックしています。 

- オンプレミスまたは IaaS データ ソースからパブリック エンドポイントに到達することはできません。 

### <a name="design-considerations"></a>設計上の考慮事項  

- Purview アカウントにプライベートでかつ安全に接続するには、アカウントとポータルのプライベート エンドポイントをデプロイする必要があります。 (たとえば、API 経由で Purview に接続する場合や、Azure Purview Studio を起動する場合など)。 

- プライベート エンドポイントを使用して Purview Studio に接続する必要がある場合は、アカウントとポータルの両方のプライベート エンドポイントをデプロイする必要があります。 

- プライベート接続経由でデータ ソースをスキャンするには、Purview 用に少なくとも 1 つのアカウントと 1 つのインジェスト プライベート エンドポイントを構成する必要があります。 Azure Purview マネージド ID 以外の認証方法を使用し、セルフホステッド統合ランタイムを使用して、スキャンを構成する必要があります。 

- スキャンを設定する前に、「[インジェスト プライベート エンドポイントを介してデータ ソースをスキャンするためのマトリックスをサポートする](catalog-private-link.md#support-matrix-for-scanning-data-sources-through-ingestion-private-endpoint)」を確認してください。

- [DNS の要件](catalog-private-link-name-resolution.md#deployment-options)を確認してください。 ネットワークでカスタム DNS サーバーを使用している場合は、クライアントで Purview アカウント エンドポイントの FQDN を、プライベート エンドポイントの IP アドレスに解決できる必要があります。 

### <a name="integration-runtime-options"></a>Integration Runtime のオプション 

- データ ソースが Azure に置かれている場合は、Azure Purview インジェスト プライベート エンドポイントがデプロイされている同じ仮想ネットワーク内にデプロイされている Windows 仮想マシンでセルフホステッド統合ランタイムを設定して使用する必要があります。 Azure 統合ランタイムは、インジェスト プライベート エンドポイントでは機能しません。 

- オンプレミスのデータ ソースをスキャンするには、オンプレミスの Windows マシンまたは Azure 仮想ネットワーク内の VM に、セルフホステッド統合ランタイムをインストールすることもできます。 

- Azure Purview でプライベート エンドポイントを使用する場合は、データ ソースから、Purview プライベート エンドポイントがデプロイされている Azure 仮想ネットワーク上のセルフホステッド統合 VM へのネットワーク接続を許可する必要があります。  

- セルフホステッド統合ランタイムの自動アップグレードを許可することが推奨されます。 自動アップグレードを許可するには、Azure 仮想ネットワークまたは企業ファイアウォールで、必要なアウトバウンド規則を公開している必要があります。 詳細については、[セルフホステッド統合ランタイムのネットワーク要件](manage-integration-runtimes.md#networking-requirements)に関するセクションを参照してください。

### <a name="authentication-options"></a>認証オプション  

- Azure Purview マネージド ID (MSI) を使用して、インジェスト プライベート エンドポイント経由でデータ ソースをスキャンすることはできません。 データ ソースの種類に基づいて、サービス プリンシパル、アカウント キー、または SQL 認証を使用します。

- 資格情報が Azure Key Vault に格納されており、Azure Purview 内に登録されていることを確認してください。

- Azure Key Vault に作成する各シークレットに基づいて、Azure Purview に資格情報を作成する必要があります。 Azure の Key Vault リソースに対して、少なくとも _get_ および _list_ アクセス権を Azure Purview のシークレットに割り当てる必要があります。そうしないと、Azure Purview アカウントで資格情報が機能しません。 

### <a name="current-limitations"></a>現在の制限 

- インジェスト用にプライベート エンドポイントを使用している場合、インジェスト プライベート エンドポイントとセルフホステッド統合ランタイムを使用した、サブスクリプションまたはリソース グループ全体を使用する Azure の複数ソースのスキャンはサポートされていません。代わりに、データ ソースを個別に登録およびスキャンできます。 

- Azure Purview プライベート エンドポイントに関連する制限については、「[既知の制限事項](catalog-private-link-troubleshoot.md#known-limitations)」を参照してください 

- Private Link サービスに関連する制限については、「[Azure Private Link の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits)」を参照してください。 

### <a name="private-endpoint-scenarios"></a>プライベート エンドポイントのシナリオ 

#### <a name="scenario-1-single-vnet--single-region"></a>シナリオ 1: 単一 VNet - 単一リージョン  

このシナリオでは、すべての Azure データ ソース、セルフホステッド統合ランタイム VM、および Purview プライベート エンドポイントが、Azure サブスクリプションの同じ VNet にデプロイされます。   

オンプレミスのデータ ソースが存在する場合、Azure Purview プライベート エンドポイントがデプロイされている Azure 仮想ネットワークへのサイト間 VPN または ExpressRoute 接続によって、接続が提供されます。 

このアーキテクチャは、主に小規模な組織、または開発、テスト、概念実証のシナリオに適しています。 

  :::image type="content" source="media/concept-best-practices/network-pe-single-vnet.png" alt-text="単一 VNet シナリオでのプライベート エンドポイントを使用した Azure Purview を示すスクリーンショット。"lightbox="media/concept-best-practices/network-pe-single-vnet.png":::

#### <a name="scenario-2-single-region--multiple-vnets"></a>シナリオ 2: 単一リージョン - 複数の Vnet 

Azure で 2 つ以上の仮想ネットワークを接続するには、[仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)を使用できます。 ピアリングされた仮想ネットワーク間のネットワーク トラフィックはプライベートであり、Azure のバックボーン ネットワーク上に保持されます。 

多くのお客様は、ハブ アンド スポーク ネットワーク アーキテクチャを使用して、Azure でネットワーク インフラストラクチャを構築しています。そこでは、 

- ネットワーク共有サービス (ネットワーク仮想アプライアンス、ExpressRoute/VPN ゲートウェイ、DNS サーバーなど) がハブ仮想ネットワーク (VNet) にデプロイされます 
- スポーク Vnet では、VNet ピアリング経由でこれらの共有サービスが使用されます。 

ハブ アンド スポーク ネットワーク アーキテクチャでは、組織のデータ ガバナンス チームに、仮想ネットワーク (ハブ) を含む Azure サブスクリプションを提供でき、すべてのデータ サービスを、VNet ピアリングまたはサイト間 VPN 接続経由でハブ仮想ネットワークに接続されているその他の少数のサブスクリプションに配置できます。 このアーキテクチャでは、ハブ サブスクリプションと仮想ネットワーク内に Azure Purview と 1 つ以上のセルフホステッド統合ランタイム VM をデプロイし、同じリージョン内の複数のサブスクリプションの他の VNet からデータ ソースを登録およびスキャンできます。 

セルフホステッド統合ランタイム VM は、インジェスト プライベート エンドポイントと同じ VNet にある必要がありますが、別のサブネットにあってもかまいません。

  :::image type="content" source="media/concept-best-practices/network-pe-multi-vnet.png" alt-text="複数 VNet シナリオでのプライベート エンドポイントを使用した Azure Purview を示すスクリーンショット。"lightbox="media/concept-best-practices/network-pe-multi-vnet.png":::

必要に応じて、スポーク仮想ネットワークに追加のセルフホステッド統合ランタイムをデプロイすることもできます。 その場合は、追加のアカウントとインジェスト プライベート エンドポイントをスポーク仮想ネットワークにデプロイする必要があります。 

#### <a name="scenario-3-multiple-regions--multiple-vnets"></a>シナリオ 3: 複数のリージョン - 複数の Vnet

データ ソースが 1 つ以上の Azure サブスクリプションの複数の Azure リージョンに分散されている場合は、このシナリオを使用できます。

パフォーマンスとコストの最適化のために、データ ソースが配置されている各リージョンに 1 つ以上のセルフホステッド統合ランタイム VM をデプロイすることが強く推奨されます。 その場合は、セルフホステッド統合ランタイム VM が配置されているリージョンと VNet に、追加のアカウントと、Azure Purview アカウントのインジェスト プライベート エンドポイントをデプロイする必要があります。  

他のリージョンから Azure Data Lake Storage (Gen 2) リソースを登録してスキャンする必要がある場合は、データソースが配置されているリージョンにローカルのセルフホステッド統合ランタイム VM が必要です。 

  :::image type="content" source="media/concept-best-practices/network-pe-multi-region.png" alt-text="マルチ VNet とマルチリージョン シナリオでのプライベート エンドポイントを使用した Azure Purview を示すスクリーンショット。"lightbox="media/concept-best-practices/network-pe-multi-region.png":::

## <a name="option-3---use-both-private-endpoint-and-public-endpoints"></a>オプション 3 - プライベート エンドポイントとパブリック エンドポイントの両方を使用する

このオプションは、プライベート エンドポイントを使用したデータ ソースのサブセットがあり、同時に、[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)で構成されている他のデータソースや、インターネット経由でアクセス可能なパブリック エンドポイントがあるデータ ソースをスキャンする必要がある場合に必要です。

次の要件のいずれかを満たす必要がある場合は、Azure Purview アカウントにプライベート エンドポイントを使用し、Purview アカウントで **パブリック ネットワーク アクセス** を _許可_ に設定できます。
  
- 一部のデータ ソースはインジェスト プライベート エンドポイントを使用して、また一部のデータ ソースはパブリック エンドポイントまたはサービス エンドポイントを使用して、スキャンする必要があります。

### <a name="integration-runtime-options"></a>Integration Runtime のオプション 

- プライベート エンドポイントで構成されている Azure データ ソースをスキャンするには、Azure Purview インジェスト プライベート エンドポイントがデプロイされている同じ仮想ネットワーク内にデプロイされた Windows 仮想マシンで、セルフホステッド統合ランタイムを設定して使用する必要があります。 Azure Purview でプライベート エンドポイントを使用する場合は、データ ソースから、Purview プライベート エンドポイントがデプロイされている Azure 仮想ネットワーク上のセルフホステッド統合 VM へのネットワーク接続を許可する必要があります。  

- パブリック エンドポイントを許可するように構成されている Azure データ ソースをスキャンするには、Azure 統合ランタイムを使用できます。 

- オンプレミスのデータ ソースをスキャンするには、オンプレミスの Windows マシンまたは Azure 仮想ネットワーク内の VM に、セルフホステッド統合ランタイムをインストールすることもできます。 

- セルフホステッド統合ランタイムの自動アップグレードを許可することが推奨されます。 自動アップグレードを許可するには、Azure 仮想ネットワークまたは企業ファイアウォールで、必要なアウトバウンド規則を公開している必要があります。 詳細については、[セルフホステッド統合ランタイムのネットワーク要件](manage-integration-runtimes.md#networking-requirements)に関するセクションを参照してください。

### <a name="authentication-options"></a>認証オプション  

- パブリック エンドポイントを許可するように構成されている Azure データ ソースをスキャンするには、データ ソースの種類に基づいて、任意の認証オプションを使用できます。
  
- インジェスト プライベート エンドポイントを使用して、プライベート エンドポイントで構成されている Azure データ ソースをスキャンします。
  - Azure Purview マネージド ID (MSI) を使用することはできません。代わりに、データ ソースの種類に基づいて、サービス プリンシパル、アカウント キー、または SQL 認証を使用します。 
  
  - 資格情報が Azure Key Vault に格納されており、Azure Purview 内に登録されていることを確認してください。

  - Azure Key Vault に作成する各シークレットに基づいて、Azure Purview に資格情報を作成する必要があります。 Azure の Key Vault リソースに対して、少なくとも _get_ および _list_ アクセス権を Azure Purview のシークレットに割り当てる必要があります。そうしないと、Azure Purview アカウントで資格情報が機能しません。 

## <a name="next-steps"></a>次の手順
-  [プライベート エンドポイントを使用して Purview へのアクセスをセキュリティで保護する](./catalog-private-link.md)

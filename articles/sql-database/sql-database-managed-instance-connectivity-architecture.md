---
title: Azure SQL Database Managed Instance の接続アーキテクチャ | Microsoft Docs
description: この記事では、Azure SQL Database Managed Instance の通信についてその概要を紹介すると共に、接続アーキテクチャと Managed Instance にトラフィックを誘導する各種コンポーネントの働きについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: e69f6869911555730fe723b340e224c0d5a1e4bb
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536051"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Azure SQL Database Managed Instance の接続アーキテクチャ

この記事では、Azure SQL Database Managed Instance の通信についてその概要を紹介すると共に、接続アーキテクチャと Managed Instance にトラフィックを誘導する各種コンポーネントの働きについて説明します。  

Azure SQL Database Managed Instance は、Azure VNet 内、また Managed Instances 専用のサブネット内に配置されます。 このデプロイにより、次のシナリオが可能になります。 
- 安全なプライベート IP アドレス。
- オンプレミス ネットワークから Managed Instance への直接接続。
- リンク サーバーまたは別のオンプレミス データ ストアへの Managed Instance の接続。
- Azure リソースへの Managed Instance の接続。

## <a name="communication-overview"></a>通信の概要

次の図は、Managed Instance に接続するエンティティと、Managed Instance が正常に機能するために接触する必要のあるリソースを示しています。

![接続アーキテクチャのエンティティ](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

図の一番下に描かれている通信は、データ ソースとしての Managed Instance に接続するユーザーのアプリケーションおよびツールです。  

Managed Instance は PaaS (サービスとしてのプラットフォーム) プランであるため、このサービスは、Microsoft が利用統計情報ストリームに基づいて自動化されたエージェント (管理、デプロイ、メンテナンス) を使って管理します。 Managed Instance を管理する責任はすべて Microsoft が担うため、ユーザーが RDP 経由で Managed Instance の仮想クラスター マシンにアクセスすることはできません。

エンド ユーザー側またはアプリケーション側から開始される SQL Server 操作の中には、Managed Instance とプラットフォームとの間の対話を必要とするものがあります。 その一例として、Managed Instance データベース (ポータル、PowerShell、Azure CLI を通じて公開されるリソース) の作成が挙げられます。

Managed Instance は、その適切な動作を他の Azure サービス (バックアップは Azure Storage、テレメトリは Azure Service Bus、認証は Azure AD、TDE は Azure Key Vault など) に依存しているため、状況に応じてそれらのサービスへの接続を開始します。

前述の通信はすべて暗号化され、証明書を使って署名されます。 通信相手が信頼済みであることを確認するために、Managed Instance は絶えず証明機関と接触して通信相手の証明書を確認します。 証明書が失効しているか、または Managed Instance が証明書を確認できなかった場合、Managed Instance はデータを保護するために接続を終了します。

## <a name="high-level-connectivity-architecture"></a>接続アーキテクチャの概要

Managed Instance は、全体としてみると、隔離された一連の専用仮想マシンにホストされた各種のサービス コンポーネントです。これらの仮想マシンは、ユーザーの仮想ネットワーク サブネット内で実行されて仮想クラスターを形成します。

1 つの仮想クラスターが複数の Managed Instance のホストになる場合もあります。 サブネットにプロビジョニングされるインスタンスの数をユーザーが変更した場合、必要に応じてクラスターが自動的に拡張または縮小されます。

ユーザーのアプリケーションが Managed Instance に接続し、クエリを実行してデータベースを更新できるのは、そのアプリケーションが、仮想ネットワーク内またはピアリングされた仮想ネットワーク内、あるいは VPN / Express Route で接続されたネットワーク内で、プライベート IP アドレスを持ったエンドポイントを使って実行されている場合だけです。  

![接続アーキテクチャ図](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Microsoftの管理サービスとデプロイ サービスは、この仮想ネットワークの外部で実行されるので、Managed Instance と Microsoft サービスとの間の接続は、パブリック IP アドレスを持ったエンドポイントを経由することになります。 Managed Instance が送信接続を作成するときは、ネットワーク アドレス変換 (NAT) が適用されるので、受信側からは、このパブリック IP を発信元とする接続であるかのように見えます。

管理トラフィックは、ユーザーの仮想ネットワーク内を通過します。 つまり、仮想ネットワーク インフラストラクチャの各種の要素は管理トラフィックに影響を与えます。そのため場合によっては管理トラフィックに悪影響が及び、インスタンスが障害の状態に陥って利用できなくなるおそれがあります。

> [!IMPORTANT]
> ユーザー エクスペリエンスとサービスの可用性を高めるために、Azure Virtual Network インフラストラクチャの中で、Managed Instance の動作に影響を及ぼすおそれのある要素には、Microsoft によってネットワーク インテント ポリシーが適用されます。 これはネットワークの要件をエンド ユーザーに対して透過的に伝えるプラットフォーム機構であり、その主な目的は、ネットワークの構成ミスを防ぎ、Managed Instance の正常な動作を確保することにあります。 Managed Instance を削除すると、ネットワーク インテント ポリシーも削除されます。

## <a name="virtual-cluster-connectivity-architecture"></a>仮想クラスターの接続アーキテクチャ

Managed Instance の接続アーキテクチャについて、さらに踏み込んでみましょう。 次の図は、仮想クラスターの概念上のレイアウトを示しています。

![接続アーキテクチャ図 (仮想クラスター)](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

クライアントは、`<mi_name>.<dns_zone>.database.windows.net` 形式のホスト名を使用して Managed Instance に接続します。 このホスト名は、パブリック DNS ゾーンに登録されていてパブリックに解決できますが、実際にはプライベート IP アドレスに解決されます。 `zone-id` はクラスターの作成時に自動的に生成されます。 新しく作成されたクラスターが 2 番目のマネージド インスタンスをホストしている場合、そのクラスターはプライマリ クラスターとそのゾーン ID を共有します。 詳細については、[自動フェールオーバー グループ](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets)に関するページを参照してください

このプライベート IP アドレスは、Managed Instance ゲートウェイ (GW) にトラフィックを誘導する、Managed Instance の内部ロード バランサー (ILB) が所有します。 同じクラスター内で複数の Managed Instance が実行される可能性もあるため、GW が Managed Instance のホスト名を使ってトラフィックを適切な SQL Engine サービスにリダイレクトするのです。

管理サービスとデプロイ サービスは、外部の負荷分散装置にマッピングされた[管理エンドポイント](#management-endpoint)を使って Managed Instance に接続します。 Managed Instance の管理コンポーネント専用としてあらかじめ定義されている一連のポートで受信したトラフィックだけが、必要なノードにルーティングされます。 ノードの組み込みファイアウォールは、Microsoft 固有の IP 範囲からのトラフィックのみを許可するように構成されます。 管理コンポーネントと管理プレーンとの間でやり取りされるすべての通信は、互いに証明書で認証されます。

## <a name="management-endpoint"></a>管理エンドポイント

Azure SQL Database Managed Instance 仮想クラスターには、Microsoft が Managed Instance の管理に使用する管理エンドポイントが含まれています。 管理エンドポイントは、ネットワーク レベルでは組み込みのファイアウォールにより保護され、アプリケーション レベルでは相互の証明書の検証により保護されます。 [管理エンドポイントの IP アドレスを検索](sql-database-managed-instance-find-management-endpoint-ip-address.md)することができます。

接続が Managed Instance 内から開始される場合 (バックアップ、監査ログ)、管理エンドポイントのパブリック IP アドレスがトラフィックの起点のように見えます。 Managed Instance の IP アドレスのみを許可するようにファイアウォール規則を設定することで、Managed Instance からパブリック サービスへのアクセスを制限することができます。 [Managed Instance の組み込みファイアウォールを検証](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)できる方法の詳細について確認してください。

> [!NOTE]
> これは、Managed Instance と同じリージョンにある Azure サービス用のファイアウォール規則の設定には適用されません。Azure プラットフォームには、同じ場所に配置されたサービス間を行き来するトラフィックの最適化が用意されているからです。

## <a name="network-requirements"></a>ネットワークの要件

Managed Instance は、仮想ネットワーク内にあって次の要件を満たしている専用サブネット (Managed Instance サブネット) にデプロイできます:
- **専用サブネット**:Managed Instance サブネットには、自身に関連付けられている他のクラウド サービスを含めることはできません。また、ゲートウェイ サブネットを指定することはできません。 マネージド インスタンス以外のリソースを含むサブネットでマネージド インスタンスを作成したり、後でサブネット内で他のリソースを追加したりすることはできません。
- **互換性のあるネットワーク セキュリティ グループ (NSG)**:Managed Instance サブネットに関連付けられている NSG には、他の規則の前に、次の表に示す規則 (必須の受信セキュリティ規則と送信セキュリティ規則) を含める必要があります。 NSG を使用して、マネージド インスタンス データ エンドポイントへのアクセスを完全に制御するには、ポート 1433 でトラフィックをフィルター処理します。 
- **互換性のあるユーザー定義ルート テーブル (UDR)**:Managed Instance サブネットには、**次ホップ インターネット 0.0.0.0/0** が必須 UDR として割り当てられているユーザー ルート テーブルが必要です。 また、オンプレミスのプライベート IP 範囲が宛先として含まれるトラフィックを、仮想ネットワーク ゲートウェイまたは仮想ネットワーク アプライアンス (NVA) 経由でルーティングする UDR を追加することもできます。 
- **省略可能なカスタム DNS**:カスタム DNS が仮想ネットワークで指定されている場合、Azure の再帰的なリゾルバーの IP アドレス (168.63.129.16 など) をリストに追加する必要があります。 詳細については、[カスタム DNS の構成](sql-database-managed-instance-custom-dns.md)に関するページを参照してください。 カスタム DNS サーバーは、*microsoft.com*、*windows.net*、*windows.com*、*msocsp.com*、*digicert.com*、*live.com*、*microsoftonline.com*、*microsoftonline-p.com* の各ドメインとそのサブドメインのホスト名を解決できる必要があります。 
- **サービス エンドポイントなし**:Managed Instance サブネットにサービス エンドポイントを関連付けることはできません。 仮想ネットワークの作成時に、サービス エンドポイント オプションが無効になっていることを確認してください。
- **十分な IP アドレス**:Managed Instance サブネットには少なくとも 16 個の IP アドレスが必要です (32 個以上を推奨)。 詳細については、[Managed Instance のサブネットのサイズ決定](sql-database-managed-instance-determine-size-vnet-subnet.md)に関するページを参照してください。 [Managed Instance のネットワーク要件](#network-requirements)を満たすように[既存のネットワーク](sql-database-managed-instance-configure-vnet-subnet.md)を構成してからそこに Managed Instance をデプロイするか、または[新しいネットワークとサブネット](sql-database-managed-instance-create-vnet-subnet.md)を作成することができます。

> [!IMPORTANT]
> デプロイ先のサブネットがこれらのすべての要件と互換性がない場合、新しいマネージド インスタンスをデプロイすることはできません。 マネージド インスタンスが作成されると、ネットワーク構成に対して非準拠の変更が行われないように、サブネットでは "*ネットワーク インテント ポリシー*" が適用されます。 最後のインスタンスがサブネットから削除されると、"*ネットワーク インテント ポリシー*" も削除されます

### <a name="mandatory-inbound-security-rules"></a>必須の受信セキュリティ規則 

| Name       |ポート                        |Protocol|ソース           |宛先|Action|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000、9003、1438、1440、1452|TCP     |任意              |任意        |ALLOW |
|mi_subnet   |任意                         |任意     |MI SUBNET        |任意        |ALLOW |
|health_probe|任意                         |任意     |AzureLoadBalancer|任意        |ALLOW |

### <a name="mandatory-outbound-security-rules"></a>必須の送信セキュリティ規則 

| Name       |ポート          |Protocol|ソース           |宛先|Action|
|------------|--------------|--------|-----------------|-----------|------|
|management  |80、443、12000|TCP     |任意              |任意        |ALLOW |
|mi_subnet   |任意           |任意     |任意              |MI SUBNET  |ALLOW |

  > [!Note]
  > 必須の受信セキュリティ規則では、ポート 9000、9003、1438、1440、1452 で_任意_のソースからのトラフィックを許可しますが、これらのポートは組み込みのファイアウォールによって保護されます。 この[記事](sql-database-managed-instance-find-management-endpoint-ip-address.md)では、管理エンドポイントの IP アドレスを検索し、ファイアウォール規則を確認する方法を示しています。 
  
  > [!Note]
  > Managed Instance でトランザクション レプリケーションを使用していて、マネージ インスタンス内のいずれかのデータベースがパブリッシャーまたはディストリビューターとして使用されている場合は、ポート 445 (TCP 送信) が、Azure ファイル共有にアクセスするためにサブネットのセキュリティ規則で開かれている必要もあります。
  
## <a name="next-steps"></a>次の手順

- 概要については、「 [マネージド インスタンスとは](sql-database-managed-instance.md)」を参照してください。
- Managed Instance をデプロイできる[新しい VNet を構成](sql-database-managed-instance-create-vnet-subnet.md)または [既存の VNet を構成](sql-database-managed-instance-configure-vnet-subnet.md)する方法を確認します。
- Managed Instance をデプロイする[サブネットのサイズを計算](sql-database-managed-instance-determine-size-vnet-subnet.md)します。 
- クイック スタートについては、Managed Instance の作成方法に関するページを参照してください。
  - [Azure Portal](sql-database-managed-instance-get-started.md) から
  - [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) の使用
  - [Azure Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)を使用する場合
  - [Azure Resource Manager テンプレート (Jumpbox と SSMS を含む)](https://portal.azure.com/) を使用する場合

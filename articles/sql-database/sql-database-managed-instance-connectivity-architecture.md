---
title: Azure SQL Database Managed Instance の接続アーキテクチャ | Microsoft Docs
description: この記事では、Azure SQL Database Managed Instance の通信についてその概要を紹介すると共に、接続アーキテクチャと Managed Instance にトラフィックを誘導する各種コンポーネントの働きについて説明します。
keywords: ''
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.subservice: managed instance
ms.custom: ''
ms.date: 08/16/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.topic: conceptual
ms.openlocfilehash: 54917c6548c7f0bfacad6408732c5619e6346683
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "40177503"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Azure SQL Database Managed Instance の接続アーキテクチャ 

この記事では、Azure SQL Database Managed Instance の通信についてその概要を紹介すると共に、接続アーキテクチャと Managed Instance にトラフィックを誘導する各種コンポーネントの働きについて説明します。  

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

クライアントは、<mi_name>.<clusterid>.database.windows.net 形式のホスト名を使用して Managed Instance に接続します。 このホスト名は、パブリック DNS ゾーンに登録されていてパブリックに解決できますが、実際にはプライベート IP アドレスに解決されます。 

このプライベート IP アドレスは、Managed Instance ゲートウェイ (GW) にトラフィックを誘導する、Managed Instance の内部ロード バランサー (ILB) が所有します。 同じクラスター内で複数の Managed Instance が実行される可能性もあるため、GW が Managed Instance のホスト名を使ってトラフィックを適切な SQL Engine サービスにリダイレクトするのです。 

管理サービスとデプロイ サービスは、外部の負荷分散装置にマッピングされたパブリック エンドポイントを使って Managed Instance に接続します。 Managed Instance の管理コンポーネント専用としてあらかじめ定義されている一連のポートで受信したトラフィックだけが、必要なノードにルーティングされます。 管理コンポーネントと管理プレーンとの間でやり取りされるすべての通信は、互いに証明書で認証されます。 

## <a name="next-steps"></a>次の手順 

- 概要については、「 [マネージド インスタンスとは](sql-database-managed-instance.md)」を参照してください。 
- VNet の構成の詳細については、 [Managed Instance VNet の構成](sql-database-managed-instance-vnet-configuration.md)に関するページを参照してください。 
- クイック スタートについては、Managed Instance の作成方法に関するページを参照してください。 
  - [Azure portal](sql-database-managed-instance-create-tutorial-portal.md) を使用する場合 
  - [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) を使用する場合 
  - [Azure Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)を使用する場合 
  - [Azure Resource Manager テンプレート (Jumpbox と SSMS を含む)](https://portal.azure.com/) を使用する場合 


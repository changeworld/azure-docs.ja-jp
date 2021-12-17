---
title: ネットワークの概要 - Azure Database for PostgreSQL - フレキシブル サーバー
description: Azure Database for PostgreSQL のフレキシブル サーバー デプロイ オプションの接続およびネットワークのオプションについて説明します。
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/08/2021
ms.openlocfilehash: 3a0ce42cf32e218f3debaf6f3e84bb8f27a81c82
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129279034"
---
# <a name="networking-overview-for-azure-database-for-postgresql---flexible-server-preview"></a>Azure Database for PostgreSQL - フレキシブル サーバーのネットワークの概要 (プレビュー)

この記事では、Azure Database for PostgreSQL - フレキシブル サーバーの接続とネットワークの概念について説明します。 フレキシブル サーバー デプロイ オプションはプレビュー段階にあります。

Azure Database for PostgreSQL - フレキシブル サーバー インスタンス ("*フレキシブル サーバー*") を作成する際、次のいずれかのネットワーク オプションを選択する必要があります: **プライベート アクセス (VNet 統合)** または **パブリック アクセス (許可された IP アドレス)**。 

> [!NOTE]
> サーバーの作成後にネットワーク オプションを変更することはできません。 

プライベート アクセスまたはパブリック アクセスのどちらのオプションを使用するかに関係なく、次の特性が適用されます。

* 許可された IP アドレスからの接続では、有効な資格情報を使用して PostgreSQL サーバーに対して認証を行う必要があります。
* ネットワーク トラフィックに対して[接続の暗号化](#tls-and-ssl)が適用されます。
* サーバーには完全修飾ドメイン名 (FQDN) があります。 接続文字列の `hostname` プロパティには、IP アドレスではなく FQDN を使用することをお勧めします。
* どちらのオプションでも、データベースまたはテーブルのレベルではなく、サーバー レベルでアクセスが制御されます。 PostgreSQL のロールのプロパティを使用して、データベース、テーブル、その他のオブジェクトのアクセスを制御します。

> [!NOTE]
> Azure Database for PostgreSQL はマネージド データベース サービスであるため、`pg_hba.conf` などの構成ファイルを表示、変更するためのホストまたは OS アクセスはユーザーに提供されません。 ファイルの内容は、ネットワーク設定に基づいて自動的に更新されます。

## <a name="private-access-vnet-integration"></a>プライベート アクセス (VNet 統合)

フレキシブル サーバーは、[Azure 仮想ネットワーク (VNet)](../../virtual-network/virtual-networks-overview.md) にデプロイできます。 Azure の仮想ネットワークでは、非公開の、セキュリティで保護されたネットワーク通信が提供されます。 仮想ネットワーク内のリソースは、このネットワークで割り当てられたプライベート IP アドレスを介して通信できます。

次の機能が必要な場合は、このネットワーク オプションを選択します。

* プライベート IP アドレスを使用して、同じ仮想ネットワーク内の Azure リソースからフレキシブル サーバーに接続する。
* VPN または Azure ExpressRoute を使用して Azure 以外のリソースからフレキシブル サーバーに接続する。
* フレキシブル サーバーが、インターネット経由でアクセスできるパブリック エンドポイントを持たないようにする。

:::image type="content" source="./media/how-to-manage-virtual-network-portal/flexible-pg-vnet-diagram.png" alt-text="いずれかにフレキシブル サーバーが含まれている仮想ネットワーク間のピアリングのしくみを示す図。":::

前の図で:
- フレキシブル サーバーは、VNet-1 仮想ネットワークのサブネット 10.0.1.0/24 に導入されています。
- 同じ仮想ネットワーク内の異なるサブネットにデプロイされたアプリケーションは、フレキシブル サーバーに直接アクセスできます。
- 別の仮想ネットワーク (VNet-2) にデプロイされたアプリケーションは、フレキシブル サーバーに直接アクセスできません。 それらがフレキシブル サーバーにアクセスできるようにするには、[プライベート DNS ゾーンの仮想ネットワーク ピアリング](#private-dns-zone-and-virtual-network-peering)を実行する必要があります。
   
### <a name="virtual-network-concepts"></a>仮想ネットワークの概念

Azure 仮想ネットワークには、用途に合わせて構成されたプライベート IP アドレス空間が含まれます。 ご利用の仮想ネットワークは、フレキシブル サーバーと同じ Azure リージョンに存在する必要があります。 仮想ネットワークの詳細については、[Azure Virtual Network の概要](../../virtual-network/virtual-networks-overview.md)に関するページを参照してください。

PostgreSQL フレキシブル サーバーで仮想ネットワークを使用する場合に理解しておくべきいくつかの概念を以下に示します。

* **委任されたサブネット**。 仮想ネットワークには、サブネット (サブネットワーク) が含まれます。 サブネットを使用すると、仮想ネットワークをより小さなアドレス空間に分割できます。 Azure リソースは、仮想ネットワーク内の特定のサブネットにデプロイされます。 

  フレキシブル サーバーは、"*委任された*" サブネット内に存在する必要があります。 つまり、そのサブネットを使用できるのは Azure Database for PostgreSQL - フレキシブル サーバー インスタンスだけだということです。 委任されたサブネットに他の Azure リソースの種類を含めることはできません。 サブネットを委任するには、その委任プロパティを `Microsoft.DBforPostgreSQL/flexibleServers` として割り当てます。

  > [!IMPORTANT]
  > `AzureFirewallSubnet`、`AzureFirewallManagementSubnet`、`AzureBastionSubnet`、`GatewaySubnet` を含む名前は、Azure 内で予約されています。 これらのいずれも、サブネット名として使用しないでください。

* **ネットワーク セキュリティ グループ (NSG)**。 NSG のセキュリティ規則を使用して、仮想ネットワークのサブネットとネットワーク インターフェイスに出入りできるネットワーク トラフィックの種類をフィルター処理できます。 詳細については、[NSG の概要](../../virtual-network/network-security-groups-overview.md)に関するページを参照してください。

  アプリケーション セキュリティ グループ (ASG) を使用すると、フラットなネットワークで NSG を使用して、レイヤー 4 のセキュリティを簡単に制御できます。 以下を迅速に実行できます。
  
  - ASG に仮想マシンを参加させる、または ASG から仮想マシンを削除する。
  - これらの仮想マシンに対する規則を動的に適用または削除する。 
  
  詳細については、[ASG の概要](../../virtual-network/application-security-groups.md)に関するページを参照してください。 
  
  現時点では、Azure Database for PostgreSQL - フレキシブル サーバーを含む規則の一部に ASG が含まれている NSG はサポートしていません。 現在、NSG では [IP ベースのソースまたは宛先のフィルター処理](../../virtual-network/network-security-groups-overview.md#security-rules)を使用することをお勧めします。 

  > [!IMPORTANT]
  > Azure Database for PostgreSQL - フレキシブル サーバーの高可用性機能には、Azure Database for PostgreSQL - フレキシブル サーバーがデプロイされている Azure 仮想ネットワーク サブネット内の宛先ポート 5432、6432、およびログ アーカイブ用の Azure Storage へのトラフィックの送信および受信機能が必要です。 Azure Database for PostgreSQL - フレキシブル サーバーがデプロイされているサブネット内での送受信のトラフィック フローを拒否する[ネットワーク セキュリティ グループ (NSG)](../../virtual-network/network-security-groups-overview.md) を作成する場合は、サブネット内の宛先ポート 5432 および 6432 へのトラフィックと、[サービス タグ](../../virtual-network/service-tags-overview.md) Azure Storage を宛先として使用して Azure Storage へのトラフィックを必ず許可してください。

* **プライベート DNS ゾーン統合**。 Azure プライベート DNS ゾーン統合により、現在の仮想ネットワーク、またはリージョン内でピアリングされた仮想ネットワーク (プライベート DNS ゾーンがこれにリンクされている) 内でプライベート DNS を解決できます。 

### <a name="using-a-private-dns-zone"></a>プライベート DNS ゾーンを使用する

Azure portal または Azure CLI を使用して仮想ネットワークでフレキシブル サーバーを作成する場合、サブスクリプション内のサーバーごとに新しいプライベート DNS ゾーンが、指定したサーバー名を使用して自動的にプロビジョニングされます。 

Azure API、Azure Resource Manager テンプレート (ARM テンプレート)、または Terraform を使用する場合は、`postgres.database.azure.com` で終わるプライベート DNS ゾーンを作成します。 これらのゾーンは、プライベート アクセスでフレキシブル サーバーを構成するときに使用します。 詳細については、[プライベート DNS ゾーンの概要](../../dns/private-dns-overview.md)に関するページを参照してください。


 Azure 仮想ネットワークにプライベート ネットワーク アクセスを使用している場合は、API、ARM、Terraform などのさまざまなインターフェイスでプライベート DNS ゾーン情報の指定が必須です。  そのため、API、ARM、Terraform でプライベート ネットワーク アクセスを使用して Azure Database for PostgreSQL フレキシブル サーバーを新しく作成する場合は、プライベート DNS ゾーンを作成し、それを使用してプライベート アクセスでフレキシブル サーバーを構成します。 詳細については、[Microsoft Azure の REST API 仕様](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/postgresql/resource-manager/Microsoft.DBforPostgreSQL/preview/2021-06-01-preview/postgresql.json)に関するページを参照してください。 フレキシブル サーバーの作成に [Azure portal](./how-to-manage-virtual-network-portal.md) または [Azure CLI](./how-to-manage-virtual-network-cli.md) を使用する場合は、同じまたは異なるサブスクリプションで以前に作成したプライベート DNS ゾーン名を指定できます。そうしない場合は、サブスクリプションに既定のプライベート DNS ゾーンが自動的に作成されます。



### <a name="integration-with-a-custom-dns-server"></a>カスタム DNS サーバーとの統合

カスタム DNS サーバーを使用する場合は、DNS フォワーダーを使用して Azure Database for PostgreSQL - フレキシブル サーバーの FQDN を解決する必要があります。 フォワーダーの IP アドレスは、[168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md) である必要があります。 

カスタム DNS サーバーは、仮想ネットワーク内にあるか、仮想ネットワークの DNS サーバー設定を使用して到達可能である必要があります。 詳細については、「[独自の DNS サーバーを使用する名前解決](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」を参照してください。

### <a name="private-dns-zone-and-virtual-network-peering"></a>プライベート DNS ゾーンと仮想ネットワーク ピアリング

プライベート DNS ゾーンの設定と、仮想ネットワーク ピアリングはそれぞれ独立しています。 同じリージョンまたは異なるリージョンの別の仮想ネットワークでプロビジョニングされたクライアントからフレキシブル サーバーに接続する必要がある場合は、プライベート DNS ゾーンをその仮想ネットワークにリンクさせる必要があります。 詳細については、「[仮想ネットワークのリンク](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)」を参照してください。

> [!NOTE]
> リンクできるのは、名前が `postgres.database.azure.com` で終わるプライベート DNS ゾーンのみです。

### <a name="unsupported-virtual-network-scenarios"></a>サポートされない仮想ネットワークのシナリオ

仮想ネットワークで作業する場合の制限事項を次に示します。

* 仮想ネットワークにデプロイされたフレキシブル サーバーには、パブリック エンドポイント (またはパブリック IP あるいは DNS) を設定できません。
* フレキシブル サーバーを仮想ネットワークおよびサブネットにデプロイした後、それを別の仮想ネットワークまたはサブネットに移動することはできません。 仮想ネットワークを別のリソース グループまたはサブスクリプションに移動することはできません。
* サブネットにリソースを配置した後、そのサブネットのサイズ (アドレス空間) を増やすことはできません。
* フレキシブル サーバーは、Azure Private Link をサポートしていません。 代わりに、仮想ネットワークの導入により、仮想ネットワーク内でフレキシブル サーバーを使用できるようにしています。 


## <a name="public-access-allowed-ip-addresses"></a>パブリック アクセス (許可された IP アドレス)

パブリック アクセス方法を選択すると、インターネット経由でパブリック エンドポイントを介してフレキシブル サーバーにアクセスできます。 パブリック エンドポイントは、パブリックに解決できる DNS アドレスです。 "*許可された IP アドレス*" という語句は、サーバーへのアクセス許可を付与することにした IP アドレスの範囲を意味します。 これらのアクセス許可は、*ファイアウォール規則* と呼ばれます。 

次の機能が必要な場合は、このネットワーク オプションを選択します。

* 仮想ネットワークをサポートしていない Azure リソースから接続する。
* VPN または ExpressRoute によって接続されていない、Azure 外部のリソースから接続する。
* フレキシブル サーバーが、インターネット経由でアクセスできるパブリック エンドポイントを持つようにする。

パブリック アクセスの方法には、次のような特性があります。

* 許可した IP アドレスにのみ、PostgreSQL フレキシブル サーバーにアクセスするためのアクセス許可が付与されます。 既定では、許可されている IP アドレスはありません。 IP アドレスの追加は、サーバーの作成時または後で行えます。
* PostgreSQL サーバーには、パブリックに解決できる DNS 名があります。
* フレキシブル サーバーは、Azure 仮想ネットワークのいずれにも存在しません。
* サーバーとの間のネットワーク トラフィックは、プライベート ネットワークを経由しません。 このトラフィックでは、一般的なインターネット経路が使用されます。

### <a name="firewall-rules"></a>ファイアウォール規則

ファイアウォール規則で許可していない IP アドレスから接続が試みられた場合、送信元のクライアントにエラーが返されます。

### <a name="allowing-all-azure-ip-addresses"></a>すべての Azure IP アドレスを許可する

ご使用の Azure サービスに対して固定の発信 IP アドレスが使用できない場合は、Azure データセンターのすべての IP アドレスからの接続を有効にすることを検討できます。

> [!IMPORTANT]
> **[Azure サービスおよび Azure 内のリソースからのパブリック アクセスを許可する]** オプションを選択すると、他の顧客のサブスクリプションからの接続を含め、Azure サービスからのすべての接続を許可するようにファイアウォールが構成されます。 このオプションを選択する場合は、許可されているユーザーのみがアクセスできるように、サインインおよびユーザーのアクセス許可が制限されていることを確認してください。

### <a name="troubleshooting-public-access-issues"></a>パブリック アクセスに関する問題のトラブルシューティング
Azure Database for PostgreSQL サービスに期待どおりにアクセスできない場合は、次の点を検討してください。

* **許可リストに対する変更がまだ有効になっていない**。 Azure Database for PostgreSQL サーバーのファイアウォール構成に対する変更が有効になるまで 5 分程度の遅延が発生する場合があります。

* **認証に失敗した**。 ユーザーが Azure Database for PostgreSQL サーバーに対するアクセス許可を持っていないか、パスワードが正しくない場合、Azure Database for PostgreSQL サーバーへの接続は拒否されます。 ファイアウォール設定を作成しても、クライアントはサーバーへの接続を試行できるようになるだけです。 各クライアントは、必要なセキュリティ資格情報を提供する必要があることに変わりはありません。

* **動的なクライアント IP アドレスによりアクセスが妨げられている**。 動的 IP アドレス指定によるインターネット接続を使用しており、ファイアウォールの通過に問題が発生している場合は、次の解決策のいずれかをお試しください。

   * Azure Database for PostgreSQL サーバーにアクセスするクライアント コンピューターに割り当てられている IP アドレス範囲について、インターネット サービス プロバイダー (ISP) に問い合わせます。 その後、ファイアウォール規則としてその IP アドレス範囲を追加します。
   * 代わりに、クライアント コンピューター用に静的 IP アドレスを取得し、ファイアウォール規則としてその静的 IP アドレス範囲を追加してください。

* **ファイアウォール規則が IPv6 形式で使用できない**。 ファイアウォール規則は IPv4 形式である必要があります。 IPv6 形式でファイアウォール規則を指定すると、検証エラーが返されます。

## <a name="host-name"></a>ホスト名

選択するネットワーク オプションに関係なく、フレキシブル サーバーに接続する場合は常に、ホスト名として FQDN を使用することをお勧めします。 サーバーの IP アドレスが静的のままであることは保証されていません。 FQDN を使用すると、接続文字列を変更せずに済みます。 

ホスト名として FQDN を使用する例は、`hostname = servername.postgres.database.azure.com` です。 可能であれば、`hostname = 10.0.0.4` (プライベート アドレス) または `hostname = 40.2.45.67` (パブリック アドレス) の使用は避けてください。


## <a name="tls-and-ssl"></a>TLS と SSL

Azure Database for PostgreSQL - フレキシブル サーバーでは、トランスポート層セキュリティ (TLS) を使用して、PostgreSQL サービスへのクライアント アプリケーションの接続を強制します。 TLS は、データベース サーバーとクライアント アプリケーションの間で、暗号化されたネットワーク接続を保証する業界標準のプロトコルです。 TLS は、Secure Sockets Layer (SSL) が更新されたプロトコルです。 

Azure Database for PostgreSQL では TLS 1.2 以降がサポートされています。 インターネット技術標準化委員会 (IETF) は [RFC 8996](https://datatracker.ietf.org/doc/rfc8996/) で、TLS 1.0 と TLS 1.1 を使用してはならないことを明記しています。 どちらのプロトコルも、2019 年末までに非推奨となりました。

TLS 1.0 や TLS 1.1 など、以前のバージョンの TLS プロトコルを使用する受信接続は既定ですべて拒否されます。 

> [!NOTE]
> SSL と TLS の証明書により、接続が最先端の暗号化プロトコルで保護されていることが証明されます。 通信中の接続を暗号化することで、転送中のデータへの不正アクセスを防ぐことができます。 このため、最新バージョンの TLS を使用して、Azure Database for PostgreSQL - フレキシブル サーバーへの接続を暗号化することを強くお勧めします。 推奨されませんが、必要に応じて **require_secure_transport** サーバー パラメーターをオフに更新することで、Azure Database for PostgreSQL - フレキシブル サーバーへの接続の TLS\SSL を無効にすることもできます。 また、**ssl_min_protocol_version** と **ssl_max_protocol_version** のサーバー パラメーターを設定することで、TLS バージョンを設定することもできます。

## <a name="next-steps"></a>次のステップ

* [Azure portal](how-to-manage-virtual-network-portal.md) または [Azure CLI](how-to-manage-virtual-network-cli.md) で **プライベート アクセス (VNet 統合)** オプションを使用してフレキシブル サーバーを作成する方法を説明します。
* [Azure portal](how-to-manage-firewall-portal.md) または [Azure CLI](how-to-manage-firewall-cli.md) で **パブリック アクセス (許可された IP アドレス)** を使用してフレキシブル サーバーを作成する方法を説明します。
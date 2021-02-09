---
title: よく寄せられる質問
description: Azure VMware Solution についてよく寄せられる質問にお答えします。
ms.topic: conceptual
ms.date: 1/27/2021
ms.openlocfilehash: 3ef3d1b9e6fc76b5f09acf8c300dbea901b4aef2
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072270"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution"></a>Azure VMware Solution についてよく寄せられる質問

この記事では、Azure VMware Solution についてよく寄せられる質問に回答します。

## <a name="general"></a>全般

### <a name="what-is-azure-vmware-solution"></a>Azure VMware Solution とは

企業によるビジネスの機敏性の向上、コスト削減、イノベーションの促進のための IT 近代化戦略の追及を受けて、ハイブリッド クラウド プラットフォームが顧客のデジタル変革の主要な手段として登場しました。 Azure VMware Solution は、VMware の Software Defined Data Center (SDDC) ソフトウェアと Microsoft Azure グローバル クラウド サービス エコシステムを組み合わせたものです。 Azure VMware Solution は、パフォーマンス、可用性、セキュリティ、およびコンプライアンスの要件を満たすために管理されます。

## <a name="azure-vmware-solution-service"></a>Azure VMware Solution サービス

### <a name="where-is-azure-vmware-solution-available-today"></a>現在、Azure VMware Solution はどこで利用できますか?

このサービスは新しいリージョンに継続的に追加されているので、詳細については、[最新のサービスの可用性情報](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware)に関するページを参照してください。 

### <a name="can-workloads-running-in-an-azure-vmware-solution-instance-consume-or-integrate-with-azure-services"></a>Azure VMware Solution インスタンスで実行されるワークロードで Azure サービスを使用できますか? また、このワークロードを Azure サービスと統合できますか?

Azure VMware Solution のお客様はすべての Azure サービスを利用できるようになります。 特定のサービスのパフォーマンスと可用性の制限については、ケースバイケースで対処する必要があります。

### <a name="what-guest-operating-systems-are-compatible-with-azure-vmware-solution"></a>Azure VMware Solution と互換性のあるゲスト オペレーティング システムには、どのようなものがありますか?

ゲスト オペレーティング システムと vSphere との互換性に関する情報については、「[VMware Compatibility Guide](https://www.vmware.com/resources/compatibility/search.php?deviceCategory=software&details=1&releases=485&page=1&display_interval=10&sortColumn=Partner&sortOrder=Asc&testConfig=16)」を参照してください。  Azure VMware Solution で実行されている vSphere のバージョンを特定するには、「[VMware ソフトウェアのバージョン](concepts-private-clouds-clusters.md#vmware-software-versions)」を参照してください。

### <a name="do-i-use-the-same-tools-that-i-use-now-to-manage-private-cloud-resources"></a>プライベート クラウドのリソースを管理するために現在使用しているのと同じツールを使用しますか?

はい。 デプロイといくつかの管理操作のために Azure portal を使用します。 vSphere と NSX-T のリソースを管理するために vCenter と NSX Manager を使用します。

### <a name="can-i-manage-a-private-cloud-with-my-on-premises-vcenter"></a>オンプレミスの vCenter を使用してプライベート クラウドを管理できますか?

初期の Azure VMware Solution は、オンプレミス環境とプライベート クラウド環境全体に対して単一の管理エクスペリエンスをサポートしていません。 プライベート クラウド クラスターは、プライベート クラウドに対してローカルな vCenter および NSX Manager を使用して管理します。

### <a name="can-i-use-vrealize-suite-running-on-premises"></a>オンプレミスで実行される vRealize スイートを使用できますか? 

特定の統合およびユース ケースは、ケースバイケースで評価されます。

### <a name="can-i-migrate-vsphere-vms-from-on-premises-environments-to-azure-vmware-solution-private-clouds"></a>オンプレミス環境から Azure VMware Solution のプライベート クラウドに vSphere VM を移行できますか?

はい。 標準クロス vCenter [vMotion の要件](https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=2106952)が満たされる場合、VM 移行と vMotion を使用して VM をプライベート クラウドに移動できます。

### <a name="is-a-specific-version-of-vsphere-required-in-on-premises-environments"></a>オンプレミス環境では、特定のバージョンの vSphere が必要ですか?

どのクラウド環境にも VMware HCX が付属しているため、vMotion のオンプレミス環境では vSphere 5.5 以降が必要です。

### <a name="what-does-the-change-control-process-look-like"></a>変更制御プロセスはどのようなものですか?

サービス自体に加えられた更新は、Microsoft Azure の標準的な変更管理プロセスに従います。 お客様は、ワークロード管理タスクと関連する変更管理プロセスについて責任を持ちます。

### <a name="how-is-this-different-from-azure-vmware-solution-by-cloudsimple"></a>これは Azure VMware Solution by CloudSimple とはどのように違いますか?

新しい Azure VMware Solution では、Microsoft と VMware の間に直接クラウド プロバイダー パートナーシップがあります。 新しいソリューションは、Microsoft によって完全に設計、構築、サポートされ、VMware によって保証されます。 アーキテクチャ上、ソリューションは専用の Azure インフラストラクチャで実行されている VMware テクノロジ スタックと一貫しています。

### <a name="can-azure-vmware-solution-vms-be-managed-by-vmrc"></a>Azure VMware Solution の VM は VMRC で管理できますか?
はい。 インストールされるシステムがプライベート クラウドの vCenter にアクセスでき、パブリック DNS を使用して ESXi ホスト名を解決している場合は管理できます。

### <a name="are-there-special-instructions-for-installing-and-using-vmrc-with-azure-vmware-solution-vms"></a>Azure VMware Solution の VM で VMRC をインストールして使用するための特別な手順はありますか?
いいえ。 VM の前提条件を満たすには、[VMware が提供する手順](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-89E7E8F0-DB2B-437F-8F70-BA34C505053F.html)に従ってください。 

### <a name="is-vmware-hcx-supported-on-vpns"></a>VMware HCX は VPN でサポートされますか?
いいえ。帯域幅と待機時間の要件があるためサポートされません。

### <a name="can-azure-bastion-be-used-for-connecting-to-azure-vmware-solution-vms"></a>Azure Bastion を使用して AVS VMware Solution VM に接続することはできますか?
Azure Bastion は、Azure VMware Solution がインターネットに公開されないようにジャンプ ボックスに接続する場合に推奨されるサービスです。 Azure VMware Solution の VM は Azure IaaS オブジェクトではないため、Azure Bastion を使用してそれらの VM に接続することはできません。

### <a name="can-azure-load-balancer-internal-be-used-for-azure-vmware-solution-vms"></a>Azure VMware Solution VM で内部 Azure Load Balancer 使用できますか?
いいえ。 内部 Azure Load Balancer では、Azure IaaS VM のみがサポートされます。 Azure Load Balancer は IP ベースのバックエンド プールをサポートしていません。Azure VMware Solution VM が Azure オブジェクトではない Azure VM または仮想マシン スケール セット オブジェクトのみです。

### <a name="can-an-existing-expressroute-gateway-be-used-to-connect-to-azure-vmware-solution"></a>既存の ExpressRoute ゲートウェイを使用して、Azure VMware Solution に接続できますか?
はい。 仮想ネットワークあたり 4 つの ExpressRoute 回路という上限を超えない限り、既存の ExpressRoute ゲートウェイを使用して Azure VMware Solution に接続できます。 オンプレミスから ExpressRoute 経由で Azure VMware Solution にアクセスするには、ExpressRoute Global Reach が必要です。これは、ExpressRoute ゲートウェイでは、それに接続されている回路との間で推移的なルーティングが提供されないためです。

### <a name="why-does-azure-vmware-solution-use-a-public-4-byte-autonomous-system-number-asn"></a>Azure VMware Solution で 4 バイトのパブリック自律システム番号 (ASN) が使用されるのはなぜですか?
Azure VMware Solution では、公式に登録された 4 バイトのパブリック ASN を使用することで、お客様がオンプレミスで Azure VMware Solution への顧客のルーティング パスに使用するプライベート ASN との競合が起きないことを保証しています。
 
### <a name="how-can-i-use-expressroute-to-connect-to-azure-vmware-solution-if-the-on-premises-expressroute-carrier-partnersisps-dont-support-4-byte-asn"></a>オンプレミスの ExpressRoute キャリア パートナー/ISP が 4 バイトの ASN をサポートしていない場合、ExpressRoute を使用して Azure VMware Solution に接続するにはどうすればよいですか?
ExpressRoute 経由で Azure VMware Solution に接続する唯一の方法は、お客様の環境およびオンプレミスの ExpressRoute キャリア パートナー/ISP が、BGP プレフィックス ASN パス アドバタイズメントにおいて、4 バイト ASN をサポートしているか、4 バイトから 2 バイト ASN への下位互換性を有していることです。

## <a name="compute-network-storage-and-backup"></a>コンピューティング、ネットワーク、ストレージ、およびバックアップ

### <a name="is-there-more-than-one-type-of-host-available"></a>使用できるホストの種類は複数ありますか?

使用できるホストの種類は 1 つだけです。

### <a name="what-are-the-cpu-specifications-in-each-type-of-host"></a>各種ホストの CPU の仕様はどのようになっていますか?

サーバーには、デュアル 18 コア 2.3 GHz Intel CPU が搭載されています。

### <a name="how-much-memory-is-in-each-host"></a>各ホストにはどのくらいのメモリがありますか?

サーバーには 576 GB の RAM が搭載されています。

### <a name="what-is-the-storage-capacity-of-each-host"></a>各ホストのストレージ容量はどのくらいですか?

各 ESXi ホストには、2 つの vSAN ディスクグループがあり、15.2 TB の容量階層と 3.2 TB の NVMe キャッシュ階層 (各ディスクグループで 1.6 TB) があります。

### <a name="how-much-network-bandwidth-is-available-in-each-esxi-host"></a>各 ESXi ホストで使用できるネットワーク帯域幅はどれくらいですか?

Azure VMware Solution の各 ESXi ホストは、4 つの 25 Gbps NIC、ESXi のシステム トラフィック用にプロビジョニングされた 2 つの NIC、ワークロードのトラフィック用にプロビジョニングされた 2 つの NIC で構成されています。 

### <a name="is-data-stored-on-the-vsan-datastores-encrypted-at-rest"></a>vSAN データストアに保存されるデータは保存時に暗号化されますか?

はい。すべての vSAN データが、Azure Key Vault に保存されているキーを使用して既定で暗号化されます。

###  <a name="what-independent-software-vendors-isvs-backup-solutions-work-with-azure-vmware-solution"></a>Azure VMware Solution と連係する独立系ソフトウェア ベンダー (ISV) のバックアップ ソリューションにはどのようなものがありますか?

Commvault、Veritas、Veeam の各社は Azure VMware Solution と連係するように自社のバックアップ ソリューションを拡張しています。  ただし、HotAdd トランスポート モードで VMware VADP を使用するバックアップ ソリューションは、Azure VMware Solution ですぐに使用できるはずです。

### <a name="what-about-support-for-isv-backup-solutions"></a>ISV バックアップ ソリューションのサポートはどのようなものですか?

これらのバックアップ ソリューションはお客様がインストールおよび管理するため、それぞれの ISV にサポートを依頼することができます。 

### <a name="what-is-the-correct-storage-policy-for-the-dedupe-setup"></a>重複除去の設定に適切なストレージ ポリシーは何ですか?

VM テンプレートに "*thin_provision*" ストレージ ポリシーを使用します。  既定値は "*thick_provision*" です。

### <a name="are-the-snmp-infrastructure-logs-shared"></a>SNMP インフラストラクチャのログは共有されますか?

不正解です。

## <a name="hosts-clusters-and-private-clouds"></a>ホスト、クラスター、およびプライベート クラウド

### <a name="is-the-underlying-infrastructure-shared"></a>基盤となるインフラストラクチャは共有されていますか?

いいえ。プライベート クラウド ホストとクラスターは専用であり、使用前と使用後に安全に消去されます。

### <a name="what-are-the-minimum-and-maximum-number-of-hosts-per-cluster"></a>クラスターあたりのホストの最小数と最大数はどれくらいですか?

クラスターは、3 台から 16 台の ESXi ホストにスケーリングできます。 試用版クラスターは、3 台のホストに制限されます。

### <a name="can-i-scale-my-private-cloud-clusters"></a>プライベート クラウド クラスターはスケーリングできますか?

はい。クラスターは、ESXi ホストの最小数と最大数の間でスケーリングできます。 試用版クラスターは、3 台のホストに制限されます。

### <a name="what-are-trial-clusters"></a>試用版クラスターとはどのようなものですか?

試用版クラスターは、Azure VMware Solution プライベート クラウドの 1 か月間の評価に使用される 3 台のホストのクラスターです。

### <a name="can-i-use-high-end-hosts-for-trial-clusters"></a>試用版クラスターでハイエンド ホストを使用できますか?

いいえ。 ハイエンド ESXi ホストは、運用クラスターで使用するために予約されています。

## <a name="azure-vmware-solution-and-vmware-software"></a>Azure VMware Solution と VMware ソフトウェア

### <a name="what-versions-of-vmware-software-is-used-in-private-clouds"></a>プライベート クラウドで使用される VMware ソフトウェアのバージョンは何ですか?

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


### <a name="do-private-clouds-use-vmware-nsx"></a>プライベート クラウドでは VMware NSX は使用されますか?

はい。Azure VMware Solution のプライベート クラウドでは、ソフトウェア定義のネットワークのために NSX-T 2.5 が使用されます。

### <a name="can-i-use-vmware-nsx-v-in-a-private-cloud"></a>プライベート クラウドで VMware NSX-V は使用できますか?

いいえ。 NSX-T が NSX のサポートされている唯一のバージョンです。

### <a name="is-nsx-required-in-on-premises-environments-or-networks-that-connect-to-a-private-cloud"></a>オンプレミス環境またはプライベート クラウドに接続するネットワークでは NSX が必要ですか?

いいえ。オンプレミスでは NSX を使用する必要はありません。

### <a name="what-is-the-upgrade-and-update-schedule-for-vmware-software-in-a-private-cloud"></a>プライベート クラウドでの VMware ソフトウェアのアップグレードと更新のスケジュールはどのようになりますか?

プライベート クラウド ソフトウェア バンドルのアップグレードは、VMware のソフトウェア バンドルの最新リリースの単一バージョン内でソフトウェアを保持します。 プライベート クラウド ソフトウェアのバージョンは、個々のソフトウェア コンポーネント (ESXi、NSX-T、vCenter、vSAN) の最新バージョンとは異なる場合があります。

### <a name="how-often-will-the-private-cloud-software-stack-be-updated"></a>プライベート クラウド ソフトウェア スタックはどのくらいの頻度で更新されますか?

プライベート クラウド ソフトウェアは、VMware からのソフトウェア バンドルのリリースに合わせたスケジュールでアップグレードされます。 プライベート クラウドでは、アップグレードのためのダウンタイムは必要ありません。

## <a name="connectivity"></a>接続

### <a name="what-network-ip-address-planning-is-required-to-incorporate-private-clouds-with-on-premises-environments"></a>オンプレミス環境にプライベート クラウドを組み込むには、どのようなネットワーク IP アドレスを計画する必要がありますか?

Azure VMware Solution のプライベート クラウドをデプロイするには、/22 プライベート ネットワーク アドレス空間が 1 つ必要です。 このプライベート アドレス空間は、サブスクリプションの他の仮想ネットワーク、またはオンプレミス ネットワークと重複しないようにする必要があります。
 
### <a name="how-do-i-connect-from-on-premises-environments-to-an-azure-vmware-solution-private-cloud"></a>オンプレミス環境から Azure VMware Solution のプライベート クラウドにはどのように接続しますか?

次の 2 つの方法のいずれかでサービスに接続できます。 

- ExpressRoute 経由でプライベート クラウドにピアリングされる、Azure 仮想ネットワークにデプロイされた VM またはアプリケーション ゲートウェイを使用する。
- オンプレミスのデータセンターから Azure ExpressRoute 回線への ExpressRoute Global Reach を使用する。

### <a name="how-do-i-connect-a-workload-vm-to-the-internet-or-an-azure-service-endpoint"></a>ワークロード VM をインターネットまたは Azure サービス エンドポイントに接続するにはどうすればよいですか?

Azure portal で、プライベート クラウドのインターネット接続を有効にします。 NSX-T Manager を使用して、NSX-T T1 ルーターと論理スイッチを作成します。 次に、vCenter を使用して、論理スイッチによって定義されたネットワーク セグメントに VM をデプロイします。 この VM は、インターネットと Azure サービスにネットワーク アクセスできます。

### <a name="do-i-need-to-restrict-access-from-the-internet-to-vms-on-logical-networks-in-a-private-cloud"></a>インターネットからプライベート クラウド内の論理ネットワーク上の VM へのアクセスを制限する必要はありますか?

いいえ。 インターネットからプライベート クラウドへの直接的なインバウンド ネットワーク トラフィックは、既定では許可されません。  ただし、Azure VMware Solution のプライベート クラウド用に Azure portal の[パブリック IP](public-ip-usage.md) オプションを使用して、Azure VMware Solution VM をインターネットに公開することはできます。

### <a name="do-i-need-to-restrict-internet-access-from-vms-on-logical-networks-to-the-internet"></a>論理ネットワーク上の VM からインターネットへのインターネット アクセスを制限する必要はありますか?

はい。 NSX-T Manager を使用して、インターネットへの VM のアクセスを制限するファイアウォールを作成する必要があります。


### <a name="can-azure-vmware-solution-use-azure-virtual-wan-hosted-expressroute-gateways"></a>Azure VMware Solution では、Azure Virtual WAN でホストされた ExpressRoute ゲートウェイを使用できますか?
はい。

### <a name="can-transit-connectivity-be-established-between-on-premises-and-azure-vmware-solution-through-azure-virtual-wan-over-expressroute-global-reach"></a>ExpressRoute Global Reach 経由で Azure Virtual WAN を使用して、オンプレミスと Azure VMware Solution の間にトランジット接続を確立できますか?
Azure Virtual WAN では、接続されている 2 つの ExpressRoute 回線と Virtual WAN 以外の ExpressRoute ゲートウェイの間に推移的なルーティングは提供されません。 ExpressRoute Global Reach を使用すると、オンプレミスと Azure VMware Solution の接続が可能になりますが、Virtual WAN ハブではなく Microsoft のグローバル ネットワークを通過します。

### <a name="could-i-use-hcx-through-public-internet-communications-as-a-workaround-for-the-non-supportability-of-hcx-when-using-vpn-s2s-with-vwan-for-on-premises-communications"></a>オンプレミスの通信で vWAN で VPN S2S を使用する場合に、HCX がサポート対象外であることに対する回避策として、公共のインターネット通信を介して HCX を使用できますか?

現在、VMware HCX でサポートされる方法は ExpressRoute のみです。

## <a name="accounts-and-privileges"></a>アカウントと特権

### <a name="what-accounts-and-privileges-will-i-get-with-my-new-azure-vmware-solution-private-cloud"></a>新しい Azure VMware Solution プライベート クラウドでは、どのようなアカウントと特権が取得されますか?

vCenter の cloudadmin ユーザーの資格情報と、NSX-T Manager の管理者アクセス権が提供されます。 また、Azure Active Directory を組み込むために使用できる CloudAdmin グループもあります。 詳細については、[アクセスと ID の概念](concepts-identity.md)に関するページを参照してください。

### <a name="can-have-administrator-access-to-esxi-hosts"></a>ESXi ホストに管理者アクセスできますか?

いいえ。ESXi への管理者アクセスは、ソリューションのセキュリティ要件を満たすために制限されています。

### <a name="what-privileges-and-permissions-will-i-have-in-vcenter"></a>vCenter にはどのような特権とアクセス許可がありますか?

CloudAdmin グループの特権が付与されます。 詳細については、[アクセスと ID の概念](concepts-identity.md)に関するページを参照してください。

### <a name="what-privileges-and-permissions-will-i-have-on-the-nsx-t-manager"></a>NSX-T Manager にはどのような特権とアクセス許可がありますか?

NSX-T に対する完全な管理者特権があり、オンプレミスの NSX-T データセンターと同様に、vSphere ロールベースのアクセス制御を管理できます。 詳細については、[アクセスと ID の概念](concepts-identity.md)に関するページを参照してください。

> [!NOTE]
> プライベート クラウドのデプロイの一部として T0 ルーターが作成され構成されます。 この論理ルーターまたは NSX-T エッジ ノード VM を変更すると、プライベート クラウドへの接続に影響する可能性があります。

## <a name="billing-and-support"></a>課金とサポート

### <a name="how-will-pricing-be-structured-for-azure-vmware-solution"></a>Azure VMware Solution の価格体系はどのようになっていますか?

価格に関する一般的な質問については、Azure VMware Solution の[価格](https://azure.microsoft.com/pricing/details/azure-vmware)に関するページを参照してください。 

### <a name="can-azure-vmware-solution-be-purchased-through-a-microsoft-csp"></a>Microsoft CSP から Azure VMware Solution を購入できますか。

はい。顧客は CSP で管理されている Azure サブスクリプション内に Azure VMware Solution をデプロイできます。

### <a name="who-supports-azure-vmware-solution"></a>Azure VMware Solution のサポート提供元はどこですか?

Azure VMware Solution のサポートは、Microsoft が提供します。 [サポート リクエスト](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を送信できます。 

CSP で管理されているサブスクリプションの場合は、他の Azure サービス用の CSP と同様に、ソリューション プロバイダーから第 1 レベルのサポートが提供されます。

### <a name="what-accounts-do-i-need-to-create-an-azure-vmware-solution-private-cloud"></a>Azure VMware Solution のプライベート クラウドを作成するにはどのアカウントが必要ですか?

Azure サブスクリプションの Azure アカウントが必要です。

### <a name="are-red-hat-solutions-supported-on-azure-vmware-solution"></a>Red Hat ソリューションは Azure VMware Solution でサポートされていますか?

Microsoft と Red Hat は、Azure プラットフォームで実行される Red Hat エコシステムの統合されたコンタクト ポイントを提供する、同じ場所に配置されている統合されたサポート チームを共有しています。  Red Hat Enterprise Linux で動作する他の Azure platform サービスと同様に、Azure VMware Solution はクラウド アクセスと統合サポートの配下にあります。 Red Hat Enterprise Linux は、Azure 内の Azure VMware Solution 上で実行することがサポートされています。

### <a name="is-vmware-hcx-enterprise-available-and-if-so-how-much-does-it-cost"></a>VMware HCX Enterprise を利用できますか? 利用できる場合、コストはどのくらいですか?

VMware HCX Enterprise は、Azure VMware Solution で、*プレビュー* の機能/サービスとして使用できます。 Azure VMware Solution 向けの VMware HCX Enterprise はプレビュー段階ですが、無料の機能/サービスであり、プレビュー サービスの利用規約が適用されます。 VMware HCX Enterprise サービスが GA になると、課金が切り替わるという通知が 30 日間前に届きます。 サービスを停止またはオプトアウトすることができます。

### <a name="how-do-i-request-a-host-quota-increase-for-azure-vmware-solution"></a>Azure VMware Solution に対するホスト クォータの増量はどのようにリクエストすればよいですか?

CSP によって管理されるサブスクリプションの場合、顧客はパートナーに要求を送信する必要があります。 その後、パートナー チームは Microsoft と協力して、サブスクリプションのクォータを増やします。 詳細については、「[Azure VMware Solution リソースを有効にする方法](enable-azure-vmware-solution.md)」を参照してください。 

EA サブスクリプションの場合は、次の手順を使用します。 まず、次のものが必要になります。

* Microsoft との [Azure Enterprise Agreement (EA)](../cost-management-billing/manage/ea-portal-agreements.md)。
* Azure サブスクリプションの Azure アカウント。

Azure VMware Solution リソースを作成する前に、サポート チケットを提出して、ホストが割り当てられるようにします。 リクエストが確認されて実行されるまでに、最大 5 営業日かかります。 Azure VMware Solution の既存のプライベート クラウドがあり、さらに多くのホストを割り当てる必要がある場合は、同じプロセスを実行します。

1. Azure portal の **[ヘルプとサポート]** で、 **[新しいサポート リクエスト](https://rc.portal.azure.com/#create/Microsoft.Support)** を作成し、チケットに関する次の情報を提供します。
   - **問題の種類:** 技術
   - **サブスクリプション:** サブスクリプションを選択します。
   - **サービス:** [すべてのサービス] > [Azure VMware Solution]
   - **リソース:** 一般的な質問 
   - **概要:** 容量が必要です
   - **問題の種類:** 容量管理に関する問題
   - **問題のサブタイプ:** 追加のホストクォータおよび容量に関する顧客の要求

1. サポート チケットの **[詳細]** タブの **[説明]** で、次を指定します。

   - POC または運用環境 
   - リージョン名
   - ホストの数
   - その他の詳細

   >[!NOTE]
   >Azure VMware Solution では、プライベート クラウドを稼働させ、冗長 N + 1 ホストを提供するために、3 つ以上のホストを推奨しています。 

1. **[確認および作成]** を選択して、リクエストを送信します。

   サポート担当者がリクエストを確認するまで、最大 5 営業日かかります。

   >[!IMPORTANT] 
   >既存の Azure VMware Solution が既にあり、追加のホストを要求する場合は、ホストが割り当てられるまでに 5 営業日が必要であることに注意してください。 

1. ホストをプロビジョニングする前に、Azure portal で **Microsoft.AVS** リソース プロバイダーを必ず登録してください。  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   リソース プロバイダーを登録するその他の方法については、「[Azure リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。 

### <a name="are-reserved-instances-available-for-purchasing-through-the-cloud-solution-provider-csp-program"></a>予約インスタンスは、クラウド ソリューション プロバイダー (CSP) プログラムを通じて購入できますか?

はい。 CSP は顧客のために予約インスタンスを購入できます。 詳細については、[予約インスタンスを使用したコスト節約](reserved-instance.md)に関するページを参照してください。 

### <a name="does-azure-vmware-solution-offer-multi-tenancy-for-hosting-csp-partners"></a>Azure VMware Solution では CSP パートナーをホストするためのマルチテナントは提供されますか?

いいえ。 現時点では、Azure VMware Solution ではマルチテナントは提供されていません。

### <a name="will-traffic-between-on-premises-and-azure-vmware-solution-over-expressroute-incur-any-outbound-data-transfer-charge-in-the-metered-data-plan"></a>オンプレミスと ExpressRoute 経由の Azure VMware Solution 間のトラフィックでは、従量制課金接続プランでの送信データ転送料金が発生しますか?

Azure VMware Solution の ExpressRoute 回線のトラフィックは、どのような方法でも課金されません。 オンプレミスに接続している ExpressRoute 回線から Azure へのトラフィックは、ExpressRoute 価格プランに従って課金されます。


## <a name="customer-communication"></a>顧客とのコミュニケーション

### <a name="how-can-i-receive-an-alert-when-azure-sends-service-health-notifications-to-my-azure-subscription"></a>Azure でサービス正常性通知が Azure サブスクリプションに送信されるときに、アラートを受け取ることはできますか?

サービスに関する問題、計画メンテナンス、正常性の勧告、セキュリティに関する勧告の通知は、Azure portal の **Service Health** を介して公開されます。  これらの通知に対してアクティビティ ログ アラートを設定すると、適切なタイミングで対処することができます。 詳細については、「[Azure portal を使用して Service Health アラートを作成する](../service-health/alerts-activity-log-service-notifications-portal.md#create-service-health-alert-using-azure-portal)」を参照してください。

:::image type="content" source="media/service-health.png" alt-text="Service Health 通知のスクリーンショット":::



<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=21069522

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md

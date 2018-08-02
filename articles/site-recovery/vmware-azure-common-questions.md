---
title: よくある質問 - Azure Site Recovery による VMware から Azure へのレプリケーション | Microsoft Docs
description: この記事では、Azure Site Recovery を使ってオンプレミスの VMware VM を Azure にレプリケートする場合によくある質問をまとめます
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.date: 07/19/2018
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: e8d30ae6cde7c787f1aa950506e0eb74bac0c12d
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238810"
---
# <a name="common-questions---vmware-to-azure-replication"></a>よくある質問 - VMware から Azure へのレプリケーション

この記事では、オンプレミスの VMware VM を Azure にレプリケートするときによくある質問に回答します。 この記事の内容について質問がある場合は、 [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="general"></a>全般
### <a name="how-is-site-recovery-priced"></a>Site Recovery の価格
詳しくは、「[Site Recovery の価格](https://azure.microsoft.com/en-in/pricing/details/site-recovery/)」をご覧ください。

### <a name="how-do-i-pay-for-azure-vms"></a>Azure VM の支払い方法
レプリケーションの間に、データは Azure ストレージにレプリケートされ、VM 変更の料金は発生しません。 Azure へのフェールオーバーを実行すると、Site Recovery は Azure IaaS 仮想マシンを自動的に作成します。 その後は、Azure で消費するコンピューティング リソースに対して課金されます。

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>VMware から Azure へのレプリケーションによってできること
- **ディザスター リカバリー**: 完全なディザスター リカバリーを設定することができます。 このシナリオでは、オンプレミスの VMware VM を Azure ストレージにレプリケートします。 その後、オンプレミスのインフラストラクチャが使用できなくなった場合は、Azure にフェールオーバーできます。 フェールオーバーの際は、レプリケートされたデータを使って Azure VM が作成されます。 オンプレミスのデータ センターが再び使用可能になるまでは、Azure VM のアプリとワークロードにアクセスできます。 その後、Azure からオンプレミスのサイトにフェールバックできます。
- **移行**: Site Recovery を使って、オンプレミスの VMware VM を Azure に移行できます。 このシナリオでは、オンプレミスの VMware VM を Azure ストレージにレプリケートします。 その後、オンプレミスから Azure にフェールオーバーします。 フェールオーバーの後は、Azure VM で実行しているアプリとワークロードを利用できます。



## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>Azure で必要なものは何ですか?
Azure サブスクリプション、Recovery Services コンテナー、ストレージ アカウント、仮想ネットワークが必要です。 コンテナー、ストレージ アカウント、ネットワークは、同じリージョンに存在する必要があります。

### <a name="what-azure-storage-account-do-i-need"></a>どの Azure ストレージ アカウントが必要ですか?
LRS または GRS ストレージ アカウントが必要です。 地域的障害が発生した場合やプライマリ リージョンが復旧できない場合にデータの復元性を確保できるように、GRS をお勧めします。 Premium Storage はサポートされています。

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Azure アカウントには VM を作成するアクセス許可が必要ですか?
サブスクリプション管理者の場合は、必要なレプリケーション アクセス許可を持っています。 サブスクリプション管理者ではない場合は、Site Recovery を構成するときに指定するリソース グループと仮想ネットワークに Azure VM を作成するアクセス許可と、選んだストレージ アカウントに書き込むアクセス許可が必要です。 [詳細情報](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)。



## <a name="on-premises"></a>オンプレミスの 

### <a name="what-do-i-need-on-premises"></a>オンプレミスには何が必要ですか?
オンプレミスでは、Site Recovery のコンポーネントが 1 つの VMware VM にインストールされている必要があります。 また、少なくとも 1 つの ESXi ホストを含む VMware インフラストラクチャも必要であり、vCenter サーバーをお勧めします。 さらに、レプリケートする 1 つ以上の VMware VM が必要です。 VMware から Azure へのアーキテクチャについて詳しくは、[こちら](vmware-azure-architecture.md)をご覧ください。

次の 2 つの方法のいずれかで、オンプレミスの構成サーバーをデプロイすることができます。

1. 構成サーバーが事前にインストールされている VM テンプレートを使用してデプロイする。 詳細については、[こちら](vmware-azure-tutorial.md#download-the-vm-template)を参照してください。
2. 任意の Windows Server 2016 コンピューターでセットアップを使用してデプロイする。 詳細については、[こちら](physical-azure-disaster-recovery.md#set-up-the-source-environment)を参照してください。

独自の Windows Server コンピューターで構成サーバーをデプロイする手順の概要を見つけるには、保護の有効化の [Protection goal]\(保護の目標\) で、**[To Azure]\(Azure へ\)、[Not virtualized/Other]\(非仮想化/その他\)** の順に選択します。

### <a name="where-do-on-premises-vms-replicate-to"></a>オンプレミスの VM のレプリケート先はどこですか?
Azure ストレージにデータがレプリケートされます。 フェールオーバーを実行すると、Site Recovery はストレージ アカウントから Azure VM を自動的に作成します。

### <a name="what-apps-can-i-replicate"></a>どのようなアプリをレプリケートできますか?
[レプリケーション要件](vmware-physical-azure-support-matrix.md##replicated-machines)に準拠する VMware VM で実行しているすべてのアプリまたはワークロードをレプリケートできます。 また、アプリケーションに対応したレプリケーションもサポートしているため、アプリをインテリジェントな状態にフェールオーバーおよびフェールバックできます。 Site Recovery は、SharePoint、Exchange、Dynamics、SQL Server、Active Directory などの Microsoft アプリケーションと統合し、Oracle、SAP、IBM、Red Hat などの主要なベンダーと緊密に連携します。 [詳細情報](site-recovery-workload.md) を参照してください。

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>サイト間 VPN で Azure にレプリケートできますか?
Site Recovery は、パブリック エンドポイントまたは ExpressRoute のパブリック ピアリングを使って、オンプレミスから Azure ストレージにデータをレプリケートします。 サイト間 VPN ネットワーク経由のレプリケーションはサポートされていません。

### <a name="can-i-replicate-to-azure-with-expressroute"></a>ExpressRoute で Azure にレプリケートできますか?
はい。ExpressRoute を使って Azure に VM をレプリケートできます。 Site Recovery はパブリック エンドポイントを使って Azure ストレージ アカウントにデータをレプリケートするので、Site Recovery のレプリケーション用に[パブリック ピアリング](../expressroute/expressroute-circuit-peerings.md#azure-public-peering)を設定する必要があります。 VM が Azure 仮想ネットワークにフェールオーバーした後は、[プライベート ピアリング](../expressroute/expressroute-circuit-peerings.md#azure-private-peering)を使ってアクセスできます。


### <a name="why-cant-i-replicate-over-vpn"></a>VPN 経由でレプリケートできないのはなぜですか?

Azure にレプリケートする場合、レプリケーション トラフィックは Azure Storage アカウントのパブリック エンドポイントに到達するので、ExpressRoute (パブリック ピアリング) のパブリック インターネットによってのみレプリケートでき、VPN は動作しません。 



## <a name="what-are-the-replicated-vm-requirements"></a>レプリケートされる VM にはどのような要件がありますか?

レプリケーションの場合、VMware VM はサポートされているオペレーティング システムを実行している必要があります。 さらに、VM は Azure VM に対する要件を満たす必要があります。 サポート マトリックスについて詳しくは、[こちら](vmware-physical-azure-support-matrix.md##replicated-machines)をご覧ください。

## <a name="how-often-can-i-replicate-to-azure"></a>どのくらいの頻度で Azure にレプリケートできますか?
VMware VM を Azure にレプリケートするときは、レプリケーションは継続的に行われます。

## <a name="can-i-extend-replication"></a>レプリケーションを拡張することはできますか?
拡張またはチェーン レプリケーションはサポートされていません。 [フィードバック フォーラム](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication)でこの機能を要求してください。

## <a name="can-i-do-an-offline-initial-replication"></a>オフラインの初期レプリケーションを行うことはできますか?
これはサポートされていません。 [フィードバック フォーラム](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)でこの機能を要求してください。

### <a name="can-i-exclude-disks"></a>ディスクを除外することはできますか?
はい、レプリケーションからディスクを除外できます。 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>ダイナミック ディスクを含む VM をレプリケートできますか?
ダイナミック ディスクをレプリケートすることができます。 オペレーティング システム ディスクはベーシック ディスクである必要があります。

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>マルチ VM 整合性用のレプリケーション グループを使用する場合、新しい VM を既存のレプリケーション グループに追加できますか?
はい、既存のレプリケーション グループのレプリケーションを有効にするときに、そのグループに新しい VM を追加できます。 レプリケーションが開始された後で既存のレプリケーション グループに VM を追加することはできません。既存の VM 用のレプリケーション グループを作成することもできません。

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>レプリケートされる VM を、ディスクの追加またはサイズ変更によって変更することはできますか?

Azure への VMware のレプリケーションでは、ディスクのサイズを変更できます。 新しいディスクを追加する場合は、ディスクを追加し、VM の保護を再度有効にする必要があります。

## <a name="configuration-server"></a>構成サーバー

### <a name="what-does-the-configuration-server-do"></a>構成サーバーは何を行いますか?
構成サーバーは、次のようなオンプレミスの Site Recovery コンポーネントを実行します。 
- 構成サーバー: オンプレミスと Azure の間の通信を調整し、データのレプリケーションを管理します。
- プロセス サーバー: レプリケーション ゲートウェイとして機能します。 レプリケーション データを受信し、そのデータをキャッシュ、圧縮、暗号化によって最適化して、Azure Storage に送信します。また、プロセス サーバーは、レプリケートする VM へのモビリティ サービスのインストールや、オンプレミスの VMware VM の自動検出も行います。
- マスター ターゲット サーバー: Azure からのフェールバック中にレプリケーション データを処理します。

### <a name="where-do-i-set-up-the-configuration-server"></a>構成サーバーはどこに設定しますか?
構成サーバーには、高可用性のオンプレミス VMware VM が 1 つ必要です。

### <a name="what-are-the-requirements-for-the-configuration-server"></a>構成サーバーにはどのような要件がありますか?

前提条件については、[こちら](vmware-azure-deploy-configuration-server.md#prerequisites)をご覧ください。

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>テンプレートを使う代わりに、手動で構成サーバーを設定できますか?
最新バージョンの OVF テンプレートを使って、[構成サーバーの VM を作成する](vmware-azure-deploy-configuration-server.md)ことをお勧めします。 何らかの理由でそれができない場合は (VMware サーバーにアクセスできない場合など)、ポータルから[統合セットアップ ファイルをダウンロード](physical-azure-set-up-source.md)して、VM でそれを実行できます。 

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>構成サーバーは複数のリージョンにレプリケートできますか?
いいえ。 これを行うには、リージョンごとに構成サーバーを設定する必要があります。

### <a name="can-i-host-a-configuration-server-in-azure"></a>Azure で構成サーバーをホストできますか?
可能ですが、構成サーバーを実行している Azure VM は、オンプレミスの VMware のインフラストラクチャや VM と通信する必要があります。 おそらく、オーバーヘッドを実行することはできません。


### <a name="where-can-i-get-the-latest-version-of-the-configuration-server-template"></a>構成サーバーの最新バージョンのテンプレートはどこで入手できますか?
最新バージョンは、[Microsoft Download Center](https://aka.ms/asrconfigurationserver) からダウンロードできます。

### <a name="how-do-i-update-the-configuration-server"></a>構成サーバーはどのようにして更新できますか?
更新プログラムのロールアップをインストールします。 最新の更新情報については、[更新プログラムの wiki ページ](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx)をご覧ください。

## <a name="mobility-service"></a>モビリティ サービス

### <a name="where-can-i-find-the-mobility-service-installers"></a>モビリティ サービスのインストーラーはどこにありますか?
インストーラーは、構成サーバーの **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** フォルダーにあります。

## <a name="how-do-i-install-the-mobility-service"></a>モビリティ サービスはどのようにしてインストールしますか?
[プッシュ インストール](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)を使って、[UI](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui) からの手動インストールで、または [PowerShell](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt) を使って、レプリケートする各 VM にインストールします。 または、[System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md) や [Azure Automation と DSC](vmware-azure-mobility-deploy-automation-dsc.md) などのデプロイ ツールを使って、デプロイすることもできます。



## <a name="security"></a>セキュリティ

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Site Recovery では、VMware サーバーに対してどのようなアクセスが必要ですか?
Site Recovery では、次のことを実行するために、VMware サーバーへのアクセスが必要です。

- 構成サーバーおよび他のオンプレミスの Site Recovery コンポーネントを実行する VMware VM を設定します。 [詳細情報](vmware-azure-deploy-configuration-server.md)
- レプリケーション対象の VM を自動的に検出します。 自動検出用のアカウントの準備方法を確認してください。 [詳細情報](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Site Recovery では、VMware VM に対してどのようなアクセスが必要ですか?

- レプリケートのためには、VMware VM に Site Recovery のモビリティ サービスがインストールされて実行していることが必要です。 ツールを手動でデプロイすることも、VM のレプリケーションを有効にするときにサービスのプッシュ インストールを行うよう Site Recovery に指定することもできます。 プッシュ インストールの場合、Site Recovery には、サービス コンポーネントのインストールに使用できるアカウントが必要です。 [詳細情報](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation)。 プロセス サーバー (構成サーバーで既定で実行しています) がこのインストールを実行します。 モビリティ サービスのインストールについて詳しくは、[こちら](vmware-azure-install-mobility-service.md)をご覧ください。
- レプリケーションの間に、VM は Site Recovery と次のように通信します。
    - VM は、レプリケーション管理のために、ポート HTTPS 443 で構成サーバーと通信します。
    - VM は、ポート HTTPS 9443 でレプリケーション データをプロセス サーバーに送信します (変更可能)。
    - マルチ VM 整合性を有効にすると、VM はポート 20004 で相互に通信します。


### <a name="is-replication-data-sent-to-site-recovery"></a>レプリケーション データは Site Recovery に送信されますか?
いいえ。Site Recovery は、レプリケートされたデータをインターセプトすることも、VM での実行内容に関するどのような情報を持つこともありません。 レプリケーション データは、VMware ハイパーバイザーと Azure ストレージの間で交換されます。 Site Recovery には、これらのデータをインターセプトする能力はありません。 レプリケーションとフェールオーバーを調整するために必要なメタデータのみが、Site Recovery サービスに送信されます。  

Site Recovery は ISO 27001:2013、27018、HIPAA、DPA の認証を受けており、SOC2 および FedRAMP JAB の評価が進行中です。

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>オンプレミスのメタデータを地理的領域内に保つことができますか?
はい。 リージョンにコンテナーを作成するとき、Site Recovery によって使われるすべてのメタデータは、そのリージョンの地理的な境界内に維持されます。

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery はレプリケーションを暗号化しますか。
はい、転送中の暗号化と [Azure での暗号化](https://docs.microsoft.com/azure/storage/storage-service-encryption)の両方がサポートされています。


## <a name="failover-and-failback"></a>フェールオーバーとフェールバック
### <a name="how-far-back-can-i-recover"></a>過去のどの時点まで遡って復旧できますか?
VMware から Azure の場合、使うことができる最も古い復旧ポイントは 72 時間です。

### <a name="how-do-i-access-azure-vms-after-failover"></a>フェールオーバー後にはどのようにして Azure VM にアクセスできますか?
フェールオーバー後、Azure VM には、セキュリティで保護されたインターネット接続、サイト間 VPN、または Azure ExpressRoute 経由でアクセスできます。 接続するにはさまざまこと準備する必要があります。 [詳細情報](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>フェールオーバーされたデータに回復力はありますか?
Azure は復元するように設計されています。 Site Recovery は、Azure SLA に従ってセカンダリ Azure データセンターにフェールオーバーする機能を備えています。 フェールオーバーが発生した場合、お客様のメタデータとコンテナーは、お客様が選択したコンテナーと同じリージョン内に保持されます。

### <a name="is-failover-automatic"></a>フェールオーバーは自動で行われますか。
[フェールオーバー](site-recovery-failover.md)は自動では行われません。 ポータルで 1 回クリックするだけでフェールオーバーを開始できます。または [PowerShell](/powershell/module/azurerm.siterecovery) を使ってフェールオーバーをトリガーすることもできます。

### <a name="can-i-fail-back-to-a-different-location"></a>異なる場所にフェールバックすることはできますか?
はい、Azure にフェールオーバーした場合、元の場所が利用できないときは、別の場所にフェールバックすることができます。 [詳細情報](concepts-types-of-failback.md#alternate-location-recovery-alr)。

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>フェールバックに VPN または ExpressRoute が必要なのはなぜですか?

Azure からフェールバックするときは、Azure からオンプレミスの VM にデータがコピーされるので、プライベート アクセスが必要です。 



## <a name="automation-and-scripting"></a>自動化とスクリプト

### <a name="can-i-set-up-replication-with-scripting"></a>スクリプトでレプリケーションを設定できますか?
はい。 Rest API、PowerShell、Azure SDK のいずれかを使って、Site Recovery ワークフローを自動化することができます。[詳細情報](vmware-azure-disaster-recovery-powershell.md)

## <a name="performance-and-capacity"></a>パフォーマンスと容量
### <a name="can-i-throttle-replication-bandwidth"></a>レプリケーションの帯域幅を調整することができますか?
はい。 [詳細情報](site-recovery-plan-capacity-vmware.md)。


## <a name="next-steps"></a>次の手順
* サポート要件を[確認する](vmware-physical-azure-support-matrix.md)。
* VMware から Azure へのレプリケーションを[設定する](vmware-azure-tutorial.md)。

---
title: "Azure Site Recovery を使用して Azure へのレプリケーションを行うための前提条件 | Microsoft Docs"
description: "この記事では、Azure Site Recovery サービスを使用して VM や物理マシンを Azure にレプリケートするための前提条件について説明します。"
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/11/2016
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: 46ea97f5c13b7befc78466fae92a737107be8f83
ms.openlocfilehash: 31cae0cf625552c0e9547d0e514c6017c6a179ef

---

#  <a name="prerequisites-for-replication-to-azure-using-azure-site-recovery"></a>Azure Site Recovery を使用して Azure へのレプリケーションを行うための前提条件

 Site Recovery とは、クラウド (Azure) またはセカンダリ データセンターへのオンプレミスの物理サーバーおよび仮想マシンのレプリケーションを統制することで BCDR 戦略を支援する Azure サービスです。 プライマリ ロケーションで障害が発生した場合は、セカンダリ ロケーションにフェールオーバーしてアプリとワークロードの可用性を維持できます。 プライマリの場所が通常の動作に戻ったときに、その場所にフェールバックすることができます。 詳細については、「 [Site Recovery とは](site-recovery-overview.md)

この記事では、Site Recovery を使用して Azure へのレプリケーションを開始するために必要な前提条件について説明します。

コメントはこの記事の末尾に投稿し、技術的な質問は [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="azure-requirements"></a>Azure の要件

**要件** | **詳細**
--- | ---
**Azure アカウント** | [Microsoft Azure](http://azure.microsoft.com/) アカウント。<br/><br/> アカウントがなくても、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。
**Site Recovery サービス** | [こちら](https://azure.microsoft.com/pricing/details/site-recovery/) をご覧ください。 |
**Azure Storage** | レプリケートされたデータを格納するための Azure ストレージ アカウントが必要です。このアカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。 レプリケートされたデータは Azure Storage に格納され、フェールオーバーが発生すると Azure VM が作成されます。<br/><br/> フェールオーバーされた Azure VM に使用するリソース モデルに応じて、アカウントを [Resource Manager モデル](../storage/storage-create-storage-account.md)または[クラシック モデル](../storage/storage-create-storage-account-classic-portal.md)でセットアップできます。<br></br>[GRS](../storage/storage-redundancy.md#geo-redundant-storage) または LRS ストレージを使用できます。 地域的障害が発生した場合やプライマリ リージョンが復旧できない場合にデータの復元性を確保できるように、GRS をお勧めします。<br/><br/> Azure Portal で VMware VM または物理サーバーをレプリケートする場合は、Premium ストレージがサポートされています。 [Premium Storage](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage) は、通常、IO を集中的に行うワークロードをホストするために常に高い IO パフォーマンスと低遅延時間が必要になる仮想マシンで使用します。 レプリケートされたデータに Premium Storage を使用する場合は、オンプレミス データへの継続的な変更をキャプチャしたレプリケーション ログを保存するために、Standard ストレージ アカウントも必要になります。<br/><br/>
**ストレージの制限事項** | Site Recovery で使用されるストレージ アカウントをリソース グループ間で移動することはできません (サブスクリプション内であっても、異なるサブスクリプション間であっても同様)。<br/><br/> インド中部およびインド南部での Premium Storage アカウントへのレプリケーションは現在サポートされていません。
**Azure ネットワーク** | フェールオーバー後に Azure VM の接続先となる Azure ネットワークが必要です。このネットワークは、Recovery Services コンテナーと同じリージョンに存在する必要があります。<br/><br/> Azure ポータルでは、[Resource Manager モデル](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)または[クラシック モデル](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)でネットワークを作成できます。<br/><br/> VMM から Azure にレプリケートする場合、VMM VM ネットワークと Azure ネットワーク間の[ネットワーク マッピング](site-recovery-network-mapping.md)を設定して、Azure VM がフェールオーバー後に適切なネットワークに接続できるようします。
**ネットワークの制限** | Site Recovery で使用されるネットワーク アカウントをリソース グループ間で移動することはできません (サブスクリプション内であっても、異なるサブスクリプション間であっても同様)。
**ネットワーク マッピング** | VMM クラウド内で Hyper-V VM をレプリケートする場合は、Azure VM がフェールオーバー後に作成された場合に適切なネットワークに接続されるように[ネットワーク マッピング](site-recovery-network-mapping.md)をセットアップする必要があります。

>[!NOTE]
>以下のセクションでは、お客様の環境で使用されるさまざまなコンポーネントの前提条件について説明します。 特定の構成のサポートについて詳しくは、[サポート マトリックス](site-recovery-support-matrix.md)をご覧ください。
>

## <a name="disaster-recovery-of-vmware-virtual-machines-or-physical-windowslinux-servers-to-azure"></a>VMware 仮想マシンまたは Windows/Linux 物理サーバーを Azure に障害復旧する場合
以下に示すのは、VMware 仮想マシンや Windows/Linux 物理サーバーを障害復旧するために、[上記の要件](#Azure requirements)に加えて必要となるコンポーネントです。


1. **構成サーバーまたは追加のプロセス サーバー**: オンプレミス サイトと Azure 間の通信を調整し、データのレプリケーションを管理するために、オンプレミス マシンを構成サーバーとして設定する必要があります。 <br></br>

2. **VMware vCenter/vSphere ホスト**

| **コンポーネント** | **要件** |
| --- | --- |
| **vSphere** | 1 つ以上の VMware vSphere ハイパーバイザー。<br/><br/>ハイパーバイザーでは、最新の更新プログラムが適用された vSphere バージョン 6.0、5.5、または 5.1 が実行されている必要があります。<br/><br/>vSphere ホストと vCenter サーバーはプロセス サーバーと同じネットワークに配置することをお勧めします (専用のプロセス サーバーをセットアップした場合を除き、これは構成サーバーが配置されているネットワークです)。 |
| **vCenter** | vSphere ホストを管理する VMware vCenter サーバーをデプロイすることをお勧めします。 このサーバーでは、最新の更新プログラムがインストールされた vCenter バージョン 6.0 または 5.5 を実行している必要があります。<br/><br/>**制限事項**: Site Recovery は、クロス vCenter vMotion、仮想ボリューム、ストレージ DRS などの vCenter および vSphere 6.0 の新しい機能をサポートしていません。 Site Recovery のサポートは、バージョン 5.5 で提供されていた機能に限定されます。||

3.**レプリケーション対象のマシンの前提条件**


| **コンポーネント** | **要件** |
| --- | --- |
| **オンプレミス (VMware VM)** | レプリケーション対象の VM では、VMware ツールがインストールされ、実行されている必要があります。<br/><br/> VM は、Azure VM を作成するための [Azure の前提条件](site-recovery-best-practices.md#azure-virtual-machine-requirements)に準拠している必要があります。<br/><br/>保護対象のマシン上の個々のディスク容量が 1023 GB 以下である必要があります。 <br/><br/>コンポーネントのインストール用として、インストール ドライブに 2 GB 以上の空き領域が必要です。<br/><br/>**ポート 20004**: マルチ VM 整合性を有効にする場合、VM のローカル ファイアウォールで開く必要があります。<br/><br/>マシン名は 1 - 63 文字 (英字、数字、ハイフン) にする必要があります。 文字または数字で始まり、文字または数字で終わる必要があります。 Azure 上の名前は、マシンのレプリケーションを有効にした後で変更できます。<br/><br/> |
| **Windows マシン (物理または VMware)** | マシンでは、サポートされている 64 ビット オペレーティング システム (Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 SP1 以降) が実行されている必要があります。<br/><br/> オペレーティング システムは C:\ ドライブにインストールする必要があります。 OS ディスクは、ダイナミック ディスクではなく、Windows ベーシック ディスクである必要があります。 データ ディスクはダイナミックでもかまいません。<br/><br/>|
| **Linux マシン** (物理または VMware) | サポートされている 64 ビット オペレーティング システム (Red Hat Enterprise Linux 6.7、7.1、7.2、Centos 6.5、6.6、6.7、7.0、7.1、7.2、Red Hat 互換カーネルまたは Unbreakable Enterprise Kernel リリース 3 (UEK3) を実行している Oracle Enterprise Linux 6.4、6.5、SUSE Linux Enterprise Server 11 SP3) が必要です。<br/><br/>保護対象のマシン上の /etc/hosts ファイルには、ローカル ホスト名をすべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが含まれている必要があります。<br/><br/>フェールオーバー後に、Linux を実行する Azure 仮想マシンに Secure Shell クライアント (ssh) を使用して接続する場合、保護されたマシンのシステム ブート時に Secure Shell サービスが自動的に起動し、ファイアウォールの規則で仮想マシンへの ssh 接続が許可されるように設定していることを確認してください。<br/><br/>ホスト名、マウント ポイント、デバイス名、および Linux システム パスとファイル名 (例: /etc/; /usr) には英語のみ使用できます。<br/><br/>

## <a name="disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-vmm"></a>Hyper-V 仮想マシンを Azure (非 VMM) に障害復旧する場合

以下に示すのは、VMM クラウド内の Hyper-V 仮想マシンを障害復旧するために、[上記の要件](#Azure requirements)に加えて必要となるコンポーネントです。

| **前提条件** | **詳細** |
| --- | --- |
| **Hyper-V ホスト** |最新の更新プログラムと Hyper-V ロールが有効になっているか、Microsoft Hyper-V Server 2012 R2 を含む Windows Server 2012 R2 を実行する 1 つ以上のオンプレミスのサーバー。<br></br>Hyper-V サーバーには、1 つ以上の仮想マシンが搭載されます。<br></br>Hyper-V サーバーは、直接、またはプロキシを経由して、インターネットに接続します。<br></br>Hyper-V サーバーには [KB2961977](https://support.microsoft.com/en-us/kb/2961977) で説明されている修正プログラムをインストールする必要があります。
|**プロバイダーとエージェント**| Azure Site Recovery のデプロイ時に、Azure Site Recovery Provider をインストールします。 保護対象の仮想マシンを実行している各 Hyper-V サーバーにプロバイダーをインストールすると、Azure Recovery Services エージェントもインストールされます。 <br></br>Site Recovery コンテナー内のすべての Hyper-V サーバーで、プロバイダーとエージェントが同じバージョンである必要があります。<br></br>プロバイダーは、インターネット経由で Azure Site Recovery に接続する必要があります。 トラフィックを直接送信することも、プロキシ経由で送信することもできます。 HTTPS ベースのプロキシはサポートされていないことに注意してください。 プロキシ サーバーでは、次の URL へのアクセスを許可する必要があります:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/>サーバーに IP アドレスベースのファイアウォール規則がある場合、規則で Azure との通信を許可していることを確認します。<br></br> [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。<br></br> ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (Access Control と ID 管理に使用されます)。


## <a name="disaster-recovery-of-hyper-v-virtual-machines-in-vmm-clouds-to-azure"></a>VMM クラウド内にある Hyper-V 仮想マシンの Azure への障害復旧

以下に示すのは、VMM クラウド内の Hyper-V 仮想マシンを障害復旧するために、[上記の要件](#Azure requirements)に加えて必要となるコンポーネントです。

| **前提条件** | **詳細** |
| --- | --- |
| **VMM** |**System Center 2012 R2 以上**で実行されている、1 つ以上の VMM サーバー。 各 VMM サーバーに&1; つ以上のクラウドが構成されている必要があります。 <br></br>クラウドには以下のものが含まれている必要があります<br>-&1; つ以上の VMM ホスト グループ。<br/>- 各ホスト グループ内に&1; つ以上の Hyper-V ホスト サーバーまたはクラスター。<br/><br/>[VMM クラウドの設定](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx)について理解を深めます。 |
| **Hyper-V** |Hyper-V ホスト サーバーは、Hyper-V ロールを持つ **Windows Server 2012 R2** 以降か、**Microsoft Hyper-V Server 2012 R2** が実行され、最新の更新プログラムがインストールされている必要があります。<br/><br/> Hyper-V サーバーに&1; つ以上の VM が含まれている必要があります。<br/><br/> レプリケート対象の VM を含む Hyper-V ホスト サーバーまたはクラスターは、VMM クラウドで管理する必要があります。<br/><br/>Hyper-V サーバーは、直接、またはプロキシを経由して、インターネットに接続します。<br/><br/>Hyper-V サーバーには、記事 [2961977](https://support.microsoft.com/kb/2961977) で説明されている修正プログラムをインストールする必要があります。<br/><br/>Azure へのデータ レプリケーションを行うには、Hyper-V ホスト サーバーがインターネットにアクセスできることが必要です。 |
| **プロバイダーとエージェント** |Azure Site Recovery をデプロイする際に、VMM サーバーに Azure Site Recovery プロバイダーをインストールし、Hyper-V ホストに Recovery Services エージェントをインストールします。 プロバイダーとエージェントは、直接またはプロキシを使用して、インターネット経由で Azure に接続する必要があります。 HTTPS ベースのプロキシはサポートされていません。 VMM サーバーおよび Hyper-V ホスト上のプロキシ サーバーは、以下へのアクセスを許可する必要があります。 <br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>VMM サーバーに IP アドレスベースのファイアウォール規則がある場合、規則で Azure との通信を許可していることを確認します。<br/><br/> [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。<br/><br/> ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (Access Control と ID 管理に使用されます)。<br/><br/> |

### <a name="replicated-machine-prerequisites"></a>レプリケーション対象のマシンの前提条件
| **コンポーネント** | **詳細** |
| --- | --- |
| **保護対象の VM** | Site Recovery では、[Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx) でサポートされているすべてのオペレーティング システムがサポートされます。<br></br>VM は、Azure VM を作成するための [Azure の前提条件](site-recovery-best-practices.md#azure-virtual-machine-requirements)に準拠している必要があります。 マシン名は 1 - 63 文字 (英字、数字、ハイフン) にする必要があります。 文字または数字で始まり、文字または数字で終わる必要があります。 <br></br>名前は、VM のレプリケーションを有効にした後で変更できます。 <br/><br/> 保護対象のマシン上の個々のディスク容量が 1023 GB 以下である必要があります。 VM は最大 16 個のディスク (最大 16 TB) に対応できます。<br/><br/>


## <a name="disaster-recovery-of-hyper-v-virtual-machines-in-vmm-clouds-to-a-customer-owned-site"></a>VMM クラウド内にある Hyper-V 仮想マシンをお客様所有サイトに障害復旧する場合

以下に示すのは、VMM クラウド内の Hyper-V 仮想マシンをお客様所有のサイトに障害復旧するために、[上記の要件](#Azure requirements)に加えて必要となるコンポーネントです。

| **コンポーネント** | **詳細** |
| --- | --- |
| **VMM** |  プライマリ サイトとセカンダリ サイトに VMM サーバーを&1; 台ずつデプロイすることをお勧めします。<br/><br/> [単一の VMM サーバー上のクラウド間でレプリケート](site-recovery-single-vmm.md)できます。 これを行うには、VMM サーバーに少なくとも&2; つのクラウドが構成されている必要があります。<br/><br/> VMM サーバーは、最新の更新プログラムがインストールされている **System Center 2012 SP1 以降**を実行している必要があります。<br/><br/> 各 VMM サーバーには&1; つ以上のクラウドが必要です。 すべてのクラウドには Hyper-V キャパシティ プロファイル セットが必要です。 <br/><br/>クラウドには、1 つ以上の VMM ホスト グループが含まれている必要があります。 [VMM クラウドの設定](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)について理解を深めます。<br/><br/> VMM サーバーは、インターネットにアクセスできる必要があります。 |
| **Hyper-V** | Hyper-V サーバーでは、**Hyper-V ロールを設定済みの Windows Server 2012 以降**が実行され、最新の更新プログラムがインストールされている必要があります。<br/><br/> Hyper-V サーバーに&1; つ以上の VM が含まれている必要があります。<br/><br/>  Hyper-V ホスト サーバーが、プライマリおよびセカンダリの VMM クラウドに配置されている必要があります。<br/><br/> Windows Server 2012 R2 のクラスターで Hyper-V を実行する場合は、[更新プログラム 2961977](https://support.microsoft.com/kb/2961977) をインストールする必要があります。<br/><br/> Windows Server 2012 上のクラスターで Hyper-V を実行する場合、静的 IP アドレス ベースのクラスターが存在すると、クラスター ブローカーは自動では作成されません。 クラスター ブローカーを手動で構成する必要があります。 詳細については、[こちら](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)を参照してください。 |
| **プロバイダー** | Site Recovery をデプロイする際に、Azure Site Recovery Provider を VMM サーバーにインストールします。 プロバイダーは、HTTPS 443 経由で Site Recovery と通信して、レプリケーションを調整します。 データのレプリケーションは、LAN または VPN 接続を経由してプライマリとセカンダリの Hyper-V サーバー間で実行されます。<br/><br/> VMM サーバーで実行されるプロバイダーには、次の URL へのアクセス権が必要です。<br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>VMM サーバーから [Azure データセンターの IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)へのファイアウォール通信と、HTTPS (443) プロトコルの使用を許可してください。 |



<!--HONumber=Jan17_HO2-->



---
title: "Azure Site Recovery のデプロイの前提条件 | Microsoft Docs"
description: "この記事では、Azure Site Recovery でレプリケーションを設定する際の前提条件について説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/04/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 92abadf68e4acbd9daae9e15dcfdce53563f6eb2
ms.openlocfilehash: a407f5ebe096db74820ff93354a636bc63894b1d

---

# <a name="site-recovery-deployment-prerequisites"></a>Site Recovery のデプロイの前提条件

組織には、予定されたダウンタイムおよび予定外のダウンタイム時にアプリ、ワークロード、およびデータの実行と利用可能な状態を維持し、できるだけ早く通常の動作状態に復旧させる方法を決定する BCDR の戦略が必要です。 Site Recovery とは、クラウド (Azure) またはセカンダリ データセンターへのオンプレミスの物理サーバーおよび仮想マシンのレプリケーションを統制することで BCDR 戦略を支援する Azure サービスです。 プライマリ ロケーションで障害が発生した場合は、セカンダリ ロケーションにフェールオーバーしてアプリとワークロードの可用性を維持します。 プライマリの場所が通常の動作に戻ると、その場所にフェールバックします。 詳細については、「 [Site Recovery とは](site-recovery-overview.md)

この記事では、Site Recovery のレプリケーション シナリオでのデプロイの前提条件の概要を説明します。  

コメントはこの記事の末尾に投稿し、技術的な質問は [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。



## <a name="azure-deployment-models"></a>Azure のデプロイメント モデル

Azure には、リソースの作成と操作に関して、Azure Resource Manager モデルとクラシック モデルという 2 種類の[デプロイメント モデル](../azure-resource-manager/resource-manager-deployment-model.md)があります。 また、ポータルも 2 つあります。クラシック デプロイメント モデルをサポートする [Azure クラシック ポータル](https://manage.windowsazure.com/)と、両方のデプロイメント モデルをサポートする [Azure Portal](https://ms.portal.azure.com/) です。

新しい Site Recovery シナリオは [Azure Portal](https://portal.azure.com) でデプロイする必要があります。 このポータルは、新機能と強化されたデプロイ エクスペリエンスを提供します。 従来のポータルでは、資格情報コンテナーを管理することはできますが、新しい資格情報コンテナーを作成することはできません。


## <a name="azure-account-requirements"></a>Azure アカウントの要件

**要件** | **詳細**
--- | --- 
**Azure アカウント** | [Microsoft Azure](http://azure.microsoft.com/) アカウント。<br/><br/> アカウントがなくても、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。 [こちら](https://azure.microsoft.com/pricing/details/site-recovery/) をご覧ください。


## <a name="azure-storage-requirements"></a>Azure Storage の要件

レプリケートされたデータは Azure Storage に格納され、フェールオーバーが発生すると Azure VM が作成されます。

**要件** | **詳細**
--- | --- 
[Azure Storage アカウント](../storage/storage-introduction.md) | [GRS](../storage/storage-redundancy.md#geo-redundant-storage) または LRS ストレージを使用できます。<br/><br/> 地域的障害が発生した場合やプライマリ リージョンが復旧できない場合にデータの復元性を確保できるように、GRS をお勧めします。 [詳細情報](https://docs.microsoft.com/en-us/azure/storage/storage-redundancy)
**Azure ポータル** | Azure Portal では、Resource Manager ストレージ アカウントまたはクラシック ストレージ アカウントを使用できます。
**Premium Storage** | [Premium Storage](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage) は、Azure Portal で VMware VM または物理サーバーを Azure にレプリケートする場合にサポートされます。<br/><br/> 通常、Premium Storage は、IO を集中的に行うワークロードをホストするために常に高い IO パフォーマンスと低遅延時間が必要になる仮想マシンで使用します。<br/><br/> Premium Storage を使用している場合、オンプレミス データの継続的な変更をキャプチャするレプリケーション ログを保存するために、Standard ストレージ アカウントも必要になります。
**ストレージの制限事項** | 従来のポータルでは、GRS がのみサポートされます。<br/><br/> Azure Portal で作成されたストレージ アカウントは、リソース グループ間で移動できません。<br/><br/> インド中部およびインド南部での Premium Storage アカウントへのレプリケーションは現在サポートされていません。

## <a name="azure-network-requirements"></a>Azure ネットワークの要件

フェールオーバー後に作成された Azure VM の接続先となる Azure ネットワークが必要です。

**要件** | **詳細**
--- | ---
**ネットワークのリージョン** | ネットワークは、コンテナーと同じリージョンにある必要があります。
**Azure ポータル** | Azure Portal では、Resource Manager ネットワークまたは従来のネットワークを使用できます。
**ネットワーク マッピング** | VMM から Azure にレプリケートする場合、VMM VM ネットワークと Azure ネットワーク間の[ネットワーク マッピング](site-recovery-network-mapping.md)を設定して、Azure VM がフェールオーバー後に適切なネットワークに接続できるようします。


## <a name="vmware-replication-requirements-to-azure"></a>VMware のレプリケーションの要件 (Azure)

**コンポーネント** | **要件**
--- | ---
**構成サーバー** | Windows Server 2012 R2 を実行しているオンプレミスの物理マシンまたは仮想マシン。 オンプレミスの Site Recovery コンポーネントはすべてこのマシンにインストールします。<br/><br/>VMware VM のレプリケーションでは、高可用性 VMware VM としてこのサーバーをデプロイすることをお勧めします。 <br/><br/>サーバーが VMware VM の場合は、ネットワーク アダプターの種類が VMXNET3 である必要があります。 別の種類のネットワーク アダプターを使用している場合は、vSphere 5.5 サーバーに [VMware アップデート](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)をインストールする必要があります。<br/><br/>サーバーには、静的 IP アドレスが必要です。<br/><br/>サーバーをドメイン コントローラーにすることはできません。<br/><br/>サーバーのホスト名は 15 文字以内である必要があります。<br/><br/>オペレーティング システムは英語版に限定されます。<br/><br/> VMware vSphere PowerCLI 6.0  サーバーをインストールします。<br/><br/>構成サーバーがインターネットにアクセスできる必要があります。 次のような発信アクセスが必要です。<br/><br/>Site Recovery コンポーネントのセットアップ中に実行される HTTP 80 での一時的なアクセス (MySQL をダウンロードするため)<br/><br/>レプリケーション管理のための HTTPS 443 での継続的な発信アクセス<br/><br/>レプリケーション トラフィックのための HTTPS 9443 での継続的な発信アクセス (このポートは変更可能)<br/><br/> MySQL のダウンロード用に ``http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi`` を許可します。<br/><br/> サーバーは、[これらの URL](#requirements-for-url-access) にもアクセスできる必要があります。
**プロセス サーバー** | 構成サーバーに既定でインストールされます。<br/><br/> レプリケーション ゲートウェイとして機能します。 プロセス サーバーは保護されたソース マシンからレプリケーション データを受信し、キャッシュ、圧縮、および暗号化を使用して最適化してから、データを Azure Storage に送信します。 また、保護されたマシンへのモビリティ サービスのプッシュ インストールを処理し、VMware VM の自動検出を実行します。 デプロイメントの拡大に合わせて、増大するレプリケーション トラフィックの処理を実行する独立した専用プロセス サーバーを追加できます。
**マスター ターゲット サーバー** | 構成サーバーに既定でインストールされます。 Azure からのフェールバック中にレプリケーション データを処理します。
**vSphere ホスト** | 1 つ以上の VMware vSphere ハイパーバイザー。<br/><br/>ハイパーバイザーでは、最新の更新プログラムが適用された vSphere バージョン 6.0、5.5、または 5.1 が実行されている必要があります。<br/><br/>vSphere ホストと vCenter サーバーはプロセス サーバーと同じネットワークに配置することをお勧めします (専用のプロセス サーバーをセットアップした場合を除き、これは構成サーバーが配置されているネットワークです)。 
**vCenter サーバー** | vSphere ホストを管理する VMware vCenter サーバーをデプロイすることをお勧めします。 このサーバーでは、最新の更新プログラムがインストールされた vCenter バージョン 6.0 または 5.5 を実行している必要があります。<br/><br/>**制限事項**: Site Recovery は、クロス vCenter vMotion、仮想ボリューム、ストレージ DRS などの vCenter および vSphere 6.0 の新しい機能をサポートしていません。 Site Recovery のサポートは、バージョン 5.5 で提供されていた機能に限定されます。

### <a name="vmware-vm-requirements-to-azure"></a>VMware VM の要件 (Azure)

**コンポーネント** | **要件**
--- | ---
**VMware VM** | レプリケーション対象の VM では、VMware ツールがインストールされ、実行されている必要があります。<br/><br/> VM は、Azure VM を作成するための [Azure の前提条件](site-recovery-support-matrix.md#support-for-azure-vms)に準拠している必要があります。<br/><br/>保護対象のマシン上の個々のディスク容量が 1023 GB 以下である必要があります。 VM は最大 64 個のディスク (従って最大 64 TB) に対応できます。 <br/><br/>コンポーネントのインストール用として、インストール ドライブに 2 GB 以上の空き領域が必要です。<br/><br/>**制限事項**: ディスクが暗号化された VM の保護はサポートされていません。<br/><br/>**制限事項**: 共有ディスク ゲスト クラスターはサポートされていません。<br/><br/>**ポート 20004**: マルチ VM 整合性を有効にする場合、VM のローカル ファイアウォールで開く必要があります。<br/><br/>Unified Extensible Firmware Interface (UEFI) ブートまたは拡張ファームウェア インターフェイス (EFI) ブートで起動するマシンはサポートされません。<br/><br/>マシン名は 1 - 63 文字 (英字、数字、ハイフン) にする必要があります。 文字または数字で始まり、文字または数字で終わる必要があります。 Azure 上の名前は、マシンのレプリケーションを有効にした後で変更できます。<br/><br/>ソース VM に NIC チーミングがある場合、Azure へのフェールオーバー後に単一の NIC に変換されます。<br/><br/>保護対象の仮想マシンに iSCSI ディスクがある場合、VM が Azure にフェールオーバーしたときに、Site Recovery は保護対象の VM の iSCSI ディスクを VHD ファイルに変換します。 Azure VM から iSCSI ターゲットに到達できる場合、両者の間で接続が行われ、iSCSI ターゲットは実質的に 2 つのディスク (Azure VM 上の VHD ディスクとソースの iSCSI ディスク) を認識することになります。 この場合、Azure VM 上の iSCSI ターゲットを切断する必要があります。
**Windows を実行している VM** | Windows マシンは、[サポートされている](site-recovery-support-matrix.md#support-for-replicated-machines) 64 ビット オペレーティング システムを実行している必要があります。<br/><br/> オペレーティング システムは C:\ ドライブにインストールする必要があります。<br/><br/> OS ディスクは、ダイナミック ディスクではなく、Windows ベーシック ディスクである必要があります。 データ ディスクはダイナミックでもかまいません。<br/><br/> Site Recovery は、RDM ディスクを使用する VM をサポートします。 フェールバック時に、元のソース VM と RDM ディスクを使用できる場合、Site Recovery は RDM ディスクを再利用します。 これらが使用できない場合は、フェールバック時に各ディスクの新しい VMDK ファイルが作成されます。
**Linux を実行している VM** | Linux マシンは、[サポートされているオペレーティング システム](site-recovery-support-matrix.md#support-for-replicated-machines)を実行している必要があります。<br/><br/> 保護対象のマシン上の /etc/hosts ファイルには、ローカル ホスト名をすべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが含まれている必要があります。<br/><br/> ホスト名、マウント ポイント、デバイス名、および Linux システム パスとファイル名 (例: /etc/; /usr) には英語のみ使用できます。<br/><br/> ストレージが[サポートされている](site-recovery-support-matrix.md#support-for-replicated-machines)必要があります。<br/><br/> Site Recovery は、RDM ディスクを使用する VM をサポートします。  Linux のフェールバックの場合、Site Recovery は RDM ディスクを再利用しません。 代わりに、対応する各 RDM ディスク用に新しい VMDK ファイルを作成します。<br/><br/> VMware の VM の構成パラメーターで disk.enableUUID=true が設定されていることを確認します。 このエントリが存在しない場合は作成します。 これは、VMDK が適切にマウントされるように、一貫性のある UUID を提供するために必要となります。 また、この設定を追加すると、フェールバック時に、完全なレプリケーションではなく、差分変更だけをオンプレミスに転送できます。



## <a name="physical-servers-replication-requirements-to-azure"></a>物理サーバーのレプリケーションの要件 (Azure)

**コンポーネント** | **要件**
--- | ---
**構成サーバー** | Windows Server 2012 R2 を実行しているオンプレミスの物理マシンまたは仮想マシン。 オンプレミスの Site Recovery コンポーネントはすべてこのマシンにインストールします。<br/><br/> 物理マシンのレプリケーションでは、このマシンを物理サーバーにすることができます。<br/><br/>  構成サーバーがインターネットにアクセスできる必要があります。 次のような発信アクセスが必要です。<br/><br/> Site Recovery コンポーネントのセットアップ中に実行される HTTP 80 での一時的なアクセス (MySQL をダウンロードするため)<br/><br/>レプリケーション管理のための HTTPS 443 での継続的な発信アクセス<br/><br/>レプリケーション トラフィックのための HTTPS 9443 での継続的な発信アクセス (このポートは変更可能)<br/><br/> MySQL のダウンロード用に ``http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi`` を許可します。<br/><br/> サーバーは、[これらの URL](#requirements-for-url-access) にもアクセスできる必要があります。
**フェールバック** | 物理サーバーをレプリケートする場合でも、Azure からのフェールバックは常に VMware VM に対して実行されます。 構成サーバーを VMware VM としてデプロイしていない場合は、フェールバックを行う前に、フェールバックのトラフィックを受信する VMware VM をセットアップする必要があります。

### <a name="physical-machine-requirements-to-azure"></a>物理マシンの要件 (Azure)

**コンポーネント** | **要件**
--- | ---
**Windows を実行している物理サーバー** | Windows マシンは、[サポートされている](site-recovery-support-matrix.md#support-for-replicated-machines) 64 ビット オペレーティング システムを実行している必要があります。<br/><br/> オペレーティング システムは C:\ ドライブにインストールする必要があります。<br/><br/> OS ディスクは、ダイナミック ディスクではなく、Windows ベーシック ディスクである必要があります。 データ ディスクはダイナミックでもかまいません。<br/><br/> Site Recovery は、RDM ディスクを使用する VM をサポートします。 フェールバック時に、元のソース VM と RDM ディスクを使用できる場合、Site Recovery は RDM ディスクを再利用します。 これらが使用できない場合は、フェールバック時に各ディスクの新しい VMDK ファイルが作成されます。
**Linux を実行している物理サーバー** | Linux マシンは、[サポートされているオペレーティング システム](site-recovery-support-matrix.md#support-for-replicated-machines)を実行している必要があります。<br/><br/> 保護対象のマシン上の /etc/hosts ファイルには、ローカル ホスト名をすべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが含まれている必要があります。<br/><br/> ホスト名、マウント ポイント、デバイス名、および Linux システム パスとファイル名 (例: /etc/; /usr) には英語のみ使用できます。<br/><br/> ストレージが[サポートされている](site-recovery-support-matrix.md#support-for-replicated-machines)必要があります。

## <a name="hyper-v-replication-requirements-to-azure"></a>Hyper-V のレプリケーションの要件 (Azure)

**コンポーネント** | **要件**
--- | ---
**VMM (省略可能)** | 必要に応じて、VMM クラウドで管理されている Hyper-V ホスト上の VM をレプリケートすることもできます。<br/><br/> VMM を使用していない場合は、1 つ以上の Hyper-V ホストまたはクラスターを Hyper-V サイトにまとめ、サイトのレプリケーションを設定します。<br/><br/> VMM を使用している場合は、System Center 2012 R2 で実行されている必要があります。<br/><br/> VMM を使用している場合、各 VMM サーバーに 1 つ以上のクラウドが構成されている必要があります。 クラウドには 1 つ以上の VMM ホスト グループが含まれ、ホスト グループごとに 1 つ以上の Hyper-V ホスト サーバーまたはクラスターが含まれている必要があります。<br/><br/> VMM サーバーはインターネットに接続され、[必要な URL](#requirements-for-url-access) にアクセスできる必要があります。<br/><br/> VMM サーバーに IP アドレスベースのファイアウォール規則がある場合、規則で Azure との通信を許可していることを確認します。<br/><br/> [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。<br/><br/> ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します。
**Hyper-V ホスト** | Hyper-V ホスト サーバーは、Hyper-V ロールが割り当てられた **Windows Server 2012 R2** 以降か、**Microsoft Hyper-V Server 2012 R2** を実行し、最新の更新プログラムがインストールされている必要があります。<br/><br/> Hyper-V サーバーに 1 つ以上の VM が含まれている必要があります。<br/><br/>Hyper-V サーバーは、直接、またはプロキシを経由して、インターネットに接続します。<br/><br/>Hyper-V サーバーには、記事 [2961977](https://support.microsoft.com/kb/2961977) で説明されている修正プログラムをインストールする必要があります。<br/><br/>Hyper-V ホスト サーバーは、Azure へのデータ レプリケーションを実行するためにインターネットにアクセスできる必要があり、[必要な URL](#requirements-for-url-access) にもアクセスできる必要があります。 また、[Azure データセンターの IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。<br/><br/> ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します。<br/><br/> Hyper-V ホストが VMM クラウド内にある場合は、[KB 2961977](https://support.microsoft.com/en-us/kb/2961977) に記載された修正プログラムを必ずインストールします。

### <a name="hyper-v-vm-requirements-to-azure"></a>Hyper-V VM の要件 (Azure)

**コンポーネント** | **要件**
--- | ---
**VM コンプライアンス** |VM をフェールオーバーする前に、Azure VM に割り当てられる名前が [Azure の前提条件](site-recovery-best-practices.md#azure-virtual-machine-requirements)に準拠していることを確認してください。 名前は、VM のレプリケーションを有効にした後で変更できます。
**ディスク** | 保護対象のマシン上の個々のディスク容量が 1023 GB 以下である必要があります。 VM は最大 64 個のディスク (従って最大 64 TB) に対応できます。<br/><br/> 共有ディスク ゲスト クラスターはサポートされていません。
**UEFI** | Unified Extensible Firmware Interface (UEFI) ブート/拡張ファームウェア インターフェイス (EFI) ブートはサポートされていません。
**NIC チーミング** | ソース VM に NIC チーミングがある場合、Azure へのフェールオーバー後に単一の NIC に変換されます。
**Linux 静的** | 静的 IP アドレスを持ち、Linux を実行している Hyper-V VM は保護できません。



## <a name="vmwarephysical-servers-replication-requirements-secondary-site"></a>VMware/物理サーバーのレプリケーションの要件 (セカンダリ サイト)

このシナリオは InMage 製品によって処理されます。InMage 製品は、VMware VM または物理サーバーをセカンダリ サイトにレプリケートする場合に Site Recovery コンテナーからダウンロードできます。 このシナリオは、Azure Portal では使用できないことに注意してください。

**コンポーネント** | **要件**
--- | ---
**プライマリ サイト** | キャッシュ、圧縮、データ最適化を処理するプロセス サーバーが必要です。 このサーバーは、レプリケートする各マシンに必要なユニファイド エージェントのプッシュ インストールも処理します。<br/><br/> VMware VM をレプリケートする場合、1 つ以上の VMware ESX/ESXi サーバーと、必要に応じて VMware vCenter サーバーが必要です。
**セカンダリ サイト** | 環境を構成して管理するために、セカンダリ サイトで構成サーバーをセットアップする必要があります。<br/><br/> レプリケートされたデータを保持するために、マスター ターゲット サーバーがセカンダリ サイトにインストールされます。<br/><br/> vContinuum サーバーは、マスター ターゲット サーバーに既定でインストールされ、環境の管理と監視を行うためのコンソールが提供されます。

### <a name="vmware-vmphysical-machine-requirements-secondary-site"></a>VMware VM/物理マシンの要件 (セカンダリ サイト)

[InMage サポート マトリックス](https://aka.ms/asr-scout-cm)の要件を確認してください。


## <a name="hyper-v-replication-requirements-secondary-site"></a>Hyper-V のレプリケーションの要件 (セカンダリ サイト)

**コンポーネント** | **要件**
--- | ---
**VMM** | Hyper-V VM をセカンダリ サイトにレプリケートするには、Hyper-V ホストが System Center VMM クラウドで管理されている必要があります。 <br/><br/> VMM は、最新の更新プログラムが適用された System Center 2012 SP1 以降を実行している必要があります。<br/><br/> プライマリ サイトとセカンダリ サイトに VMM サーバーを 個別にデプロイすることをお勧めします。 [同じ VMM サーバー上の異なるクラウド](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-single-vmm)間でレプリケートできますが、このシナリオでは手動構成が必要になります。<br/><br/> VMM サーバーはインターネットに接続され、[必要な URL](#requirements-for-url-access) にアクセスできる必要があります。<br/><br/> VMM サーバーに IP アドレスベースのファイアウォール規則がある場合、規則で Azure との通信を許可していることを確認します。<br/><br/> [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。<br/><br/> ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します。
**Hyper-V** | Hyper-V サーバーは、Hyper-V ロールが適用された Windows Server 2012 以降を実行し、最新の更新プログラムがインストールされている必要があります。<br/><br/> Hyper-V サーバーに 1 つ以上の VM が含まれている必要があります。<br/><br/> Hyper-V ホストは、プライマリおよびセカンダリ VMM サーバー上のホスト グループに配置する必要があります。<br/><br/> Windows Server 2012 R2 上のクラスターで Hyper-V を実行する場合は、更新プログラム [2961977](https://support.microsoft.com/kb/2961977) をインストールする必要があります。 Hyper-V クラスターが Windows Server 2012 上にある場合、静的 IP アドレス ベースのクラスターが存在すると、クラスター ブローカーは自動的には作成されません。 [こちら](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)で詳細を確認して手動で構成してください。

### <a name="hyper-v-vm-requirements-secondary-site"></a>Hyper-V VM の要件 (セカンダリ サイト)

**コンポーネント** | **詳細**
--- | ---
**VMM クラウド** | VM は、VMM クラウド内の Hyper-V ホスト上に配置する必要があります。






## <a name="url-access-requirements"></a>URL アクセスの要件

これらの URL は、VMware、VMM、および Hyper-V ホスト サーバーから入手できます。

**URL** | **VMM から VMM** | **VMM から Azure** | **Hyper-V から Azure** | **VMware から Azure** 
--- | --- | --- | --- | ---
``*.accesscontrol.windows.net`` | ALLOW | 許可 | 許可 | 許可
``*.backup.windowsazure.com`` | 必要なし | 許可 | 許可 | 許可
``*.hypervrecoverymanager.windowsazure.com`` | 許可 | 許可 | 許可 | 許可
``*.store.core.windows.net`` | 許可 | 許可 | 許可 | 許可
``*.blob.core.windows.net`` | 必要なし | 許可 | 許可 | 許可
``https://www.msftncsi.com/ncsi.txt`` | 許可 | 許可 | 許可 | 許可
``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | 必要なし | 必要なし | 必要なし | SQL のダウンロードを許可
``time.windows.com`` | ALLOW | 許可 | 許可 | 許可
``time.nist.gov`` | 許可 | 許可 | 許可 | ALLOW

## <a name="failover-requirements"></a>フェールオーバーの要件

**シナリオ** | **Test** | **計画されている** | **計画されていない**
--- | --- | --- | ---
VMware から Azure | サポートされています | サポートされていません | サポートされています
物理から Azure | サポートされています | サポートされていません | サポートされています
Hyper-V (VMM) から Azure | サポートされています | サポートされています | サポートされています
Hyper-V (VMM なし) から Azure | サポートされています | サポートされています | サポートされていません
Hyper-V (プライマリ VMM/クラウド) からセカンダリ | サポートされています | サポートされています | サポートされています

## <a name="failback-requirements"></a>フェールバックの要件

**シナリオ** | **Test** | **計画されている** | **計画されていない**
--- | --- | --- | ---
**Azure から VMware** | サポートされていません | サポートされていません | サポートされています
**Azure から物理** | サポートされていません | サポートされていません | VMware のみサポートされています
**Azure から VMM** | サポートされていません | サポートされています | サポートされていません
**Azure から Hyper-V (VMM なし)** | サポートされていません | サポートされています | サポートされていません
**Hyper-V (セカンダリ VMM/クラウド) からプライマリ** | サポートされています | サポートされています | サポートされています

### <a name="failback-from-azure-to-vmware-vms"></a>Azure から VMware VM へのフェールバック

**コンポーネント** | **詳細**
--- | ---
**Azure 上の一時的なプロセス サーバー** | フェールオーバー後に Azure からフェールバックする場合は、Azure からのレプリケーションを処理するために、プロセス サーバーとして構成された Azure VM をセットアップする必要があります。 この VM は、フェールバックの完了後に削除できます。
**VPN 接続** | フェールバックするには、Azure ネットワークからオンプレミス サイトへの VPN 接続 (または Azure ExpressRoute) をセットアップする必要があります。
**別のオンプレミスのマスター ターゲット サーバー** | オンプレミスのマスター ターゲット サーバーがフェールバックを処理します。 マスター ターゲット サーバーは、既定では管理サーバーにインストールされますが、大量のトラフィックをフェールバックする場合は、この目的用にオンプレミスのマスター ターゲット サーバーをセットアップする必要があります。



### <a name="failback-from-azure-to-physical-machines"></a>Azure から物理マシンへのフェールバック

**コンポーネント** | **詳細**
--- | ---
**物理マシン間のフェールバック** | これはサポートされていません。 つまり、物理サーバーを Azure にフェールオーバーした後でフェールバックする場合は、VMware VM にフェールバックする必要があります。 物理サーバーにはフェールバックできません。
**VM** | フェールバック先となるオンプレミスの VMware VM が必要です。
**構成サーバー** | オンプレミスの構成サーバーを VMware VM としてデプロイしていない場合は、別のマスター ターゲット サーバーを VMware VM としてセットアップする必要があります。 これは、マスター ターゲット サーバーが VMware ストレージとのやり取りと接続を通じてディスクを VMware VM に復元する必要があるために必要です。



## <a name="deployment-optimization"></a>デプロイの最適化

次のヒントを使用して、デプロイの最適化およびスケーリングを行ってください。

- **オペレーティング システムのボリューム サイズ**: 仮想マシンを Azure にレプリケートする場合、オペレーティング システムのボリュームは、1 TB より小さくする必要があります。 これよりもボリュームが大きい場合は、デプロイを開始する前に、手動で別のディスクに移動することができます。
- **データ ディスクのサイズ**: Azure にレプリケートする場合、仮想マシンに最大で 64 個のデータ ディスクを備えることができます。それぞれのディスクのサイズは最大 1 TB です。 これにより、最大 64 TB の仮想マシンのレプリケートとフェールオーバーを効率的に行うことができます。
- **復旧計画の制限**: Site Recovery は、何千もの仮想マシンを処理するようにスケールアップできます。 復旧計画は、ひとまとまりでフェールオーバーする必要があるアプリケーション向けのモデルとして設計されているため、復旧計画で用いるマシン数は 50 個までに制限されています。
- **Azure サービスの制限**: すべての Azure サブスクリプションには、コアやクラウド サービスなどに一連の既定の制限が設定されています。サブスクリプション内のリソースの可用性については、テスト フェールオーバーを実行して検証することをお勧めします。 これらの制限は、Azure サポートを使用して変更することができます。
- **キャパシティ プランニング**: Site Recovery の [キャパシティ プランニング](site-recovery-capacity-planner.md) のページをご覧ください。
- **レプリケーションの帯域幅**: レプリケーションの帯域幅が不足する場合は、次の点に注意してください。
- **ExpressRoute**: Site Recovery は、Azure ExpressRoute や、Riverbed などの WAN オプティマイザーと連携します。 [こちら](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) を参照してください。
- **レプリケーション トラフィック**: Site Recovery は、VHD 全体ではなく、データ ブロックのみを使用してスマート初期レプリケーションを実行します。 レプリケーション進行中にレプリケートされるのは、変更箇所のみです。
- **ネットワーク トラフィック**: 送信先 IP アドレスとポートに基づくポリシーを用いて [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx) を設定することで、レプリケーションに使用されるネットワーク トラフィックを制御できます。  さらに、Azure Backup エージェントを使用して、Azure Site Recovery にレプリケートする場合には、そのエージェントの調整を構成できます。 詳細については、[こちら](https://support.microsoft.com/kb/3056159)を参照してください。
- **RTO**: Site Recovery を使用した場合に予期できる回復時刻の目標 (RTO) を測定するには、テスト フェールオーバーを実行し、Site Recovery ジョブを表示して、操作を完了するためにかかる時間を分析することをお勧めします。 Azure にフェールオーバーする場合、最適な RTO を実現するには、Site Recovery を Azure Automation と復旧計画に統合して、すべての手動操作を自動化することをお勧めします。
- **RPO**: Azure にレプリケートするときには、Site Recovery は、ほぼ同期の回復ポイントの目標 (RPO) をサポートします。 これは、データセンターと Azure の間に十分な帯域幅があることを前提としています。



## <a name="next-steps"></a>次のステップ
デプロイに関する一般的な要件を確認した後、詳細な前提条件を読み、シナリオを展開します。

* [VMWare 仮想マシンを Azure にレプリケート](site-recovery-vmware-to-azure.md)
* [物理サーバーを Azure にレプリケート](site-recovery-vmware-to-azure.md)
* [VMM クラウドの Hyper-V サーバーを Azure にレプリケート](site-recovery-vmm-to-azure.md)
* [Hyper-V 仮想マシン (VMM を不使用) を Azure にレプリケート](site-recovery-hyper-v-site-to-azure.md)
* [Hyper-V VM をセカンダリ サイトにレプリケート](site-recovery-vmm-to-vmm.md)
* [Hyper-V VM をセカンダリ サイトにレプリケート (SAN を使用)](site-recovery-vmm-san.md)
* [Hyper-V VM をレプリケート (単一の VMM サーバーを使用)](site-recovery-single-vmm.md)



<!--HONumber=Dec16_HO1-->



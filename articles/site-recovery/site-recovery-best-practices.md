---
title: "Site Recovery のデプロイの準備 | Microsoft Docs"
description: "この記事では、Azure Site Recovery を使用してレプリケーションをデプロイする準備を整える方法について説明します。"
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
ms.date: 02/06/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 5ed46a6bd8931d3b8829a053ace12fd219f108c4
ms.openlocfilehash: 6430ae9a97888ef993cd3dd35eea765fe95bb43f

---

# <a name="prepare-for-azure-site-recovery-deployment"></a>Azure Site Recovery のデプロイの準備

組織には、予定されたダウンタイムおよび予定外のダウンタイム時にアプリ、ワークロード、およびデータの実行と利用可能な状態を維持し、できるだけ早く通常の動作状態に復旧させる方法を決定する BCDR の戦略が必要です。 Site Recovery とは、クラウド (Azure) またはセカンダリ データセンターへのオンプレミスの物理サーバーおよび仮想マシンのレプリケーションを統制することで BCDR 戦略を支援する Azure サービスです。 プライマリ ロケーションで障害が発生した場合は、セカンダリ ロケーションにフェールオーバーしてアプリとワークロードの可用性を維持します。 プライマリの場所が通常の動作に戻ると、その場所にフェールバックします。 詳細については、「 [Site Recovery とは](site-recovery-overview.md)

この記事では、各 Site Recovery レプリケーション シナリオの必須条件を要約し、詳細なデプロイのチュートリアルへのリンクを示します。  

コメントはこの記事の末尾に投稿し、技術的な質問は [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。



## <a name="azure-deployment-models"></a>Azure のデプロイメント モデル

Azure には、リソースの作成と操作に関して、Azure Resource Manager モデルとクラシック モデルという 2 種類の[デプロイメント モデル](../azure-resource-manager/resource-manager-deployment-model.md)があります。 また、ポータルも 2 つあります。クラシック デプロイメント モデルをサポートする [Azure クラシック ポータル](https://manage.windowsazure.com/)と、両方のデプロイメント モデルをサポートする [Azure Portal](https://ms.portal.azure.com/) です。

新しい Site Recovery シナリオは [Azure Portal](https://portal.azure.com) でデプロイする必要があります。 このポータルは、新機能と強化されたデプロイ エクスペリエンスを提供します。 従来のポータルでは、資格情報コンテナーを管理することはできますが、新しい資格情報コンテナーを作成することはできません。


## <a name="deployment-scenarios"></a>デプロイメント シナリオ

Site Recovery でレプリケートできるシナリオは次のとおりです。

| **デプロイ** | **詳細** | **Azure Portal** | **クラシック ポータル** | **PowerShell デプロイメント** |
| --- | --- | --- | --- | --- |
| [VMware から Azure](site-recovery-vmware-to-azure.md) | オンプレミス VMware VM の Azure ストレージへのレプリケート | リソース マネージャー、または従来のストレージとネットワークを使用します。 リソース マネージャー ベースの VM または従来の VM にフェールオーバーします。 | メンテナンス モードのみ。 新しい資格情報コンテナーを作成することはできません。 | 現在、サポートされていません。 |
| [物理サーバーを Azure に](site-recovery-vmware-to-vmware.md) | Windows/Linux の物理サーバーを Azure ストレージにレプリケートします | リソース マネージャー、または従来のストレージとネットワークを使用します。 リソース マネージャー ベースの VM または従来の VM にフェールオーバーします。 | メンテナンス モードのみ。 新しい資格情報コンテナーを作成することはできません。  |
| [VMM クラウドの Hyper-V VM を Azure に](site-recovery-vmm-to-azure.md) | VMM クラウドのオンプレミス Hyper-V VM を Azure Portal にレプリケートします。<br/><br/> | リソース マネージャー、または従来のストレージとネットワークを使用します。 リソース マネージャー ベースの VM または従来の VM にフェールオーバーします。   | メンテナンス モードのみ。 新しい資格情報コンテナーを作成することはできません。 | サポートされています |
| [Hyper-V VM を Azure に (VMM なし)](site-recovery-hyper-v-site-to-azure.md) | オンプレミス Hyper-V VM を Azure Portal にレプリケートします。 | リソース マネージャー、または従来のストレージとネットワークを使用します。 リソース マネージャー ベースの VM または従来の VM にフェールオーバーします。 | メンテナンス モードのみ。 新しい資格情報コンテナーを作成することはできません。 | サポートされています |
| VMware VM/物理サーバーからセカンダリ サイトへ (site-recovery-vmware-to-vmware.md) | VMware VM または Windows/Linux の物理サーバーをセカンダリ サイトにレプリケートします。<br/><br/> InMage Scout [ユーザー ガイドをダウンロード](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf)します。 | Azure Portal では使用できません | Site Recovery コンテナーから InMage Scout をダウンロードします。 | サポートされていません |
| [Hyper-V VM をセカンダリ サイトに](site-recovery-vmm-to-vmm.md) | VMM クラウドの Hyper-V VM をセカンダリ クラウドにレプリケートします  | レプリケーションでは、標準の Hyper-V レプリカを使用します。 SAN はサポートされていません。 | Hyper-V レプリカまたは [SAN レプリケーション](site-recovery-vmm-san.md) を使用したレプリケート | サポートされています |

## <a name="requirements-for-replication-to-azure"></a>Azure へのレプリケーションの要件

| **要件** | **詳細** |
| --- | --- |
| **Azure アカウント** | [Microsoft Azure](http://azure.microsoft.com/) アカウント。<br/><br/> アカウントがなくても、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。 [こちら](https://azure.microsoft.com/pricing/details/site-recovery/) をご覧ください。 |
| **Azure Storage** | レプリケートされたデータは Azure Storage に格納され、フェールオーバーが発生すると Azure VM が作成されます。 [Azure ストレージ アカウント](../storage/storage-introduction.md)が必要です。<br/><br/>Azure Portal で [GRS](../storage/storage-redundancy.md#geo-redundant-storage) または LRS ストレージを使用します。 従来のポータルでは、GRS がのみサポートされます。<br/><br/> Azure Portal で VMware VM または物理サーバーをレプリケートする場合は、Premium ストレージがサポートされています。<br/><br/>  |
| **Azure ネットワーク** | Azure VM の接続先となる Azure ネットワークが必要です。 <br/><br/> Azure Portal では、従来のネットワークまたは Resource Manager ネットワークを使用できます。<br/><br/> ネットワークは、コンテナーと同じリージョンにある必要があります。<br/><br/> VMM から Azure にレプリケートする場合、VMM VM ネットワークと Azure ネットワーク間の[ネットワーク マッピング](site-recovery-network-mapping.md)を設定して、Azure VM がフェールオーバー後に適切なネットワークに接続できるようします。 |
| **オンプレミスの** |**VMware VM**: Site Recovery コンポーネントが実行されているオンプレミスのコンピューター。 また、VMware vSphere ホスト/vCenter サーバー、およびレプリケートする VM が必要です。 [詳細情報](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites)<br/><br/> **物理サーバー**: Site Recovery コンポーネントが実行されているオンプレミスのコンピューター、およびレプリケートする物理サーバー。 [詳細情報](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites) Azure から物理サーバーを[フェールバック](site-recovery-failback-azure-to-vmware.md)する場合は、そのための VMware インフラストラクチャが必要になります。<br/><br/> **Hyper-V VM**: VMM サーバー、およびレプリケートする VM を格納している Hyper-V ホスト。 [詳細情報](site-recovery-vmm-to-azure.md#on-premises-prerequisites)<br/><br/> VMM なしの Hyper-V VM をレプリケートする場合は、Hyper-V ホストのみが必要です。 [詳細情報](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites) |
| **保護されたマシン** | Azure にレプリケートする保護されたマシンは、以下で説明する [Azure の前提条件](#azure-virtual-machine-requirements) に従う必要があります。 |

## <a name="requirements-for-replication-to-a-secondary-site"></a>セカンダリ サイトへのレプリケーションの要件

| **要件** | **詳細** |
| --- | --- |
| **Azure** | [Microsoft Azure](http://azure.microsoft.com/) アカウント。<br/><br/> アカウントがなくても、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。 [こちら](https://azure.microsoft.com/pricing/details/site-recovery/) をご覧ください。 |
| **オンプレミスの** |**VMware VM**: プライマリ サイトには、レプリケーション データをキャッシュ、圧縮、および暗号化するためのプロセス サーバーが必要です。 セカンダリ サイトでは、デプロイメントを管理および監視するための構成サーバー、およびマスター ターゲット サーバーが必要です。 また、管理を容易にする vContinuum サーバーもお勧めします。 さらに、1 台以上の VMware vSphere ホストと、必要に応じて vCenter サーバーが必要です。 [詳細情報](site-recovery-vmware-to-vmware.md)<br/><br/> **VMM クラウドの Hyper-V VM**: VMM サーバーと、レプリケートする VM が格納されている Hyper-V ホストが必要です。 [詳細情報](site-recovery-vmm-to-vmm.md#on-premises-prerequisites) |
| **ネットワーク (VMM から VMM へ)** | VMM から VMM にレプリケートする場合、[ネットワーク マッピング](site-recovery-network-mapping.md) を設定して、フェールオーバー後にレプリカ VM が適切なネットワークに確実に接続され、Hyper-V ホストに最適に配置されるようにします。 |

## <a name="url-access"></a>URL アクセス

これらの URL は、VMware、VMM、および Hyper-V ホスト サーバーから入手できます。

**URL** | **VMM から VMM** | **VMM から Azure** | **Hyper-V から Azure** | **VMware から Azure** |
|--- | --- | --- | --- | ---
``*.accesscontrol.windows.net`` | ALLOW | 許可 | 許可 | 許可
``*.backup.windowsazure.com`` | 必要なし | 許可 | 許可 | 許可
``*.hypervrecoverymanager.windowsazure.com`` | 許可 | 許可 | 許可 | 許可
``*.store.core.windows.net`` | 許可 | 許可 | 許可 | 許可
``*.blob.core.windows.net`` | 必要なし | 許可 | 許可 | 許可
``https://www.msftncsi.com/ncsi.txt`` | 許可 | 許可 | 許可 | 許可
``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | 必要なし | 必要なし | 必要なし | SQL のダウンロードを許可
``time.windows.com`` | ALLOW | 許可 | 許可 | 許可
``time.nist.gov`` | 許可 | 許可 | 許可 | ALLOW



## <a name="azure-virtual-machine-requirements"></a>Azure 仮想マシンの要件

Site Recovery をデプロイすると、Azure でサポートされた任意のオペレーティング システムを実行している仮想マシンまたは物理サーバーをレプリケートできます。 これには、Windows と Linux のほとんどのバージョンが含まれます。 レプリケートするオンプレミスの仮想マシンは Azure 要件を満たしている必要があります。

**機能** | **要件** | **詳細**
--- | --- | ---
**Hyper-V ホスト** | Windows Server 2012 R2 以降が実行されている必要があります。 | オペレーティング システムがサポートされていない場合は、前提条件の確認は失敗します。
**VMware ハイパーバイザー** | サポートされるオペレーティング システム | [要件の確認](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
**ゲスト オペレーティング システム** | Hyper-V から Azure へのレプリケーション: Site Recovery では、 [Azure でサポートされている](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)すべてのオペレーティング システムがサポートされます。 <br/><br/> VMware と物理サーバーのレプリケーション: Windows と Linux の [前提条件](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | サポートされていない場合、前提条件の確認は失敗します。
**ゲスト オペレーティング システムのアーキテクチャ** | 64 ビット | サポートされていない場合、前提条件の確認は失敗します。
**オペレーティング システムのディスク サイズ** | 最大 1023 GB | サポートされていない場合、前提条件の確認は失敗します。
**オペレーティング システムのディスク数** | 1 | サポートされていない場合、前提条件の確認は失敗します。
**データ ディスク数** | 16 以下 (最大値は、作成される仮想マシンのサイズの関数であり、16 = XL で求められます。 ) | サポートされていない場合、前提条件の確認は失敗します。
**データ ディスク VHD のサイズ** | 最大 1023 GB | サポートされていない場合、前提条件の確認は失敗します。
**ネットワーク アダプター** | 複数のアダプターがサポートされます。 |
**静的 IP アドレス** | サポートされています | プライマリ仮想マシンが静的 IP アドレスを使用している場合、Azure で作成される仮想マシンに静的 IP アドレスを指定できます。<br/><br/> **Hyper-V で実行されている Linux VM** の静的な IP アドレスはサポートされていません。
**iSCSI ディスク** | サポートされていません | サポートされていない場合、前提条件の確認は失敗します。
**共有 VHD** | サポートされていません | サポートされていない場合、前提条件の確認は失敗します。
**FC ディスク** | サポートされていません | サポートされていない場合、前提条件の確認は失敗します。
**ハード ディスク フォーマット** | VHD  <br/><br/> VHDX | VHDX は現在、Azure でサポートされていませんが、Azure にフェールオーバーするとき、Site Recovery が VHDX を VHD に自動的に変換します。 オンプレミスにフェールバックした場合、仮想マシンは引き続き VHDX 形式を使用します。
**Bitlocker** | サポートされていません | 仮想マシンを保護する前に BitLocker は無効にしてください。
**VM 名** | 1 ～ 63 文字で指定します。 名前に使用できるのは、英文字、数字、およびハイフンのみです。 名前の先頭と末尾は、英文字または数字である必要があります。 | Site Recovery で仮想マシンのプロパティの値を更新します。
**VM の種類** | 第 1 世代<br/><br/> 第 2 世代 - Windows | VHDX としてフォーマットされている 1 つまたは 2 つのデータ ボリュームが含まれる、300 GB 未満で OS ディスクタイプが基本の第 2 世代の VM はサポートされています。<br/><br/>に関するページを参照してください。 第 2 世代の Linux VM はサポートされていません。 [詳細情報](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) |

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
* [Hyper-V VM をレプリケート (単一の VMM サーバーを使用)](site-recovery-single-vmm.md)



<!--HONumber=Dec16_HO1-->



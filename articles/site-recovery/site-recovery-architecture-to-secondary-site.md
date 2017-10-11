---
title: "内部設置型の Azure Site Recovery でセカンダリ オンプレミス サイト作業へのレプリケーションへのコンピューター | Microsoft Docs"
description: "この記事では、コンポーネントとをレプリケートする内部設置型 Vm と Azure Site Recovery サービスがセカンダリ サイトに物理サーバーの場合に使用されるアーキテクチャの概要を示します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/29/2017
ms.author: raynew
ms.openlocfilehash: fca95c63964b955db7ddfbe53250702cc8af122e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="how-does-on-premises-machine-replication-to-a-secondary-site-work-in-site-recovery"></a>内部設置型の Site Recovery でセカンダリ サイト作業へのレプリケーションへのコンピューター

コンポーネントについて説明し、レプリケートするときに関連する処理でオンプレミスの仮想マシンと、Azure への物理サーバーを使用して、 [Azure Site Recovery](site-recovery-overview.md)サービス。

次は、セカンダリ オンプレミス サイトにレプリケートできます。
- 内部設置型 HYPER-V 仮想マシンの HYPER-V Vm と System Center Virtual Machine Manager (VMM) クラウドで管理されているスタンドアロン ホストの HYPER-V クラスターでします。
- 内部設置型 VMware 仮想マシンおよび Windows または Linux の物理サーバーです。 このシナリオでは、レプリケーションは Scout によって管理されます。

この記事の下部にある任意のコメントを投稿、 [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)です。

## <a name="replicate-hyper-v-vms-to-a-secondary-on-premises-site"></a>HYPER-V Vm をオンプレミスでセカンダリ サイトにレプリケートします。


### <a name="architectural-components"></a>アーキテクチャのコンポーネント

セカンダリ サイトへの HYPER-V 仮想マシンのレプリケーションに必要なものを次に示します。

**コンポーネント** | **場所** | **詳細**
--- | --- | ---
**Azure** | Microsoft アカウントが必要です。 |
**VMM サーバー** | プライマリ サイトの VMM サーバーと 1 つはセカンダリ サイトをお勧め | 各 VMM サーバーは、インターネットに接続する必要があります。<br/><br/> 各サーバーに少なくとも 1 つ VMM でのプライベート クラウド、HYPER-V の機能プロファイルのセットが必要です。<br/><br/> Azure Site Recovery プロバイダーをインストールするには、VMM サーバーにします。 プロバイダーは、調整し、インターネット経由でサイト回復サービスにレプリケーションを統制します。 プロバイダーと Azure 間の通信は、セキュリティで保護された暗号化です。
**HYPER-V サーバー** |  1 つ以上の HYPER-V ホスト サーバー、プライマリとセカンダリ VMM クラウドにします。<br/><br/> サーバーは、インターネットに接続する必要があります。<br/><br/> データは、LAN または Kerberos または証明書の認証を使用して、VPN 経由でプライマリとセカンダリ HYPER-V ホスト サーバー間でレプリケートされます。  
**HYPER-V Vm** | ソース HYPER-V ホスト サーバーにあります。 | ソース ホスト サーバーをレプリケートする場合に、少なくとも 1 つの VM が必要です。

### <a name="replication-process"></a>レプリケーション プロセス

1. Azure アカウントを設定します。
2. Site Recovery のレプリケーション サービスの資格情報コンテナーを作成してなどの資格情報コンテナー設定を構成します。

    - レプリケーション ソースとターゲット (プライマリとセカンダリ サイト)。
    - Azure Site Recovery Provider と Microsoft Azure Recovery Services エージェントのインストール。 プロバイダーは、VMM サーバー、および各 HYPER-V ホスト上のエージェントにインストールされます。
    - ソース VMM クラウドのレプリケーション ポリシーを作成します。 ポリシーは、クラウド内のホスト上にあるすべての Vm に適用されます。
    - HYPER-V Vm のレプリケーションを有効にするとします。 レプリケーション ポリシー設定に従って、初期レプリケーションが行われます。
4. データの変更が追跡され、デルタのレプリケーションが初期レプリケーション終了後、開始に変わります。 .Hrl ファイル内の項目の追跡された変更が保持されます。
5. 確認するテスト フェールオーバーを実行するすべてが動作します。

**図 1: VMM への VMM のレプリケーション**

![オンプレミスからオンプレミス](./media/site-recovery-components/arch-onprem-onprem.png)

### <a name="failover-and-failback-process"></a>フェールオーバーとフェールバック プロセス

1. 計画されたを実行することができますまたは計画されていない[フェールオーバー](site-recovery-failover.md)のオンプレミス サイトとの間です。 ソース Vm がシャット ダウンし、計画的フェールオーバーを実行する場合しないようにするためのデータが失われる。
2. 1 台のコンピューターをフェールオーバーしたり作成したり[復旧計画](site-recovery-create-recovery-plans.md)複数のマシンのフェールオーバーを調整するためです。
4. 場合は、セカンダリ サイトを計画外のフェールオーバーを実行するは、2 次拠点でフェールオーバー マシンは保護またはレプリケーションに対して有効にします。 フェールオーバー後に、計画的フェールオーバーを実行すると、2 次拠点のマシンは保護されます。
5. その後、レプリカ VM から、ワークロードへのアクセスを開始するフェールオーバーをコミットします。
6. ユーザーのプライマリ サイトを再度使用できる場合、セカンダリ サイトからプライマリにレプリケートするレプリケーションの反転を開始します。 レプリケーションの反転は、保護された状態に仮想マシンを表示しますが、セカンダリ データ センターがアクティブな場所です。
7. プライマリ サイトをアクティブな場所にもう一度を開始するセカンダリからプライマリ、計画されたフェールオーバー後に別のレプリケーションを反転します。




## <a name="replicate-vmware-vmsphysical-servers-to-a-secondary-site"></a>VMware の Vm/物理サーバーをセカンダリ サイトにレプリケートします。

これらのアーキテクチャのコンポーネントを使用して、InMage Scout を使用してセカンダリ サイトに VMware 仮想マシンまたは物理サーバーをレプリケートします。


### <a name="architectural-components"></a>アーキテクチャのコンポーネント

**コンポーネント** | **場所** | **詳細**
--- | --- | ---
**Azure** | InMage Scout です。 | InMage Scout を取得するには、Azure サブスクリプションが必要です。<br/><br/> Recovery Services コンテナーを作成した後は、InMage Scout をダウンロードして、展開を設定する最新の更新プログラムをインストールします。
**プロセス サーバー** | プライマリ サイトにあります。 | プロセス サーバー キャッシュを処理する、圧縮、およびデータの最適化を展開するとします。<br/><br/> また、保護するマシンに統合されたエージェントのプッシュ インストールを処理します。
**構成サーバー** | セカンダリ サイトにあります。 | 構成サーバーを管理、構成、および管理 web サイトまたは vContinuum コンソールを使用するかの展開を監視します。
**vContinuum サーバー** | 任意。 構成サーバーと同じ場所にインストールされます。 | 管理および保護された環境を監視コンソールを提供します。
**マスター ターゲット サーバー** | セカンダリ サイトにあります。 | マスター ターゲット サーバーでは、レプリケートされたデータを保持します。 プロセス サーバーからデータを受信して、セカンダリ サイトにレプリカ マシンが作成、および保有期間のデータ ポイントを保持します。<br/><br/> 必要なマスター ターゲット サーバーの数は、保護しているマシンの数によって異なります。<br/><br/> プライマリ サイトにフェールバックする場合は、必要があります、マスター ターゲット サーバーをあるすぎます。 統合エージェントはこのサーバーにインストールします。
**VMware ESX または ESXi および vCenter サーバー** |  Vm は、Esx/esxi ホストでホストされています。 ホストは、vCenter サーバーで管理します。 | VMware infrastructure VMware 仮想マシンをレプリケートする必要があります。
**Vm/物理サーバー** |  レプリケートする統合エージェントが VMware 仮想マシンと物理サーバーにインストールします。 | エージェントは、すべてのコンポーネント間の通信プロバイダーとして機能します。


### <a name="replication-process"></a>レプリケーション プロセス

1. (構成、process、マスター ターゲット) は、各サイトのコンポーネント サーバーを設定し、レプリケートするマシンに統合されたエージェントをインストールします。
2. 初期レプリケーションは後、は、各コンピューター上のエージェントは、プロセス サーバーにデルタ レプリケーションの変更を送信します。
3. プロセス サーバーは、データを最適化し、セカンダリ サイト上のマスター ターゲット サーバーに転送します。 構成サーバーでは、レプリケーション プロセスを管理します。

**図 2: VMware VMware のレプリケーション**

![VMware を VMware](./media/site-recovery-components/vmware-to-vmware.png)


## <a name="next-steps"></a>次のステップ

確認、[サポート マトリックス](site-recovery-support-matrix-to-sec-site.md)

---
title: "Azure からオンプレミスに再保護する方法 |Microsoft Docs"
description: "VM を Azure にフェールオーバーした後に、フェールバックを開始して VM をオンプレミスに戻すことができます。 フェールバックの前に再保護する方法の手順を説明します。"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 02/13/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: c4fb25880584add0832464d9049dc6c78059c48b
ms.openlocfilehash: 39e91dea775cf03f726db29f27f30142b69f9dfe
ms.lasthandoff: 02/22/2017


---
# <a name="reprotect-from-azure-to-on-premises"></a>Azure からオンプレミスへの再保護

## <a name="overview"></a>概要
この記事では、Azure からオンプレミス サイトへ Azure Virtual Machines を再保護する方法について説明します。 この記事の手順は、 [このチュートリアル](site-recovery-vmware-to-azure-classic.md)を使用して、VMware 仮想マシンまたは Windows/Linux 物理サーバーをオンプレミス サイトから Azure にフェールオーバーした後に、それらをフェールバックする準備ができたときに実行します。

再保護が完了し、保護された仮想マシンがレプリケートされた後に、VM のフェールバックを開始してオンプレミスに戻すことができます。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。

こちらに概要を簡単に説明したビデオが用意してありますのでご覧ください。
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

フェールバックの完全なプロセスについては、こちらでお読みください。

## <a name="pre-requisites"></a>前提条件
再保護を準備する際に対応または考慮する必要がある前提条件の手順を次に示します。

* フェールバックする VM が vCenter サーバーによって管理されている場合、vCenter サーバー上の VM の検出に必要な権限があることを確認する必要があります。 詳細については、[こちら](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)を参照してください。
* オンプレミスの VM 上にスナップショットがある場合、再保護は失敗します。 スナップショットを削除してから再保護に進むことができます。
* フェールバックを開始する前に、さらに&2; つのコンポーネントを作成する必要があります。
  * **プロセス サーバーを作成する**:  プロセス サーバーは、Azure の保護された VM からデータを受け取り、オンプレミスにデータを送信するために使用されます。 そのため、プロセス サーバーは、保護された VM との間の待機時間が短いネットワーク上にあることが必要です。 したがって、プロセス サーバーはオンプレミス (Express Route 接続を使用している場合)、または Azure (VPN を使用している場合) に配置できます。
  * **マスター ターゲット サーバーを作成する**: マスター ターゲット サーバーは、フェールバック データを受信します。 オンプレミスに作成した管理サーバーには、既定でマスター ターゲット サーバーがインストールされています。 ただし、フェールバック トラフィックの量によっては、フェールバック用に別のマスター ターゲット サーバーを作成する必要があります。 
        * [Linux VM には Linux のマスター ターゲット サーバーが必要です](site-recovery-how-to-install-linux-master-target.md)。 
        * Windows VM には、Windows のマスター ターゲットが必要です。 オンプレミスの PS+MT マシンを再利用してもかまいません。
* フェールバックを実行するときは、構成サーバーがオンプレミスに存在している必要があります。 フェールバック時に仮想マシンが構成サーバー データベースに存在していないと、フェールバックは失敗します。 サーバーを定期的にバックアップするようスケジュールを設定してください。 万一障害が発生した場合は、フェールバックが正常に機能するように、同じ IP アドレスで復元する必要があります。
* VMware でマスター ターゲット VM の構成パラメーターに disk.enableUUID=true が設定されていることを確認してください。 この行が存在しない場合は追加してください。 これは、一貫性のある UUID を VMDK に提供することで適切なマウントが実行されるようにするために必要です。
* **マスター ターゲット サーバーをストレージ vMotioned にすることはできません**。 そのようにした場合、フェールバックが失敗する可能性があります。 ディスクが利用可能にならないため、この VM は起動しません。
* マスター ターゲット サーバーに新しいドライブを追加する必要があります。 このドライブはリテンション ドライブと呼ばれます。 新しいディスクを追加し、ドライブをフォーマットします。
* マスター ターゲットには他にも、[再保護の前に行うマスター ターゲットの一般的なチェック事項](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-master-target)に記載した前提条件があります。


### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-to-replicate-data-back-to-on-premises"></a>オンプレミスにデータをレプリケート バックするために S2S VPN または ExpressRoute が必要な理由
オンプレミスから Azure へのレプリケーションを、インターネット、またはパブリック ピアリングを使用した ExpressRoute 経由で実行できる場合、再保護とフェールバックでは、データをレプリケートするために S2S VPN を設定する必要があります。 **ネットワークは、Azure のフェールオーバーされた VM からオンプレミスの構成サーバーに接続 (ping) できるように指定する必要があります**。 フェールオーバーされた VM の Azure ネットワークにプロセス サーバーをデプロイすることもできます。このプロセス サーバーも、オンプレミスの構成サーバーと通信できる必要があります。

### <a name="when-should-i-install-a-process-server-in-azure"></a>Azure にプロセス サーバーをインストールするタイミング


再保護の対象となる Azure VM は、レプリケーション データをプロセス サーバーに対して送信します。 Azure VM からプロセス サーバーにアクセスできるようにネットワークを設定する必要があります。

Azure でプロセス サーバーをデプロイしたり、フェールオーバーの際に使用した既存のプロセス サーバーを使用したりできます。 考慮すべき重要な点は、Azure VM からプロセス サーバーにデータを送信するときの待機時間です。 

* ExpressRoute を設定している場合は、オンプレミスの PS を使用してデータを送信できます。 これは、VM と PS の間の待機時間が短いためです。
    
    ![ExpressRoute のアーキテクチャ ダイアグラム](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



* ただし、使用できるのが S2S VPN のみの場合は、Azure でプロセス サーバーをデプロイすることをお勧めします。

    ![VPN のアーキテクチャ ダイアグラム](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


レプリケーションは、S2S VPN または、ExpressRoute ネットワークのプライベート ピアリングを介してのみ実行されることに注意してください。 そのネットワーク チャネルで十分な帯域幅を使用できることを確認してください。

Azure のプロセス サーバーのインストール方法について詳しくは、[こちら](site-recovery-vmware-setup-azure-ps-resource-manager.md)をご覧ください。

### <a name="what-are-the-different-ports-to-be-open-on-different-components-so-that-reprotect-can-work"></a>再保護が正常に機能するために各コンポーネントで開放されているべきポート

![すべてのポートのフェールオーバー/フェールバック](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

### <a name="which-master-target-server-to-use-for-reprotect"></a>再保護に使用するマスター ターゲット サーバー
マスター ターゲット サーバーは、プロセス サーバーからデータを受信してオンプレミスの VM の VMDK に書き込むために、オンプレミスである必要があります。 Windows VM を保護する場合、Windows マスター ターゲット サーバーが必要になります。ここで、オンプレミスの PS+MT <!-- !todo component --> を再利用できます。 Linux VM の場合は、追加の Linux マスター ターゲットをオンプレミスで設定する必要があります。


次のリンクをクリックして、マスター ターゲット サーバーのインストール手順を確認してください。

* [Windows マスター ターゲット サーバーをインストールする方法](site-recovery-vmware-to-azure.md#run-site-recovery-unified-setup)
* [Linux マスター ターゲット サーバーをインストールする方法](site-recovery-how-to-install-linux-master-target.md)


#### <a name="common-things-to-check-after-completing-installation-of-master-target"></a>マスター ターゲットのインストール完了後の一般的なチェック事項

* VM が vCenter サーバー上でオンプレミスに存在する場合、マスター ターゲット サーバーには、オンプレミスの VM の VMDK へのアクセス権が必要になります。 これは、レプリケートされたデータを VM のディスクに書き込むためです。 そのため、**オンプレミスの VM のデータ ストアは必ず、読み取りと書き込みアクセス権のある MT のホストにマウントする必要があります**。

* VM が vCenter サーバー上でオンプレミスに存在しない場合、再保護時に新しい VM を作成する必要があります。 MT を作成する ESX ホストでこの VM は作成されます。 そのため、希望するホストにフェールバック VM が作成されるように、ESX ホストは慎重に選択します。
    
* **マスター ターゲット サーバーをストレージ vMotioned にすることはできません**。 そのようにした場合、フェールバックが失敗する可能性があります。 ディスクが利用可能にならないため、この VM は起動しません。

* 既存の Windows マスター ターゲット サーバーに新しいドライブを追加する必要があります。 このドライブはリテンション ドライブと呼ばれます。 新しいディスクを追加し、ドライブをフォーマットします。 リテンション ドライブは、VM がオンプレミスにレプリケートされたときの特定の時点で停止するために使用されます。 リテンション ドライブの条件の一部を以下に示します。これらの条件を満たしていない場合、このドライブはマスター ターゲット サーバーに表示されません。
   
   * ボリュームを他の目的 (レプリケーションのターゲットなど) に使用することはできません。

   * ボリュームをロック モードにすることはできません。

   * ボリュームをキャッシュ ボリュームにすることはできません。 (そのボリュームに MT インストールが存在してはいけません。 PS + MT カスタム インストール ボリュームは、保持ボリュームの対象ではありません。 ここでは、インストール済みの PS + MT ボリュームが MT のキャッシュ ボリュームです。)

   * ボリューム ファイル システムの種類として、FAT および FAT32 は使用できません。

   * ボリュームの容量は&0; 以外である必要があります。

   * Windows の既定の保持ボリュームは R ボリュームです。

   * Linux の既定の保持ボリュームは、/mnt/retention/ です。

* フェールオーバーした Linux VM には Linux のマスター ターゲット サーバーが必要です。 フェールオーバーした Windows VM には Windows のマスター ターゲット サーバーが必要です。

* VMware ツールをマスター ターゲット サーバーにインストールします。 VMware ツールがない場合、MT の ESXi ホスト上のデータストアを検出できません。

* vCenter プロパティを使用して MT VM の disk.EnableUUID = True パラメーターを有効にします。 <!-- !todo Needs link. -->

* マスター ターゲットには、VMFS データストアが少なくとも&1; つ接続されている必要があります。 まったく存在しない場合、再保護ページに入力するデータストアが空となり先に進めません。

* マスター ターゲット サーバーのディスクにはスナップショットが一切存在してはいけません。 スナップショットが存在すると再保護/フェールバックが失敗します。

* 準仮想化 SCSI コントローラーを MT に割り当てることはできません。 LSI Logic コントローラー以外は使用できません。 LSI Logic コントローラーがないと再保護は失敗します。

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## <a name="steps-to-reprotect"></a>再保護の手順

オンプレミスのマスター ターゲット (Windows または [Linux](site-recovery-how-to-install-linux-master-target.md)) と Azure に[プロセス サーバー](site-recovery-vmware-setup-azure-ps-resource-manager.md)がインストールされていることを再保護の前に確認してください。

> [!NOTE]
> Azure で VM が起動した後、エージェントが構成サーバーに再度登録されるまでにしばらく時間がかかります (最大 15 分)。 この間は再保護が失敗し、エージェントがインストールされていないというエラー メッセージが表示されます。 数分待ってから再保護操作をやり直してください。
 
 

1. [コンテナー] の [レプリケートされたアイテム] で、フェールオーバーされた VM を選択し、右クリックして **[再保護]** をクリックします。 マシンをクリックし、コマンド ボタンから再保護を選択することもできます。
2. ブレードで、保護の方向として [Azure からオンプレミスへ] が既に選択されていることを確認します。
3. **[マスター ターゲット サーバー]** と **[プロセス サーバー]** で、オンプレミスのマスター ターゲット サーバーとプロセス サーバーを選択します。
4. オンプレミスのディスクの復旧先となる **データストア** を選択します。 このオプションは、オンプレミスの VM を削除し、新しいディスクを作成する必要がある場合に使用されます。 ディスクが既に存在する場合、このオプションは無視されますが、値は指定しておく必要があります。
5. リテンション ドライブを選択します。 
6. フェールバック ポリシーが自動的に選択されます。
7. **[OK]** をクリックして再保護を開始すると、Azure からオンプレミス サイトに VM をレプリケートするジョブが開始されます。 進行状況は **[ジョブ]** タブで追跡できます。

別の場所に復元する場合 (オンプレミスの VM が削除されているとき) は、マスター ターゲット サーバー用に構成したリテンション ドライブとデータ ストアを選択します。 オンプレミス サイトにフェールバックした場合、フェールバック保護計画の VMware VM はマスター ターゲット サーバーと同じデータ ストアを使用し、新しい VM が vCenter に作成されます。 Azure VM を既存のオンプレミスの VM に復旧する場合、マスター ターゲット サーバーの ESXi ホストに対する読み取り/書き込みアクセス権のあるオンプレミスの VM のデータストアをマウントする必要があります。
    ![](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

また、復旧計画レベルで再保護を実行することもできます。 レプリケーション グループがある場合、グループを再保護するには、復旧計画を使用する必要があります。 復旧計画を使用した再保護では、保護対象のすべてのマシンに上記の値を指定する必要があります。

> [!NOTE]
> レプリケーション グループは、同じマスター ターゲットを使用して保護する必要があります。 別のマスター ターゲット サーバーを使用して保護されている場合、共通の時点を取得できません。
 

正常に再保護できると、VM は保護された状態になります。

## <a name="next-steps"></a>次のステップ

VM が保護された状態になると、フェールバックを開始できます。 フェールバックによって Azure の VM がシャットダウンされ、オンプレミスの VM が起動されます。 そのため、アプリケーションに短時間のダウンタイムが発生します。 したがって、アプリケーションがダウンタイムに対応できるように、フェールバックの時刻を選択します。

[VM のフェールバックを開始する手順](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-failback)

## <a name="common-issues"></a>一般的な問題 

* テンプレートを使用して仮想マシンを作成した場合、各 VM のディスクに一意の UUID が割り当てられていることを、保護する前に確認してください。 オンプレミスの VM とマスター ターゲットとで UUID が (どちらも同じテンプレートから作成したために) 衝突した場合、再保護が失敗します。 同じテンプレートから作成したものではない別のマスター ターゲットをデプロイする必要があります。
* 読み取り専用ユーザー vCenter の検出を実行し、仮想マシンを保護すると、適切に動作し、フェールオーバーが実行されます。 再保護中は、データストアが検出できないため、フェールオーバーは失敗します。 症状としては、再保護中はデータストアの一覧が表示されません。 この問題を解決するには、適切な権限を持つアカウントを使用して vCenter 資格情報を更新し、ジョブを再試行します。 詳細については、「[Azure Site Recovery を使用して VMware 仮想マシンと物理サーバーを Azure にレプリケートする](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)」を参照してください。
* Linux VM をフェールバックしてオンプレミスで実行すると、ネットワーク マネージャー パッケージがマシンからアンインストールされていることを確認できます。 このアンインストールは、Azure で VM が 復旧されるときに、ネットワーク マネージャー パッケージが削除されるために発生します。
* Linux VM が静的 IP アドレスで構成されているときに Azure にフェールオーバーされると、IP アドレスは DHCP 経由で取得されます。 オンプレミスにフェールオーバーすると、VM は引き続き DHCP を使用して、IP アドレスを取得します。 必要であれば、手動でコンピューターにサインインし、IP アドレスを静的アドレスに設定し直します。 Windows VM は、その静的 IP を再取得できます。
* ESXi 5.5 無償版または vSphere 6 Hypervisor 無償版のいずれかを使用すると、フェールオーバーは成功しますが、フェールバックは失敗します。 フェールバックを有効にするには、いずれかのプログラムの評価ライセンスにアップグレードしてください。
* プロセス サーバーから構成サーバーにアクセスできない場合は、構成サーバー コンピューターのポート 443 で Telnet を実行して、構成サーバーへの接続を確認します。 プロセス サーバー コンピューターから構成サーバーに対して ping を試すこともできます。 プロセス サーバーが構成サーバーに接続されるときは、ハートビートも必要です。
* 代替の vCenter にフェールバックする場合は、新しい vCenter が検出されること、またマスター ターゲット サーバーも検出されることを確認してください。 典型的な症状は、データストアにアクセスできないか、**[再保護]** ダイアログに表示されないことです。
* 物理オンプレミス コンピューターとして保護されている WS2008R2SP1 コンピューターは、Azure からオンプレミスにフェールバックすることができません。

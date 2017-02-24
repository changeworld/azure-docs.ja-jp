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
ms.date: 12/15/2016
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: e650439b4bfd468c4bd7b236b80ce7361de1c6ac
ms.openlocfilehash: 972a3e88d15be656fd8cd3d51b7e507c7cbb49d5


---
# <a name="reprotect-from-azure-to-on-premises"></a>Azure からオンプレミスへの再保護

## <a name="overview"></a>概要
この記事では、Azure からオンプレミス サイトへ Azure Virtual Machines を再保護する方法について説明します。 この記事の手順は、 [このチュートリアル](site-recovery-vmware-to-azure-classic.md)を使用して、VMware 仮想マシンまたは Windows/Linux 物理サーバーをオンプレミス サイトから Azure にフェールオーバーした後に、それらをフェールバックする準備ができたときに実行します。

再保護が完了し、保護された仮想マシンがレプリケートされた後に、VM のフェールバックを開始してオンプレミスに戻すことができます。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。

## <a name="pre-requisites"></a>前提条件
再保護を準備する際に対応または考慮する必要がある前提条件の手順を次に示します。

### <a name="reprotect-needs-a-s2s-vpn-or-an-express-route-to-replicate-data-back-to-on-premises"></a>オンプレミスにデータをレプリケートして戻すため、再保護では S2S VPN または ExpressRoute が必要である
オンプレミスから Azure へのレプリケーションを、インターネット、またはパブリック ピアリングを使用した ExpressRoute 経由で実行できる場合、再保護とフェールバックでは、データをレプリケートするために S2S VPN を設定する必要があります。 ネットワークは、Azure のフェールオーバーされた VM からオンプレミスの構成サーバーに接続できるように指定する必要があります。 フェールオーバーされた VM の Azure ネットワークにプロセス サーバーをデプロイすることもできます。このプロセス サーバーも、オンプレミスの構成サーバーと通信できる必要があります。

### <a name="process-server-is-needed-to-replicate-the-data-from-source-vms-to-on-premises"></a>ソース VM からオンプレミスにデータをレプリケートするためにプロセス サーバーが必要である
<!-- Read more about a process server here.!todo -->

再保護する Azure VM からプロセス サーバーにレプリケーション データを送信します。 Azure VM からプロセス サーバーにアクセスできるようにネットワークを設定する必要があります。

Azure でプロセス サーバーをデプロイしたり、フェールオーバーの際に使用した既存のプロセス サーバーを使用したりできます。 考慮すべき重要な点は、Azure VM からプロセス サーバーにデータを送信するときの待機時間です。 

* ExpressRoute を設定している場合は、オンプレミスの PS を使用してデータを送信できます。 これは、VM と PS の間の待機時間が短いためです。
    
    ![Expressroute のアーキテクチャ ダイアグラム](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



* ただし、使用できるのが S2S VPN のみの場合は、Azure でプロセス サーバーをデプロイすることをお勧めします。

    ![VPN のアーキテクチャ ダイアグラム](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


レプリケーションは、S2S VPN または、ExpressRoute ネットワークのプライベート ピアリングを介してのみ実行されることに注意してください。 そのネットワーク チャネルで十分な帯域幅を使用できることを確認してください。

### <a name="ports-to-be-opened-on-different-machines-so-that-all-the-components-can-communicate"></a>すべてのコンポーネントが通信できるように、さまざまなコンピューターでポートを開く必要がある

![すべてのポートのフェールオーバー/フェールバック](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

### <a name="master-target-needs-to-be-available-on-premises-to-receive-data-from-process-server"></a>マスター ターゲットは、プロセス サーバーからデータを受信するために、オンプレミスで使用できる必要がある
マスター ターゲット サーバーは、プロセス サーバーからデータを受信してオンプレミスの VM の VMDK に書き込むために、オンプレミスである必要があります。 Windows VM を保護する場合、Windows マスター ターゲット サーバーが必要になります。ここで、オンプレミスの PS+MT <!-- !todo component --> を再利用できます。 Linux VM の場合は、追加の Linux マスター ターゲットをオンプレミスで設定する必要があります。

次のリンクをクリックして、マスター ターゲット サーバーをインストールする方法の手順を確認してください。

* [Windows マスター ターゲット サーバーをインストールする方法](site-recovery-vmware-to-azure.md#run-site-recovery-unified-setup)
* [Linux マスター ターゲット サーバーをインストールする方法](site-recovery-how-to-install-linux-master-target.md)


#### <a name="common-things-to-check-after-completing-installation-of-master-target"></a>マスター ターゲットのインストール完了後の一般的なチェック事項

* VM が vCenter サーバー上でオンプレミスに存在する場合、マスター ターゲット サーバーには、オンプレミスの VM の VMDK へのアクセス権が必要になります。 これは、レプリケートされたデータを VM のディスクに書き込むためです。 そのため、**オンプレミスの VM のデータ ストアは必ず、読み取りと書き込みアクセス権のある MT のホストにマウントする必要があります**。

* VM が vCenter サーバー上でオンプレミスに存在しない場合、再保護時に新しい VM を作成する必要があります。 MT を作成する ESX ホストでこの VM は作成されます。 そのため、希望するホストにフェールバック VM が作成されるように、ESX ホストは慎重に選択します。
    
* **マスター ターゲット サーバーをストレージ vMotioned にすることはできません**。 そのようにした場合、フェールバックが失敗する可能性があります。 ディスクが利用可能にならないため、この VM は起動しません。

* 既存のマスター ターゲット サーバーに新しいドライブを追加する必要があります。 このドライブはリテンション ドライブと呼ばれます。 新しいディスクを追加し、ドライブをフォーマットします。 リテンション ドライブは、VM がオンプレミスにレプリケートされたときの特定の時点で停止するために使用されます。 リテンション ドライブの条件の一部を以下に示します。これらの条件を満たしていない場合、このドライブはマスター ターゲット サーバーに表示されません。
   
   a. ボリュームを他の目的 (レプリケーションのターゲットなど) に使用することはできません。

   b. ボリュームをロック モードにすることはできません。

   c. ボリュームをキャッシュ ボリュームにすることはできません。 (そのボリュームに MT インストールが存在してはいけません。 PS + MT カスタム インストール ボリュームは、保持ボリュームの対象ではありません。 ここでは、インストール済みの PS + MT ボリュームが MT のキャッシュ ボリュームです。)

   d. ボリューム ファイル システムの種類として、FAT および FAT32 は使用できません。

   e. ボリュームの容量は&0; 以外である必要があります。

   e. Windows の既定の保持ボリュームは R ボリュームです。

   f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 Linux の既定の保持ボリュームは、/mnt/retention/ です。

* フェールオーバーした Linux VM には Linux のマスター ターゲット サーバーが必要です。 フェールオーバーした Windows VM には Windows のマスター ターゲット サーバーが必要です。

* VMware ツールをマスター ターゲット サーバーにインストールします。 VMware ツールがない場合、MT の ESXi ホスト上のデータストアを検出できません。

* vCenter プロパティを使用して MT VM の disk.EnableUUID = True パラメーターを有効にします。 <!-- !todo Needs link. -->



### <a name="failback-policy"></a>フェールバック ポリシー
オンプレミスにレプリケートするには、フェールバック ポリシーが必要です。 このポリシーは、順方向のポリシーを作成したときに自動的に作成されます。 次の点に注意してください。

1. このポリシーは、作成時に構成サーバーに自動的に関連付けられます。
2. このポリシーは編集できません。
3. ポリシーの設定値は、RPO しきい値 = 15 分、復旧ポイントのリテンション期間 = 24 時間、アプリケーションの整合性スナップショットの取得頻度 = 60 分です。![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)



## <a name="steps-to-reprotect"></a>再保護の手順

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
> 
> 

正常に再保護できると、VM は保護された状態になります。

## <a name="next-steps"></a>次のステップ

VM が保護された状態になると、フェールバックを開始できます。 フェールバックによって Azure の VM がシャットダウンされ、オンプレミスの VM が起動されます。 そのため、アプリケーションに短時間のダウンタイムが発生します。 したがって、アプリケーションがダウンタイムに対応できるように、フェールバックの時刻を選択します。

[VM のフェールバックを開始する手順](site-recovery-how-to-failback-v2a.md#steps-to-failback)

## <a name="common-issues"></a>一般的な問題 



<!--HONumber=Feb17_HO2-->



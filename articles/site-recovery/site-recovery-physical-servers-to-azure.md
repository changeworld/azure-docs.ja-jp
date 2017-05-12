---
title: "Azure への物理サーバーのレプリケート | Microsoft Docs"
description: "Azure Portal を使用して Azure Site Recovery をデプロイして、オンプレミスの Windows/Linux 物理サーバーの Azure へのレプリケーション、フェールオーバー、復旧を調整する方法を説明します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: e093b78980f5f1aacfdf7be278ef58906eedc634
ms.contentlocale: ja-jp
ms.lasthandoff: 04/25/2017


---
# <a name="replicate-physical-machines-to-azure-with-site-recovery"></a>Site Recovery を使用して物理マシンを Azure にレプリケートする


この記事では、Azure Portal の Azure Site Recovery サービスを使用して、オンプレミスの物理マシンを Azure にレプリケートする方法について説明します。

物理マシンを Azure に移行する (フェールオーバーのみ) 場合は、[こちらの記事](site-recovery-migrate-to-azure.md)で詳細を確認してください。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="deployment-summary"></a>デプロイの概要

次の手順を実行する必要があります。

1. 前提条件と制限事項を確認します。
2. Azure ネットワークおよびストレージ アカウントを設定します。
3. 構成サーバーとして動作するオンプレミスのマシンを準備します。
4. Recovery Services コンテナーを作成します。 このコンテナーには構成設定が含まれ、レプリケーションを調整します。
5. ソース、ターゲット、レプリケーションの設定を指定します。
6. レプリケートするマシン上でモビリティ サービスをデプロイします。
7. マシンのレプリケーションを有効にします。
7. テスト フェールオーバーを実行して、すべて想定どおりに動作していることを確認します。

## <a name="prerequisites"></a>前提条件

**サポート要件** | **詳細**
--- | ---
**Azure** | [Azure の要件](site-recovery-prereq.md#azure-requirements)を参照してください。
**オンプレミスの構成サーバー** | Windows Server 2012 R2 以降が動作しているオンプレミスのマシン (物理または VMware VM)。 構成サーバーは、Site Recovery のデプロイの際に設定します。<br/><br/> 既定では、プロセス サーバーとマスター対象サーバーもこのマシンにインストールされます。 スケールアップする場合は、別のプロセス サーバーが必要になる可能性があります。この場合も、要件は構成サーバーと同じです。<br/><br/> これらのコンポーネントの詳細については[こちらを参照してください](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements)。
**オンプレミスの VM** | レプリケートするマシンでは、[サポートされるオペレーティング システム](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)を実行し、[Azure の前提条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠する必要があります。
**URL** | 構成サーバーは以下の URL にアクセスできる必要があります。<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> IP アドレスベースのファイアウォール規則を使用している場合、その規則で Azure との通信が許可されていることを確認します。<br/></br> [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。<br/></br> ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (Access Control と ID 管理に使用されます)。<br/><br/> MySQL をダウンロードするために、http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi を許可します。
**モビリティ サービス** | これは、レプリケートする各マシンにインストールされます。

## <a name="limitations"></a>制限事項

**制限事項** | **詳細**
--- | ---
**Azure** | ストレージ アカウントとネットワーク アカウントは、コンテナーと同じリージョンに存在する必要があります。<br/><br/> Premium Storage アカウントを使用する場合は、レプリケーション ログを格納するために Standard ストア アカウントも必要になります。<br/><br/> インド中部およびインド南部では Premium アカウントにレプリケートすることはできません。
**オンプレミスの構成サーバー** | VMware VM に構成サーバーをインストールする場合、VM のアダプターの種類は VMXNET3 である必要があります。 そうでない場合は、[この更新プログラムをインストールしてください](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)。<br/><br/> VMware VM を使用している場合は、vSphere PowerCLI 6.0 をインストールする必要があります。<br/><br> このマシンをドメイン コントローラーにすることはできません。<br/><br/> このマシンには、静的 IP アドレスが必要です。<br/><br/> ホスト名は 15 文字以下で指定し、オペレーティング システムを英語にする必要があります。
**レプリケートされたマシン** | [Azure VM の制限事項](site-recovery-prereq.md#azure-requirements)を確認してください。<br/><br/> 同じワークロードを実行するマシンを整合性データ ポイントに同時に復旧できるようにマルチ VM の整合性を有効にする場合は、マシンのポート 20004 を開いてください。<br/><br/> 特定の種類の [Linux ストレージ](site-recovery-support-matrix-to-azure.md#support-for-storage)がサポートされています。
**フェールバック** | Azure から物理マシンにはフェールバックできません。 フェールオーバー後にオンプレミスにフェールバックできるようにするには、VMware VM にフェールバックできる VMware 環境が必要です。


## <a name="set-up-azure"></a>Azure をセットアップする

1. [Azure ネットワークをセットアップ](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)します。
    - Azure VM は、フェールオーバー後に作成されたときに、このネットワークに配置されます。
    - [Resource Manager](../resource-manager-deployment-model.md) またはクラシック モードでネットワークをセットアップすることができます。

2. レプリケートされるデータ用に [Azure ストレージ アカウント](../storage/storage-create-storage-account.md#create-a-storage-account)をセットアップします。
    - このアカウントには、Standard または [Premium](../storage/storage-premium-storage.md) を使用できます。
    - アカウントは、Resource Manager またはクラシック モードでセットアップすることができます。

## <a name="prepare-the-configuration-server"></a>構成サーバーを準備

1. オンプレミスの物理サーバーまたは VMware VM に、Windows Server 2012 R2 以降をインストールします。
2. [前提条件](#prerequisites)に記載されている URL にマシンからアクセスできることを確認します。

## <a name="prepare-for-mobility-service-installation"></a>モビリティ サービスのインストールを準備する

モビリティ サービスを物理マシンにプッシュする場合は、マシンにアクセスするためにプロセス サーバーで使用できるアカウントが必要になります。 このアカウントは、プッシュ インストールのみで使用されます。 ドメイン アカウントまたはローカル アカウントを使用することができます。

  - Windows の場合、ドメイン アカウントを使用していなければ、ローカル マシンでリモート ユーザー アクセス コントロールを無効にする必要があります。 無効にするには、レジスタで、**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** の下に、値 1 を指定した DWORD エントリの **LocalAccountTokenFilterPolicy** を追加します。
    - CLI から Windows 用のレジストリ エントリを追加する場合は、次のように入力します。  ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
  - Linux の場合、アカウントは、ソース Linux サーバーの root ユーザーである必要があります。


## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="select-the-protection-goal"></a>保護の目標を選択する

レプリケートの対象とレプリケート先を選択します。

1. **[Recovery Services コンテナー]** > [コンテナー] の順にクリックします。
2. リソース メニューで、**[Site Recovery]** > **[インフラストラクチャを準備する]** > **[保護の目標]** の順にクリックします。

    ![Choose goals](./media/site-recovery-vmware-to-azure/choose-goal-physical.PNG)
3. **[保護の目標]** で、**[To Azure] \(Azure へ)** を選択し、**[非仮想化/その他]** を選択します。


## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

構成サーバーをセットアップし、コンテナーに登録して、VM を検出します。

1. **[Site Recovery]** > **[インフラストラクチャを準備する]** > **[ソース]** の順にクリックします。
2. 構成サーバーがない場合は **[+ 構成サーバー]** をクリックします。

    ![Set up source](./media/site-recovery-vmware-to-azure/set-source1.png)
3. **[サーバーの追加]** で、**[サーバーの種類]** に **[構成サーバー]** が表示されていることを確認します。
4. Site Recovery 統合セットアップ インストール ファイルをダウンロードします。
5. コンテナー登録キーをダウンロードします。 このキーは、統合セットアップを実行するときに必要になります。 キーは生成後 5 日間有効です。

   ![Set up source](./media/site-recovery-vmware-to-azure/set-source2.png)


## <a name="run-site-recovery-unified-setup"></a>Site Recovery 統合セットアップを実行する

開始する前に、以下を行います。

- 簡単なビデオ概要を見ます (ビデオでは VMware VM をレプリケートする方法について説明していますが、物理マシンのレプリケーションでもプロセスは同様です)

    > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video1-Source-Infrastructure-Setup/player]

- 構成サーバー マシンのシステム クロックが[タイム サーバー](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)と同期していることを確認します。 15 分進んでいるか遅れている場合は、セットアップが失敗する可能性があります。
- 構成サーバー マシンのローカル管理者としてセットアップを実行します。
- マシンで TLS 1.0 が有効になっていることを確認します。

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 構成サーバーは[コマンド ラインから](http://aka.ms/installconfigsrv)インストールすることもできます。


## <a name="set-up-the-target-environment"></a>ターゲット環境をセットアップする

ターゲット環境をセットアップする前に、[Azure ストレージ アカウントとネットワーク](#set-up-azure)が存在することを確認します。

1. **[インフラストラクチャの準備]** > **[ターゲット]** の順にクリックし、使用する Azure サブスクリプションを選択します。
2. ターゲット デプロイ モデルを Resource Manager ベースとクラシック モードのどちらにするかを指定します。
3. Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。

   ![[ターゲット]](./media/site-recovery-vmware-to-azure/gs-target.png)
4. ストレージ アカウントまたはネットワークを作成していない場合は、**[+ ストレージ アカウント]** または **[+ ネットワーク]** をクリックして、Resource Manager アカウントまたはネットワーク インラインを作成します。

## <a name="set-up-replication-settings"></a>レプリケーション設定をセットアップする

開始する前に、簡単なビデオ概要を見ます (ビデオでは VMware VM をレプリケートする方法について説明していますが、物理マシンのレプリケーションでもプロセスは同様です)。

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video2-vCenter-Server-Discovery-and-Replication-Policy/player]

1. 新しいレプリケーション ポリシーを作成するには、**[Site Recovery インフラストラクチャ]** > **[レプリケーション ポリシー]** > **[+ レプリケーション ポリシー]** の順にクリックします。
2. **[レプリケーション ポリシーの作成]** で、ポリシー名を指定します。
3. **[RPO しきい値]** で、RPO の制限を指定します。 この値で、データの復旧ポイントを作成する頻度を指定します。 継続的なレプリケーションがこの制限を超えると、アラートが生成されます。
4. **[復旧ポイントの保持期間]** で、各復旧ポイントのリテンション期間の長さ (時間単位) を指定します。 レプリケートされた VM は、期間内の任意の時点に復旧できます。 Premium Storage にレプリケートされたマシンでは最大 24 時間のリテンション期間がサポートされ、Standard Storage の場合は 72 時間です。
5. **[アプリ整合性スナップショットの頻度]**で、アプリケーション整合性スナップショットを含む復旧ポイントの作成頻度 (分単位) を指定します。 **[OK]** をクリックしてポリシーを作成します。

    ![Replication policy](./media/site-recovery-vmware-to-azure/gs-replication2.png)
8. 新しいポリシーを作成すると、自動的に構成サーバーに関連付けられます。 既定でフェールバックの照合ポリシーが自動的に作成されます。 たとえば、レプリケーション ポリシーが **rep-policy** の場合、フェールバック ポリシーは **rep-policy-failback** になります。 このポリシーは、Azure からフェールバックを開始するまで使用されません。  


## <a name="plan-capacity"></a>容量を計画する

1. 基本的なインフラストラクチャをセットアップできたので、キャパシティ プランニングを立案し、追加のリソースが必要かどうかを検討できます。 [詳細情報](site-recovery-plan-capacity-vmware.md)。
2. 容量計画の作業が完了したら、**[容量計画は完了していますか?]** で **[はい]** を選択します。

   ![容量計画](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)


## <a name="prepare-vms-for-replication"></a>レプリケーション用の VM を準備する

レプリケートするすべてのマシンにモビリティ サービスをインストールする必要があります。 モビリティ サービスはさまざまな方法でインストールできます。

1. プロセス サーバーからのプッシュ インストールを使用してインストールします。 このメソッドを使用するには、事前にマシンを準備しておく必要があります。
2. System Center Configuration Manager、または Azure Automation DSC などのデプロイ ツールを使用してインストールします。
3.  手動でインストールします。

[詳細情報](site-recovery-vmware-to-azure-install-mob-svc.md)


## <a name="enable-replication"></a>Enable replication

開始する前に次の操作を実行してください。

- VM を追加または変更するときに、変更が有効になってポータルに表示されるまでに 15 分以上かかることがあります。
- VM の最終検出時刻は、**[構成サーバー]** > **[前回のアクセス]** で確認できます。
- 定期検出を待たずに VM を追加するには、構成サーバーを強調表示し (クリックしないでください)、**[更新]** をクリックします。
* VM をプッシュ インストール用に準備した場合は、レプリケーションを有効にすると、プロセス サーバーでモビリティ サービスが自動的にインストールされます。
- 開始する前に、簡単なビデオ概要を見ます (ビデオでは VMware VM をレプリケートする方法について説明していますが、物理マシンのレプリケーションでもプロセスは同様です)。

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video3-Protect-VMware-Virtual-Machines/player]


### <a name="exclude-disks-from-replication"></a>レプリケーションからディスクを除外する

既定では、マシンのすべてのディスクがレプリケートされます。 レプリケーションからディスクを除外できます。 たとえば、一時的なデータや、マシンまたはアプリケーションを再起動するたびに更新されるデータ (pagefile.sys や SQL Server tempdb など) が保存されたディスクをレプリケートから除外できます。

### <a name="replicate-vms"></a>VM をレプリケートする

1. **[アプリケーションをレプリケートする]** > **[ソース]** の順にクリックします。
2. **[ソース]** で [オンプレミス] を選択します。
3. **[ソースの場所]** で、目的の構成サーバー名を選択します。
4. **[マシンの種類]** で、**[物理マシン]** を選択します。
5. **[プロセス サーバー]** でプロセス サーバーを選択します。 追加のプロセス サーバーを作成していない場合、これは構成サーバーになります。 次に、 **[OK]**をクリックします

    ![Enable replication](./media/site-recovery-physical-to-azure/chooseVM.png)

6. **[ターゲット]** で、サブスクリプションと、フェールオーバー後に Azure VM を作成するリソース グループを選択します。 Azure で、フェールオーバー対象の VM に使用するデプロイ モデル (クラシックまたはリソース管理) を選択します。

7. データのレプリケーションに使用する Azure Storage アカウントを選択します。 既にセットアップしたアカウントを使用しない場合は、新しいアカウントを作成できます。

8. フェールオーバー後に Azure VM が接続する Azure ネットワークとサブネットを選択します。 保護の対象として選択したすべてのマシンにネットワーク設定を適用する場合は、**[選択したマシン用に今すぐ構成します。]** を選択します。 マシンごとに Azure ネットワークを選択する場合は、**[後で構成する]** を選択します。 既存のネットワークを使用しない場合は、ネットワークを作成することができます。

    ![Enable replication](./media/site-recovery-physical-to-azure/targetsettings.png)
9. **[物理マシン]** で [+] ボタンをクリックし、**[名前]** と **[IP アドレス]** に入力して、レプリケートするマシンのオペレーティング システムを選択します。
  ![Enable replication](./media/site-recovery-physical-to-azure/machineselect.png)マシンが検出されて一覧に表示されるまで数分かかります。

10. **[プロパティ]** >  の **[プロパティの構成]** で、モビリティ サービスをマシンに自動的にインストールするためにプロセス サーバーが使用するアカウントを選択します。
11. 既定では、すべてのディスクがレプリケートされます。 **[すべてのディスク]** をクリックし、レプリケートしないディスクをオフにします。 次に、 **[OK]**をクリックします 後で追加の VM プロパティを設定できます。

    ![Enable replication](./media/site-recovery-physical-to-azure/configprop.png)
11. **[レプリケーションの設定]** > **[レプリケーション設定の構成]** で、正しいレプリケーション ポリシーが選択されていることを確認します。 ポリシーを変更する場合、変更はレプリケートしているマシンと新しいマシンに適用されます。
12. マシンをレプリケーション グループにまとめる場合は、**[マルチ VM 整合性]** を有効にし、グループの名前を指定します。 次に、 **[OK]**をクリックします 以下の点に注意してください。

    * レプリケーション グループのマシンはまとめてレプリケートされ、フェールオーバー時にクラッシュ整合性復旧ポイントとアプリ整合性復旧ポイントを共有します。
    * VM と物理サーバーがワークロードをミラー化できるように、これらをまとめることをお勧めします。 マルチ VM 整合性を有効にすると、ワークロードのパフォーマンスに影響を及ぼす可能性があるため、複数のマシンが同じワークロードを実行しており、整合性が必要な場合にのみ使用してください。
    ![Enable replication](./media/site-recovery-physical-to-azure/policy.png)

13. **[レプリケーションを有効にする]**をクリックします。 **[設定]** > **[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックして、**保護の有効化**ジョブの進行状況を追跡できます。 **保護の最終処理** ジョブが実行されると、マシンはフェールオーバーできる状態になります。

レプリケーションを有効にした後、プッシュ インストールを設定している場合はモビリティ サービスがインストールされます。 モビリティ サービスがマシンでプッシュ インストールされると、保護ジョブが開始されて失敗します。 ジョブの失敗後、各マシンを手動で再起動する必要があります。 その後、保護ジョブが再び開始され、最初のレプリケーションが実行されます。


### <a name="view-and-manage-azure-vm-properties"></a>Azure VM プロパティを表示して管理する

VM プロパティを確認し、必要な変更を加えることをお勧めします。

1. **[レプリケートされたアイテム]** をクリックし、マシンを選択します。 **[要点]** ブレードにマシンの設定と状態に関する情報が表示されます。
2. **[プロパティ]** で、VM のレプリケーションとフェールオーバーの情報を確認できます。
3. **[コンピューティングとネットワーク]** > **[コンピューティングのプロパティ]** で、Azure VM の名前とターゲットのサイズを指定できます。 必要に応じて、 [Azure の要件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) に準拠するように名前を変更します。
4. Azure VM に割り当てられるターゲット ネットワーク、サブネット、および IP アドレスの設定を変更することができます。

   - ターゲット IP アドレスを設定できます。

    - アドレスを指定しなかった場合、フェールオーバーされたマシンで DHCP が使用されます。
    - フェールオーバーで使用できないアドレスを設定した場合、フェールオーバーは機能しません。
    - テスト フェールオーバー ネットワークのアドレスを利用できる場合、テスト フェールオーバーに同じターゲット IP アドレスを使用できます。

   - ネットワーク アダプターの数は、ターゲット仮想マシンに指定したサイズによって異なります。

     - ソース マシン上のネットワーク アダプターの数が、ターゲット マシンのサイズに許可されているアダプターの数以下の場合、ターゲットのアダプターの数は、ソースと同じになります。
     - ソース仮想マシン用のアダプターの数が、ターゲットのサイズに許可されている数を超える場合は、ターゲットの最大サイズが使用されます。
     - たとえば、ソース マシンに 2 つのネットワーク アダプターがあり、ターゲット マシンのサイズが 4 つをサポートしている場合は、ターゲット マシンのアダプターの数は、2 つになります。 ソース マシンに 2 つのアダプターがあるが、サポートされているターゲット サイズで 1 つしかサポートしていない場合、ターゲット マシンのアダプターの数は 1 つだけになります。     
   - 仮想マシンにネットワーク アダプターが複数ある場合、これらのアダプターはすべて同じネットワークに接続されます。
   - 仮想マシンにネットワーク アダプターが複数ある場合は、一覧で最初に表示されるアダプターが、Azure 仮想マシンの*既定*のネットワーク アダプターとなります。
5. **[ディスク]** で、VM のオペレーティング システム ディスクと、レプリケートされるデータ ディスクを確認できます。

## <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

すべてのセットアップが完了したら、テスト フェールオーバーを実行して、すべて想定どおりに動作していることを確認します。 始める前に、簡単なビデオ概要を見ます。

>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video4-Recovery-Plan-DR-Drill-and-Failover/player]


1. 1 つの仮想マシンをフェールオーバーする場合は、**[設定]** > **[レプリケートされたアイテム]** で、その VM をクリックし、**[+ テスト フェールオーバー]** をクリックします。

    ![[テスト フェールオーバー]](./media/site-recovery-vmware-to-azure/TestFailover.png)

2. 復旧計画をフェールオーバーする場合は、**[設定]** > **[復旧計画]** で、計画を右クリックし、**[テスト フェールオーバー]** をクリックします。 復旧計画を作成する場合は、[こちらの手順に従ってください](site-recovery-create-recovery-plans.md)。  
3. **[テスト フェールオーバー]** で、フェールオーバー後に Azure VM が接続する Azure ネットワークを選択します。
4. **[OK]** をクリックすると、フェールオーバーが開始されます。 進行状況を追跡するには、VM をクリックしてそのプロパティを開くか、コンテナー名、**[設定]** > **[ジョブ]** > **[Site Recovery ジョブ]** で **[テスト フェールオーバー]** ジョブをクリックします。
5. フェールオーバーの完了後は、Azure Portal の **[仮想マシン]** にレプリカの Azure マシンも表示されるようになります。 VM が適切なサイズであること、適切なネットワークに接続していること、実行されていることを確認する必要があります。
6. [フェールオーバー後の接続の準備](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)が完了したら、Azure VM に接続できるようになります。
7. 完了したら、復旧計画の **[Cleanup test failover (テスト フェールオーバーのクリーンアップ)]** をクリックします。 **[メモ]** を使用して、テスト フェールオーバーに関連する観察結果をすべて記録し、保存します。 これで、テスト フェールオーバー中に作成された仮想マシンが削除されます。

詳しくは、「[Site Recovery での Azure へのフェールオーバーをテストする](site-recovery-test-failover-to-azure.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

レプリケーションの準備を完了して実行した後に障害が発生したら、Azure にフェールオーバーし、レプリケートされたデータから Azure VM が作成されます。 通常の運用に戻るときは、プライマリ ロケーションにフェールバックするまで、Azure でワークロードとアプリにアクセスできます。

- さまざまな種類のフェールオーバーとそれらを実行する方法の[詳細を確認](site-recovery-failover.md)します。
- レプリケーションやフェールバックではなく、マシンを移行する場合は、[詳細](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers)を確認してください。
- 物理マシンをレプリケートする場合、可能なフェールバック先は VMWare 環境のみです。 [フェールバックの詳細](site-recovery-failback-azure-to-vmware.md)を確認してください。

## <a name="third-party-software-notices-and-information"></a>サード パーティ製ソフトウェアに関する通知および情報
Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.


---
title: Azure Site Recovery を使用してオンプレミスのマシンを Azure に移行する | Microsoft Docs
description: この記事では、Azure Site Recovery を使用して、オンプレミスのマシンを Azure に移行する方法について説明します。
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5023171c4f943b7e698a0b6bbcadef209965e2df
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2018
ms.locfileid: "53789248"
---
# <a name="migrate-on-premises-machines-to-azure"></a>オンプレミスのマシンを Azure に移行する

[Azure Site Recovery](site-recovery-overview.md) サービスを使用して、ビジネス継続性およびディザスター リカバリー (BCDR) の目的でオンプレミス マシンや Azure VM のディザスター リカバリーを管理よび調整する他に、オンプレミス マシンを Azure に移行するためにも Site Recovery を使用できます。


このチュートリアルでは、オンプレミスの VM と物理サーバーを Azure に移行する方法を説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * レプリケーションの目標を選ぶ
> * ソース環境とターゲット環境をセットアップする
> * レプリケーション ポリシーを設定する
> * レプリケーションを有効にする
> * テスト移行を実行して、すべて想定どおりに動作していることを確認する
> * Azure へのワンタイム フェールオーバーを実行する

これは、シリーズ 3 番目のチュートリアルです。 このチュートリアルでは、前のチュートリアルで以下のタスクがすでに完了していることを前提としています。

1. [Azure を準備する](tutorial-prepare-azure.md)
2. オンプレミスの [VMware](vmware-azure-tutorial-prepare-on-premises.md) または [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) サーバーを準備する。

開始する前に、ディザスター リカバリーのために [VMware](vmware-azure-architecture.md) または [Hyper-V](hyper-v-azure-architecture.md) アーキテクチャを確認しておくと役立ちます。


## <a name="prerequisites"></a>前提条件

準仮想化ドライバーによってエクスポートされたデバイスはサポートされません。


## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

1. [Azure Portal](https://portal.azure.com) > **Recovery Services** にサインインします。
2. **[リソースの作成]** > **[監視 + 管理]** > **[Backup and Site Recovery]** の順にクリックします。
3. **[名前]** に、フレンドリ名 **ContosoVMVault** を指定します。 複数のサブスクリプションがある場合は、適切なものを選択します。
4. リソース グループ **ContosoRG** を作成します。
5. Azure リージョンを指定します。 サポートされているリージョンを確認するには、[Azure Site Recovery の価格の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)に関するページでご利用可能な地域をご覧ください。
6. ダッシュボードからコンテナーにすばやくアクセスするには、**[ダッシュボードにピン留めする]**、**[作成]** の順にクリックします。

   ![新しいコンテナー](./media/migrate-tutorial-on-premises-azure/onprem-to-azure-vault.png)

新しいコンテナーは、**[ダッシュボード]** の **[すべてのリソース]** と、メインの **[Recovery Services コンテナー]** ページに追加されます。



## <a name="select-a-replication-goal"></a>レプリケーションの目標を選ぶ

レプリケートの対象とレプリケート先を選択します。
1. **[Recovery Services コンテナー]** > [コンテナー] の順にクリックします。
2. リソース メニューで、**[Site Recovery]** > **[インフラストラクチャの準備]** > **[保護の目標]** の順にクリックします。
3. **[保護の目標]** で、何を移行するかを選択します。
    - **VMware**:**[To Azure]\(Azure へ\)** > **[Yes, with VMware vSphere Hypervisor]\(はい、VMware vSphere ハイパーバイザーを使用する\)** の順に選択します。
    - **物理マシン**:**[To Azure]\(Azure へ\)** > **[非仮想化/その他]** の順に選択します。
    - **Hyper-V**:**[To Azure]\(Azure へ\)** > **[Yes, with Hyper-V]\(はい、Hyper-V を使用する\)** の順に選択します。 Hyper-V VM が VMM で管理される場合は **[はい]** を選択します。


## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

- VMware VM のソース環境を[設定します](vmware-azure-tutorial.md#set-up-the-source-environment)。
- 物理サーバーのソース環境を[設定します](physical-azure-disaster-recovery.md#set-up-the-source-environment)。
- Hyper-V VM のソース環境を[設定します](hyper-v-azure-tutorial.md#set-up-the-source-environment)。

## <a name="set-up-the-target-environment"></a>ターゲット環境をセットアップする

ターゲット リソースを選択して確認します。

1. **[インフラストラクチャの準備]** > **[ターゲット]** の順にクリックし、使用する Azure サブスクリプションを選択します。
2. Resource Manager デプロイ モデルを指定します。
3. Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。

## <a name="set-up-a-replication-policy"></a>レプリケーション ポリシーを設定する

- VMware VM の[レプリケーション ポリシーを設定します](vmware-azure-tutorial.md#create-a-replication-policy)。
- 物理サーバーの[レプリケーション ポリシーを設定します](physical-azure-disaster-recovery.md#create-a-replication-policy)。
- Hyper-V VM の[レプリケーション ポリシーを設定します](hyper-v-azure-tutorial.md#set-up-a-replication-policy)。


## <a name="enable-replication"></a>レプリケーションを有効にする

- VMware VM の [レプリケーションを有効にします](vmware-azure-tutorial.md#enable-replication)。
- 物理サーバーの[レプリケーションを有効にします](physical-azure-disaster-recovery.md#enable-replication)。
- [VMM を使用](hyper-v-vmm-azure-tutorial.md#enable-replication)するか、または [VMM を使用せずに](hyper-v-azure-tutorial.md#enable-replication)、Hyper-V VM のレプリケーションを有効にします。


## <a name="run-a-test-migration"></a>テスト移行を実行する

Azure への[テスト フェールオーバー](tutorial-dr-drill-azure.md)を実行して、すべて想定どおりに動作していることを確認します。


## <a name="migrate-to-azure"></a>Azure への移行

移行するマシンのフェールオーバーを実行します。

1. **[設定]** > **[レプリケートされたアイテム]** で、[マシン] > **[フェールオーバー]** をクリックします。
2. **[フェールオーバー]** で、フェールオーバーする**復旧ポイント**を選択します。 最新の復旧ポイントを選択します。
3. 暗号化キー設定は、このシナリオには関係しません。
4. **[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。 Site Recoverty は、仮想マシンのシャットダウンを試行してからフェールオーバーをトリガーします。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 フェールオーバーの進行状況は **[ジョブ]** ページで確認できます。
5. 想定どおりに Azure VM が Azure に表示されることを確認します。
6. **[レプリケートされたアイテム]** で [VM] > **[移行の完了]** を右クリックします。 次の処理が実行されます。

    - 移行プロセスが終了し、AWS VM のレプリケーションが停止して、その VM での Site Recovery の課金が停止します。
    - この手順でレプリケーション データがクリーンアップされます。 移行した VM は削除されません。

    ![移行の完了](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **進行中のフェールオーバーを取り消さないでください**:フェールオーバーが開始される前に、VM のレプリケーションが停止されます。 進行中のフェールオーバーをキャンセルすると、フェールオーバーは停止しますが、VM が再びレプリケートされることはありません。

一部のシナリオでは、フェールオーバーが完了するまでに、さらに約 8 ～ 10 分の処理が必要です。 物理サーバー、VMware Linux マシン、DHCP サービスが有効でない VMware VM、ブート ドライバー storvsc、vmbus、storftt、intelide、atapi を持たない VMware VM については、テスト フェールオーバーの時間がさらに長くなる場合もあります。

## <a name="after-migration"></a>移行後

マシンが Azure に移行された後に、多くの手順を完了する必要があります。

[復旧計画]( https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)における組み込みの自動化スクリプト機能を使用して、移行プロセスの一環として、いくつかの手順を自動化できます。   


### <a name="post-migration-steps-in-azure"></a>Azure での移行後の手順

- データベース接続文字列、および Web サーバー構成の更新など、移行後のアプリの微調整を実行します。 
- Azure で現在実行されている移行後のアプリケーション上で、最終的なアプリケーションと移行の受け入れのテストを実行します。
- [Azure VM エージェント](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)では、Azure ファブリック コントローラーと VM の対話を管理します。 Azure Backup、Site Recovery、および Azure Security など、一部の Azure サービスでは必須です。
    - VMware マシンおよび物理サーバーを移行する場合、モビリティ サービス インストーラーによって Windows マシン上で利用できる Azure VM エージェントがインストールされます。 Linux VM 上で、フェールオーバー後にエージェントをインストールすることをお勧めします。 a
    - Azure VM をセカンダリ リージョンに移行している場合、移行前に、VM 上に Azure VM エージェントがプロビジョニングされる必要があります。
    - Hyper-V VM を Azure に移行している場合、移行後に Azure VM 上に Azure VM エージェントをインストールします。
- VM から任意の Site Recovery プロバイダー/エージェントを手動で削除します。 VMware VM または物理サーバーを移行する場合、Azure VM から [モビリティ サービスをアンインストール][vmware-azure-install-mobility-service.md#uninstall-mobility-service-on-a-windows-server-computer] します。
- 復元性の向上:
    - Azure Backup サービスを使用して、Azure VM をバックアップすることで、データの安全性を保持します。 [詳細情報]( https://docs.microsoft.com/azure/backup/quick-backup-vm-portal)。
    - Azure VM を Site Recovery のセカンダリ リージョンにレプリケートし、継続的にワークロードを実行して利用可能にします。 [詳細情報](azure-to-azure-quickstart.md)。
- セキュリティの強化：
    - Azure Security Center の[ジャスト イン タイム管理]( https://docs.microsoft.com/azure/security-center/security-center-just-in-time)を利用して、受信トラフィック アクセスをロック ダウンして制限します。
    - [ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/security-overview)を使って、ネットワーク トラフィックを管理エンドポイントに制限します。
    - [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) をデプロイして、ディスクをセキュリティ保護し、盗難や不正アクセスからデータを安全に保護します。
    - [IaaS リソースのセキュリティ保護]( https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/ )に関する詳細を読み、[Azure Security Center](https://azure.microsoft.com/services/security-center/ ) を確認してください。
- 監視と管理：
    - [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) をデプロイして、リソースの使用率と消費量を監視します。

### <a name="post-migration-steps-on-premises"></a>オンプレミスにおける移行後の手順

- 移行後の Azure VM インスタンスで実行するアプリに、アプリ トラフィックを移動します。
- ローカル VM インベントリからオンプレミスの VM を削除します。
- ローカル バックアップからオンプレミスの VM を削除します。
- Azure VM の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、オンプレミス VM を Azure VM に移行しました。 Azure VM のセカンダリ Azure リージョンに[ディザスター リカバリーを設定](azure-to-azure-replicate-after-migration.md)できるようになりました。

  

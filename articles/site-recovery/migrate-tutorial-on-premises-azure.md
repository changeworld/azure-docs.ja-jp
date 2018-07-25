---
title: Azure Site Recovery を使用してオンプレミスのマシンを Azure に移行する | Microsoft Docs
description: この記事では、Azure Site Recovery を使用して、オンプレミスのマシンを Azure に移行する方法について説明します。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: bc04483c35162c0b461fd03c63aaa894b1bc199a
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070679"
---
# <a name="migrate-on-premises-machines-to-azure"></a>オンプレミスのマシンを Azure に移行する

[Azure Site Recovery](site-recovery-overview.md) サービスを使用して、ビジネス継続性およびディザスター リカバリー (BCDR) の目的でオンプレミス マシンや Azure VM のディザスター リカバリーを管理よび調整する他に、オンプレミス マシンを Azure に移行するためにも Site Recovery を使用できます。


このチュートリアルでは、オンプレミスの VM と物理サーバーを Azure に移行する方法を説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * レプリケーションの目標を選ぶ
> * ソース環境とターゲット環境をセットアップする
> * レプリケーション ポリシーを設定する
> * レプリケーションを有効にする
> * テスト移行を実行して、すべて想定どおりに動作していることを確認する
> * Azure へのワンタイム フェールオーバーを実行する

これは、シリーズ 3 番目のチュートリアルです。 このチュートリアルでは、前のチュートリアルで以下のタスクがすでに完了していることを前提としています。

1. [Azure を準備する](tutorial-prepare-azure.md)
2. オンプレミスの [VMware](vmware-azure-tutorial-prepare-on-premises.md) または [Hyper-V] (hyper-v-prepare-on-premises-tutorial.md) サーバーを準備する。

開始する前に、ディザスター リカバリーのために [VMware](vmware-azure-architecture.md) または [Hyper-V](hyper-v-azure-architecture.md) アーキテクチャを確認しておくと役立ちます。


## <a name="prerequisites"></a>前提条件

- 準仮想化ドライバーによってエクスポートされたデバイスはサポートされません。
 
> [!WARNING]
> VM を物理サーバーのように扱うことで、(VMware、Hyper-V 以外の) 別の仮想化プラットフォーム (XenServer など) に VM を移行できます。 ただし、この方法は Microsoft によってテストと検証が行われておらず、うまくいく場合といかない場合があります。 たとえば、XenServer ツールと準仮想化ストレージおよびネットワークのドライバーが移行開始前に VM からアンインストールされていないと、XenServer プラットフォームで実行される VM は Azure で動作しない可能性があります。


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
    - **VMware**: **[To Azure]\(Azure へ\)** > **[Yes, with VMware vSphere Hypervisor]\(はい、VMware vSphere ハイパーバイザーを使用する\)** の順に選択します。
    - **物理マシン**: **[To Azure]\(Azure へ\)** > **[非仮想化/その他]** の順に選択します。
    - **Hyper-V**: **[Azure へ]** > **[Yes, with Hyper-V]\(はい、Hyper-V を使用する\)** を選択します。 Hyper-V VM が VMM で管理される場合は **[はい]** を選択します。


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
6. **[レプリケートされたアイテム]** で [VM] > **[移行の完了]** を右クリックします。 これによって、移行プロセスが終了し、VM のレプリケーションが停止して、その VM での Site Recovery の課金が停止します。

    ![移行の完了](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **進行中のフェールオーバーを取り消さないでください**。フェールオーバーが開始される前に VM のレプリケーションが停止します。 進行中のフェールオーバーをキャンセルすると、フェールオーバーは停止しますが、VM が再びレプリケートされることはありません。

一部のシナリオでは、フェールオーバーが完了するまでに、さらに約 8 ～ 10 分の処理が必要です。 物理サーバー、VMware Linux マシン、DHCP サービスが有効でない VMware VM、ブート ドライバー storvsc、vmbus、storftt、intelide、atapi を持たない VMware VM については、テスト フェールオーバーの時間がさらに長くなる場合もあります。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、オンプレミス VM を Azure VM に移行しました。 VM を正常に移行できたところで:
- 移行した VM の[ディザスター リカバリーを設定](azure-to-azure-replicate-after-migration.md)します。
- [安全で管理の行き届いた Azure のクラウド](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)機能を活用して、Azure で VM を管理します。
  

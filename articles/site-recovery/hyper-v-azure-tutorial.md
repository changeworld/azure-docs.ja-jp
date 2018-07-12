---
title: Azure Site Recovery を使用して Azure にオンプレミス Hyper-V VM (VMM なし) のディザスター リカバリーを設定する |Microsoft Docs
description: Azure Site Recovery サービスを使用して Azure にオンプレミス Hyper-V VM (VMM なし) のディザスター リカバリーを設定する方法について説明します。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/06/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: f09d66e069ac22e5b8203d9871d2e5645570086a
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917950"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Azure にオンプレミス Hyper-V VM のディザスター リカバリーを設定する

[Azure Site Recovery](site-recovery-overview.md) サービスは、オンプレミスのコンピューターと Azure 仮想マシン (VM) のレプリケーション、フェールオーバー、およびフェールバックの管理と調整を行うことでディザスター リカバリー戦略に貢献します。

このチュートリアルでは、Azure にオンプレミス Hyper-V VM のディザスター リカバリーを設定する方法を紹介します。 このチュートリアルの対象は、System Center Virtual Machine Manager (VMM) で管理されない Hyper-V VM です。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * レプリケーションのソースとターゲットを選択します。
> * ソースのレプリケーション環境 (オンプレミスの Site Recovery コンポーネントなど) と、ターゲットのレプリケーション環境を設定します。
> * レプリケーション ポリシーを作成します。
> * VM のレプリケーションを有効にします。

これは、シリーズ 3 番目のチュートリアルです。 このチュートリアルでは、前のチュートリアルで以下のタスクがすでに完了していることを前提としています。

1. [Azure を準備する](tutorial-prepare-azure.md)
2. [オンプレミスの Hyper-V を準備する](tutorial-prepare-on-premises-hyper-v.md)

開始する前に、こちらのディザスター リカバリー シナリオの[アーキテクチャを確認](concepts-hyper-v-to-azure-architecture.md)しておくと有用です。

## <a name="select-a-replication-goal"></a>レプリケーションの目標を選ぶ


1. **[すべてのサービス]** > **[Recovery Services コンテナー]** で、前のチュートリアルで準備したコンテナー **ContosoVMVault** を選択します。
2. **[作業の開始]** で、**[Site Recovery]** をクリックします。 次に、**[インフラストラクチャの準備]** をクリックします。
3. **[保護の目標]** > **[マシンのある場所]** で、**[オンプレミス]** を選びます。
4. **[マシンをどこにレプリケートしますか]** で、**[To Azure]\(Azure\)** を選びます。
5. **[Are you using System Center VMM to manage your Hyper-V hosts]\(System Center VMM を使用して Hyper-V ホストを管理しますか\)** で、**[いいえ]** を選択します。 次に、 **[OK]** をクリックします

    ![レプリケーションの目標](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>展開の計画を確認する

大規模な展開を計画している場合は、[Hyper-V レプリケーションの展開計画](hyper-v-deployment-planner-overview.md)を完了する必要があります。 このチュートリアルの目的として、**[Have you completed deployment planning?]\(展開計画を完了しましたか?\)** のドロップダウン リストで、**[I will do it later]\(後で実行する\)** を選択します。

![展開の計画](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

ソース環境をセットアップするには、Hyper-V サイトを作成し、そのサイトに Hyper-V ホストを追加します。 次に、Azure Site Recovery Provider と Azure Recovery Services エージェントをダウンロードして各ホストにインストールし、コンテナーに Hyper-V サイトを登録します。 

1. **[インフラストラクチャの準備]** で、**[ソース]** をクリックします。
2. **[+Hyper-V サイト]** をクリックし、前のチュートリアルで作成したサイトの名前 **ContosoHyperVSite** を指定します。

    ![Hyper-V サイト](./media/hyper-v-azure-tutorial/hyperv-site.png)

3. サイトが作成されたら、**[+ Hyper-V Server]** をクリックします。

    ![Hyper-V サーバー](./media/hyper-v-azure-tutorial/hyperv-server.png)

4. プロバイダーのセットアップ ファイルをダウンロードします。
6. コンテナー登録キーをダウンロードします。 プロバイダーのセットアップの実行に、このキーが必要になります。 キーは生成後 5 日間有効です。

    ![プロバイダーのダウンロード](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>プロバイダーをインストールする

プロバイダーのセットアップ ファイル (AzureSiteRecoveryProvider.exe) を **ContosoHyperVSite** サイトに追加した各 Hyper-V ホストで実行します。 セットアップによって Azure Site Recovery プロバイダーと Recovery Services エージェントが各 Hyper-V ホストにインストールされます。

1. Azure Site Recovery プロバイダーのセットアップ ウィザードの **[Microsoft Update]** で、Microsoft Update を使用してプロバイダーの更新プログラムを確認するように選択します。
2. **[インストール]** で、プロバイダーとエージェントの既定のインストール先のままにして **[インストール]** をクリックします。
3. インストール後、Microsoft Azure Site Recovery 登録ウィザードの **[資格情報コンテナー設定]** で **[参照]** をクリックし、**[キー ファイル]** でダウンロードしたコンテナー キー ファイルを選択します。 
4. Azure Site Recovery のサブスクリプション、コンテナー名 (**ContosoVMVault**)、Hyper-V サーバーが属している Hyper-V サイト (**ContosoHyperVSite**) を指定します。
5. **[プロキシ設定]** で、**[プロキシを使用せずに直接 Azure Site Recovery に接続する]** を選択します。
6. **[登録]** で、サーバーがコンテナーに登録されたら **[完了]** をクリックします。

Azure Site Recovery によって Hyper-V サーバーからメタデータが取得され、**[Site Recovery インフラストラクチャ]** の  > **[Hyper-V ホスト]** にサーバーが表示されます。 このプロセスには最大 30 分かかる可能性があります。


## <a name="set-up-the-target-environment"></a>ターゲット環境をセットアップする

ターゲット リソースを選択して確認します。 

1. **[インフラストラクチャの準備]** > **[ターゲット]** の順にクリックします。
2. サブスクリプションと、フェールオーバー後に Azure VM が作成されるリソース グループ **ContosoRG** を選択します。
3. **Resource Manager"** デプロイメント モデルを選択します。

Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。


## <a name="set-up-a-replication-policy"></a>レプリケーション ポリシーを設定する

> [!NOTE]
> Hyper-V から Azure へのレプリケーション ポリシーで、15 分のコピー頻度オプションは、5 分および 30 秒のコピー頻度設定の方が多用されるため、廃止されています。 15 分のコピー頻度を使用するレプリケーション ポリシーは、5 分のコピー頻度設定を使用するように自動的に更新されます。 5 分および 30 秒のコピー頻度オプションでは、15 分のコピー頻度と比較して、レプリケーションのパフォーマンスと目標復旧時点が向上し、帯域幅の使用量とデータ転送量への影響は最小限に抑えられます。

1. **[インフラストラクチャの準備]** > **[レプリケーションの設定]** > **[+ 作成と関連付け]** の順にクリックします。
2. **[ポリシーの作成と関連付け]** で、ポリシー名「**ContosoReplicationPolicy**」を指定します。
3. 既定の設定のままにして **[OK]** をクリックします。
    - **[コピーの頻度]** は、差分データを (初期レプリケーション後) 5 分ごとにレプリケートするように設定されています。
    - **[復旧ポイントのリテンション期間]** では、各復旧ポイントのリテンション期間が 2 時間に設定されています。
    - **[アプリ整合性スナップショットの頻度]** は、アプリ整合性スナップショットを含む復旧ポイントが 1 時間ごとに作成されるように設定されています。
    - **[初期レプリケーションの開始時刻]** は、初期レプリケーションを直ちに開始するように設定されています。
4. ポリシーが作成されたら、**[OK]** をクリックします。 新しいポリシーを作成すると、指定した Hyper-V サイト (**ContosoHyperVSite**) に自動的に関連付けられます。

    ![Replication policy](./media/hyper-v-azure-tutorial/replication-policy.png)


## <a name="enable-replication"></a>レプリケーションを有効にする


1. **[アプリケーションのレプリケート]** で、**[ソース]** をクリックします。 
2. **[ソース**] で "**ContosoHyperVSite**" サイトを選択します。 次に、 **[OK]** をクリックします
3. **[ターゲット]** で、Azure がレプリケーション対象であること、コンテナーのサブスクリプション、および**Resource Manager** モデルを確認します。
4. レプリケートされるデータのために前のチュートリアルで作成した "**contosovmsacct1910171607**" ストレージ アカウントと "**ContosoASRnet**" ネットワーク (フェールオーバー後に Azure VM を配置する) を選択します。
5. **[仮想マシン]** > **[選択]** で、レプリケートする VM を選択します。 次に、 **[OK]** をクリックします

 **[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックして、**[保護を有効にする]** アクションの進行状況を追跡できます。 **[保護の最終処理]** ジョブが完了すると、初期レプリケーションが完了し、仮想マシンがフェールオーバーを実行できる状態になります。

## <a name="next-steps"></a>次の手順
[ディザスター リカバリーのテストを実行する](tutorial-dr-drill-azure.md)

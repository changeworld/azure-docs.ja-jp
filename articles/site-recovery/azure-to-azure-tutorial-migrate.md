---
title: Azure Site Recovery を使用して Azure リージョン間で Azure VM を移行する | Microsoft Docs
description: Azure Site Recovery を使用して、異なる Azure リージョン間で Azure IaaS VM を移行します。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/06/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: a8f081ca9a0868bee11b27e1065a72806cb8c2c9
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214603"
---
# <a name="migrate-azure-vms-to-another-region"></a>Azure VM を別のリージョンに移行する

[Azure Site Recovery](site-recovery-overview.md) サービスを使用して、ビジネス継続性およびディザスター リカバリー (BCDR) の目的でオンプレミス マシンや Azure VM のディザスター リカバリーを管理よび調整する他に、Azure VM をセカンダリ リージョンに移行するためにも Site Recovery を使用できます。 Azure VM を移行するには、それらのレプリケーションを有効にして、プライマリ リージョンから選択したセカンダリ リージョンにフェールオーバーします。

このチュートリアルでは、Azure VM を別のリージョンに移行する方法を説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Recovery Services コンテナーを作成する
> * VM のレプリケーションを有効にする
> * VM を移行するためにフェールオーバーを実行する

このチュートリアルでは、既に Azure サブスクリプションがあることを前提としています。 そうでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成してください。





## <a name="prerequisites"></a>前提条件

- 移行元の Azure リージョンに Azure VM が存在する。
- [シナリオのアーキテクチャとコンポーネント](azure-to-azure-architecture.md)を理解している。
- [サポートの制限と要件](azure-to-azure-support-matrix.md)を確認する。



## <a name="before-you-start"></a>開始する前に

レプリケーションをセットアップする前に、以下の各手順を実行しておいてください。


### <a name="verify-target-resources"></a>ターゲット リソースの確認

1. Azure サブスクリプションによって、ディザスター リカバリーに使用されるターゲット リージョンに VM を作成できることを確認します。 サポートに連絡して、必要なクォータを有効にしてください。

2. サブスクリプションに、ソース VM と一致するサイズの VM をサポートするための十分なリソースがあることを確認します。 Site Recovery によって、ターゲット VM に対して同じサイズまたは最も近いサイズが選択されます。


### <a name="verify-account-permissions"></a>アカウントのアクセス許可を確認する

無料 Azure アカウントを作成したところであれば、自分のサブスクリプションの管理者になっています。 サブスクリプションの管理者でなければ、管理者に協力をあおぎ、必要なアクセス許可を割り当てます。 新しい VM のレプリケーションを有効にするには、次のアクセス許可が必要です。

1. VM を Azure リソースに作成するアクセス許可。 '仮想マシン共同作成者' 組み込みロールには次のアクセス許可が与えられています。
    - 選択したリソース グループ内に VM を作成するためのアクセス許可
    - 選択した仮想ネットワーク内に VM を作成するためのアクセス許可
    - 選択したストレージ アカウントに書き込むためのアクセス許可

2. Azure Site Recovery 操作を管理するためのアクセス許可も必要です。 'Site Recovery 共同作成者' ロールには、Recovery Services コンテナーでの Site Recovery の操作の管理に必要なすべてのアクセス許可があります。


### <a name="verify-vm-outbound-access"></a>VM 発信アクセスの確認

1. 移行しようとする VM のネットワーク接続を制御するために認証プロキシを使用していないことを確認します。 
2. このチュートリアルの目的から、移行しようとする VM はインターネットにアクセスでき、発信アクセスを制御するファイアウォール プロキシを使用していないことを前提としています。 これに該当しない場合は、[ここ](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity)で要件を確認してください。

### <a name="verify-vm-certificates"></a>VM の証明書の確認

移行する Azure VM に、最新のルート証明書がすべて存在することを確認します。 最新のルート証明書がない場合、セキュリティの制約のため、VM を Site Recovery に登録できません。

- Windows VM については、最新のすべての Windows 更新プログラムを VM にインストールして、すべての信頼されたルート証明書をマシンに用意します。 接続されていない環境の場合は、組織の標準の Windows Update プロセスおよび証明書更新プロセスに従ってください。
- Linux VM の場合は、Linux ディストリビューターから提供されるガイダンスに従って、VM で最新の信頼されたルート証明書と証明書失効リストを取得します。



## <a name="create-a-vault"></a>コンテナーの作成

任意のリージョン (ソース リージョンを除く) にコンテナーを作成します。

1. [Azure Portal](https://portal.azure.com) > **Recovery Services** にサインインします。
2. **[リソースの作成]** > **[監視 + 管理]** > **[Backup and Site Recovery]** の順にクリックします。
3. **[名前]** に、フレンドリ名 **ContosoVMVault** を指定します。 複数のサブスクリプションがある場合は、適切なものを選択します。
4. リソース グループ **ContosoRG** を作成します。
5. Azure リージョンを指定します。 サポートされているリージョンを確認するには、[Azure Site Recovery の価格の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)に関するページでご利用可能な地域をご覧ください。
6. ダッシュボードからコンテナーにすばやくアクセスするには、**[ダッシュボードにピン留めする]**、**[作成]** の順にクリックします。

   ![新しいコンテナー](./media/tutorial-migrate-azure-to-azure/azure-to-azure-vault.png)

新しいコンテナーは、**[ダッシュボード]** の **[すべてのリソース]** と、メインの **[Recovery Services コンテナー]** ページに追加されます。






## <a name="select-the-source"></a>ソースを選択する

1. [Recovery Services コンテナー] で、**[ConsotoVMVault]** > **[+ レプリケート]** の順にクリックします。
2. **[ソース]** で **[Azure]** を選択します。
3. **[ソースの場所]** で、現在 VM が実行されているソースの Azure リージョンを選択します。
4. Resource Manager デプロイ モデルを選択します。 次に、**[ソース リソース グループ]** を選択します。
5. **[OK]** をクリックして設定を保存します。


## <a name="enable-replication-for-azure-vms"></a>Azure VM のレプリケーションの有効化

Site Recovery は、サブスクリプションとリソース グループに関連付けられている VM の一覧を取得します。


1. Azure Portal で **[仮想マシン]** を選択します。
2. 移行する VM を選択します。 次に、 **[OK]** をクリックします
3. **[設定]** で、**[ディザスター リカバリー]** をクリックします。
4. **[Configure disaster recovery]\(ディザスター リカバリーを構成する\)** > **[ターゲット リージョン]** で、レプリケート先のターゲット リージョンを選択します。
5. このチュートリアルでは、他の既定の設定をそのまま使用します。
6. **[レプリケーションを有効にする]** をクリックします。 これにより VM レプリケーションを有効にするジョブが開始されます。

    ![レプリケーションの有効化](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="run-a-failover"></a>フェールオーバーの実行

1. **[設定]** > **[レプリケートされたアイテム]** で、マシンをクリックしてから **[フェールオーバー]** をクリックします。
2. **[フェールオーバー]** で **[最新]** を選択します。 暗号化キー設定は、このシナリオには関係しません。
3. **[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。 Site Recovery は、フェールオーバーを開始する前にソース VM をシャットダウンしようとします。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 フェールオーバーの進行状況は **[ジョブ]** ページで確認できます。
4. 想定どおりに Azure VM が Azure に表示されることを確認します。
5. **[レプリケートされたアイテム]** で [VM] > **[コミット]** を右クリックします。 これで移行プロセスが完了します。
6. コミットが完了したら、**[Disable Replication]\(レプリケーションの無効化\)** をクリックします。  VM のレプリケーションが停止します。



## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure VM を別の Azure リージョンに移行しました。 次は、移行した VM のディザスター リカバリーを構成できます。

> [!div class="nextstepaction"]
> [移行後のディザスター リカバリーのセットアップ](azure-to-azure-quickstart.md)


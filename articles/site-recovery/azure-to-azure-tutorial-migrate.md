---
title: Azure Site Recovery サービスを使用して Azure IaaS VM を別の Azure リージョンに移動する | Microsoft Docs
description: Azure Site Recovery を使用して、異なる Azure リージョン間で Azure IaaS VM を移動します。
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: f6713326045ebd84f1cd484803fbc725ad798d7b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882269"
---
# <a name="move-azure-vms-to-another-region"></a>Azure VM を別のリージョンに移動する

既存の Azure IaaS 仮想マシンを別のリージョンに移動するシナリオとしては、[こちら](azure-to-azure-move-overview.md)に詳しく説明されているように、さまざまなケースがあります。たとえば、既存の VM の信頼性と可用性を高めることや、管理を容易にすること、またガバナンス上の理由も考えられます。 [Azure Site Recovery](site-recovery-overview.md) サービスを使用して、ビジネス継続性およびディザスター リカバリー (BCDR) の目的でオンプレミス マシンや Azure VM のディザスター リカバリーを管理よび調整する他に、Azure VM をセカンダリ リージョンに移動するためにも Site Recovery を使用できます。       

このチュートリアルでは、Azure Site Recovery を使用して、Azure VM を別のリージョンに移動する方法を説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * [前提条件を確認する](#verify-prerequisites)
> * [ソース VM を準備する](#prepare-the-source-vms)
> * [ターゲット リージョンを準備する](#prepare-the-target-region)
> * [ターゲット リージョンにデータをコピーする](#copy-data-to-the-target-region)
> * [構成をテストする](#test-the-configuration) 
> * [移動を実行する](#perform-the-move-to-the-target-region-and-confirm) 
> * [ソース リージョンのリソースを破棄する](#discard-the-resource-in-the-source-region) 

> [!IMPORTANT]
> このドキュメントでは、Azure VM をそのまま別のリージョンに移動する手順を説明しています。可用性セット内の VM を別のリージョンのゾーン固定 VM に移動することによって可用性を高めることが要件である場合は、[こちら](move-azure-VMs-AVset-Azone.md)のチュートリアルを参照してください。

## <a name="verify-prerequisites"></a>前提条件を確認する

- 移動元の Azure リージョンに Azure VM が存在する。
- 選択した[ソース リージョンとターゲット リージョンの組み合わせがサポート対象](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)であるかどうかを確認し、十分な情報を得たうえでターゲット リージョンを決定する。
- [シナリオのアーキテクチャとコンポーネント](azure-to-azure-architecture.md)を理解している。
- [サポートの制限と要件](azure-to-azure-support-matrix.md)を確認する。
- アカウントのアクセス許可を確認する。無料 Azure アカウントを作成したところであれば、自分のサブスクリプションの管理者になっています。 サブスクリプションの管理者でなければ、管理者に協力をあおぎ、必要なアクセス許可を割り当てます。 VM のレプリケーションを有効にし、基本的に Azure Site Recovery を使用してデータをコピーするには、次の要件を満たす必要があります。

    1. VM を Azure リソースに作成するアクセス許可。 '仮想マシン共同作成者' 組み込みロールには次のアクセス許可が与えられています。
        - 選択したリソース グループ内に VM を作成するためのアクセス許可
        - 選択した仮想ネットワーク内に VM を作成するためのアクセス許可
        - 選択したストレージ アカウントに書き込むためのアクセス許可

    2. Azure Site Recovery 操作を管理するためのアクセス許可も必要です。 'Site Recovery 共同作成者' ロールには、Recovery Services コンテナーでの Site Recovery の操作の管理に必要なすべてのアクセス許可があります。

## <a name="prepare-the-source-vms"></a>ソース VM を準備する

1. 移動する Azure VM に、最新のルート証明書がすべて存在することを確認します。 最新のルート証明書が存在しない場合、セキュリティ上の制約により、ターゲット リージョンへのデータ コピーを有効にすることができません。

    - Windows VM については、最新のすべての Windows 更新プログラムを VM にインストールして、すべての信頼されたルート証明書をマシンに用意します。 接続されていない環境の場合は、組織の標準の Windows Update プロセスおよび証明書更新プロセスに従ってください。
    - Linux VM の場合は、Linux ディストリビューターから提供されるガイダンスに従って、VM で最新の信頼されたルート証明書と証明書失効リストを取得します。
2. 移動しようとする VM のネットワーク接続を制御するために認証プロキシを使用していないことを確認します。
3. 移動しようとしている VM にインターネットへのアクセスが存在せず、ファイアウォール プロキシを使用してアウトバウンド アクセスを制御している場合は、[こちら](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity)で要件を確認してください。
4. ソース ネットワーク レイアウトと現在使用しているすべてのリソースを特定します (ロード バランサー、NSG、パブリック IP など)。

## <a name="prepare-the-target-region"></a>ターゲット リージョンを準備する

1. Azure サブスクリプションによって、ディザスター リカバリーに使用されるターゲット リージョンに VM を作成できることを確認します。 サポートに連絡して、必要なクォータを有効にしてください。

2. サブスクリプションに、ソース VM と一致するサイズの VM をサポートするための十分なリソースがあることを確認します。 Site Recovery を使用してターゲットにデータをコピーする場合、ターゲット VM には、同じサイズ (または可能な限り近いサイズ) が選択されます。

3. ソース ネットワーク レイアウトから特定されたすべてのコンポーネントについてターゲット リソースを作成します。 これは、ターゲット リージョンへの切り替え後、ソースにあったすべての機能を VM に確保するうえで重要となります。

    > [!NOTE]
    > ソース VM のレプリケーションを有効にすると、Azure Site Recovery によって仮想ネットワークが自動的に検出されて作成されます。または、自分でネットワークを事前に作成しておき、レプリケーションを有効にするユーザー フローの中で VM に割り当てることもできます。 ただし後述のように、その他のリソースについては、ターゲット リージョンに手動で作成する必要があります。

     最も一般的に使用されるネットワーク リソースについては、次のドキュメントを参照して、実際の環境に適したものをソース VM の構成に基づいて作成してください。

    - [ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [ロード バランサー](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [パブリック IP ](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
    その他のネットワーク コンポーネントについては、ネットワークに関する[ドキュメント](https://docs.microsoft.com/azure/#pivot=products&panel=network)を参照してください。 

4. ターゲット リージョンへの最終的な切り替えを行う前に構成をテストしたい場合は、ターゲット リージョンに手動で[非運用ネットワークを作成](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)します。 運用環境への影響が最小限で済むため、この方法をお勧めします。
    
## <a name="copy-data-to-the-target-region"></a>ターゲット リージョンにデータをコピーする
以下の手順では、Azure Site Recovery を使用してターゲット リージョンにデータをコピーする方法を説明しています。

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>任意のリージョン (ソース リージョンを除く) にコンテナーを作成します。

1. [Azure Portal](https://portal.azure.com) > **Recovery Services** にサインインします。
2. **[リソースの作成]** > **[管理ツール]** > **[Backup and Site Recovery]** の順にクリックします。
3. **[名前]** に、フレンドリ名 **ContosoVMVault** を指定します。 複数のサブスクリプションがある場合は、 適切なサブスクリプションを 1 つ選択してください。
4. リソース グループ **ContosoRG** を作成します。
5. Azure リージョンを指定します。 サポートされているリージョンを確認するには、[Azure Site Recovery の価格の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)に関するページでご利用可能な地域をご覧ください。
6. [Recovery Services コンテナー] で、**[概要]** > **[ConsotoVMVault]** > **[+ レプリケート]** の順にクリックします。
7. **[ソース]** で **[Azure]** を選択します。
8. **[ソースの場所]** で、現在 VM が実行されているソースの Azure リージョンを選択します。
9. Resource Manager デプロイ モデルを選択します。 次に、**ソース サブスクリプション**と**ソース リソース グループ**を選択します。
10. **[OK]** をクリックして設定を保存します。

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Azure VM のレプリケーションを有効にしてデータのコピーを開始する

Site Recovery は、サブスクリプションとリソース グループに関連付けられている VM の一覧を取得します。

1. 次の手順で、 移動する VM を選択します。 次に、 **[OK]** をクリックします
3. **[設定]** で、**[ディザスター リカバリー]** をクリックします。
4. **[Configure disaster recovery]\(ディザスター リカバリーを構成する\)** > **[ターゲット リージョン]** で、レプリケート先のターゲット リージョンを選択します。
5. このチュートリアルでは、他の既定の設定をそのまま使用します。
6. **[レプリケーションを有効にする]** をクリックします。 これにより VM レプリケーションを有効にするジョブが開始されます。

    ![レプリケーションの有効化](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>構成をテストする


1. コンテナーに移動し、**[設定]** > **[レプリケートされたアイテム]** で、ターゲット リージョンに移動する仮想マシンをクリックし、**[+ テスト フェールオーバー]** アイコンをクリックします。
2. **[テスト フェールオーバー]** で、フェールオーバーに使用する復旧ポイントを選択します。

   - **最後に処理があった時点**:Site Recovery サービスによって処理された最新の復旧ポイントに VM をフェールオーバーします。 タイム スタンプが表示されます。 このオプションを使用すると、データの処理に時間がかからないため、低い RTO (Recovery Time Objective: 回復時刻の目標) を提示します。
   - **最新のアプリ整合性**:このオプションでは、すべての VM が最新のアプリ整合性の復旧ポイントにフェールオーバーされます。 タイム スタンプが表示されます。
   - **カスタム**:任意の復旧ポイントを選択します。

3. Azure VM の移動先となるターゲット Azure 仮想ネットワークを選択し、構成をテストします。 

> [!IMPORTANT]
> テスト フェールオーバーには、VM の最終的な移動先となる  (レプリケーションを有効にするときにセットアップした) 運用環境のネットワークではなく、別個の Azure VM ネットワークを使用することをお勧めします。

4. 移動テストを開始するには、**[OK]** をクリックします。 進行状況を追跡するには、VM をクリックして、そのプロパティを開きます。 また、**[設定]** > **[ジョブ]** > **[Site Recovery ジョブ]** で、**テスト フェールオーバー** ジョブをクリックすることもできます。
5. フェールオーバーの完了後、レプリカの Azure VM は、Azure Portal の **[仮想マシン]** に表示されます。 VM が実行中で、適切なサイズであること、また、適切なネットワークに接続されていることを確認してください。
6. 移動テストの過程で作成した VM を削除する場合は、[レプリケートされたアイテム] の **[テスト フェールオーバーのクリーンアップ]** をクリックします。 **[メモ]** を使用して、テストに関連する観察結果をすべて記録し、保存します。

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>ターゲット リージョンへの移動を実行して確認する

1.  コンテナーに移動し、**[設定]** > **[レプリケートされたアイテム]** で仮想マシンをクリックして、**[フェールオーバー]** をクリックします。
2. **[フェールオーバー]** で **[最新]** を選択します。 
3. **[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。 Site Recovery は、フェールオーバーを開始する前にソース VM をシャットダウンしようとします。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 フェールオーバーの進行状況は **[ジョブ]** ページで確認できます。 
4. ジョブが完了したら、ターゲット Azure リージョンに VM が正しく表示されることを確認します。
5. **[レプリケートされたアイテム]** で [VM] > **[コミット]** を右クリックします。 これでターゲット リージョンへの移動プロセスは完了です。 コミット ジョブが完了するまでお待ちください。

## <a name="discard-the-resource-in-the-source-region"></a>ソース リージョンのリソースを破棄する 

1. VM に移動します。  **[レプリケーションの無効化]** をクリックします。  これで VM のデータをコピーするプロセスが停止します。  

> [!IMPORTANT]
> これは、ASR レプリケーションの請求を避けるうえで重要な手順となります。

ソース リソースを再利用する予定がない場合は、次の一連の手順に進んでください。

1. 「[ソース VM を準備する](#prepare-the-source-vms)」の手順 4. で特定した該当するネットワーク リソースをソース リージョンからすべて削除します。 
2. 対応するストレージ アカウントをソース リージョンから削除します。



## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure VM を別の Azure リージョンに移動しました。 次には、移動した VM のディザスター リカバリーを構成できます。

> [!div class="nextstepaction"]
> [移行後のディザスター リカバリーのセットアップ](azure-to-azure-quickstart.md)


---
title: Azure Site Recovery サービスを使用して Azure IaaS VM をゾーン固定 VM として別の Azure リージョンに移動する | Microsoft Docs
description: Azure Site Recovery を使用して、Azure IaaS VM を別の Azure リージョンにゾーン固定 VM として移動します。
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 6a731750da4edfb4a71c00156c5ff527dee30941
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824553"
---
# <a name="move-azure-vms-into-availability-zones"></a>Azure VM を Availability Zones に移動する
Azure の Availability Zones は、アプリケーションとデータをデータセンターの障害から保護します。 それぞれの可用性ゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。 回復性を確保するため、有効になっているリージョンにはいずれも最低 3 つのゾーンが別個に存在しています。 Availability Zones は 1 リージョン内で物理的に分離されているため、データセンターで障害が発生した場合でもアプリケーションとデータを保護できます。 Availability Zones では、Azure によって業界最高の 99.99% VM アップタイム SLA が実現されます。 可用性ゾーンは、[こちら](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones)に記載されている一部のリージョンでサポートされます。 

仮想マシンを "単一インスタンス" として特定のリージョンにデプロイしてあり、それらを可用性ゾーンに移動することで可用性を強化したい場合、その手段として Azure Site Recovery を使用することができます。 これは、さらに次のように分類できます。

- 単一インスタンスの VM をターゲット リージョン内の可用性ゾーンに移動する
- 可用性セット内の VM をターゲット リージョン内の可用性ゾーンに移動する

> [!IMPORTANT]
> 現在 Azure Site Recovery では、異なるリージョン間の VM の移動はサポートされますが、同じリージョン内での移動はサポートされません。 

## <a name="verify-prerequisites"></a>前提条件を確認する

- ターゲット リージョンが[可用性ゾーンをサポート](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones)しているかどうかを確認する。選択した[ソース リージョンとターゲット リージョンの組み合わせがサポート対象](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)であるかどうかを確認し、十分な情報を得たうえでターゲット リージョンを決定します。
- [シナリオのアーキテクチャとコンポーネント](azure-to-azure-architecture.md)を理解している。
- [サポートの制限と要件](azure-to-azure-support-matrix.md)を確認する。
- アカウントのアクセス許可を確認する。無料 Azure アカウントを作成したところであれば、自分のサブスクリプションの管理者になっています。 サブスクリプションの管理者でなければ、管理者に協力をあおぎ、必要なアクセス許可を割り当てます。 VM のレプリケーションを有効にし、最終的に Azure Site Recovery を使用してデータをターゲットにコピーするには、次の要件を満たす必要があります。

    1. VM を Azure リソースに作成するアクセス許可。 '仮想マシン共同作成者' 組み込みロールには次のアクセス許可が与えられています。
        - 選択したリソース グループ内に VM を作成するためのアクセス許可
        - 選択した仮想ネットワーク内に VM を作成するためのアクセス許可
        - 選択したストレージ アカウントに書き込むためのアクセス許可

    2. Azure Site Recovery 操作を管理するためのアクセス許可も必要です。 'Site Recovery 共同作成者' ロールには、Recovery Services コンテナーでの Site Recovery の操作の管理に必要なすべてのアクセス許可があります。

## <a name="prepare-the-source-vms"></a>ソース VM を準備する

1. Site Recovery を使用して可用性ゾーンに VM を移動する場合、その VM でマネージド ディスクが使用されている必要があります。 アンマネージド ディスクを使用する既存の Windows 仮想マシン (VM) は変換することができます。[こちら](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)の手順に従って、マネージド ディスクを使用するように VM を変換してください。 そのために、可用性セットは必ず "マネージド" として構成してください。 
2. 移動する Azure VM に、最新のルート証明書がすべて存在することを確認します。 最新のルート証明書が存在しない場合、セキュリティ上の制約により、ターゲット リージョンへのデータ コピーを有効にすることができません。

3. Windows VM については、最新のすべての Windows 更新プログラムを VM にインストールして、すべての信頼されたルート証明書をマシンに用意します。 接続されていない環境の場合は、組織の標準の Windows Update プロセスおよび証明書更新プロセスに従ってください。

4. Linux VM の場合は、Linux ディストリビューターから提供されるガイダンスに従って、VM で最新の信頼されたルート証明書と証明書失効リストを取得します。
5. 移動しようとする VM のネットワーク接続を制御するために認証プロキシを使用していないことを確認します。

6. 移動しようとしている VM にインターネットへのアクセスが存在せず、ファイアウォール プロキシを使用してアウトバウンド アクセスを制御している場合は、[こちら](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity)で要件を確認してください。

7. ソース ネットワーク レイアウトと現在使用しているすべてのリソースを、確認のために特定します (ロード バランサー、NSG、パブリック IP など)。

## <a name="prepare-the-target-region"></a>ターゲット リージョンを準備する

1. Azure サブスクリプションによって、ディザスター リカバリーに使用されるターゲット リージョンに VM を作成できることを確認します。 必要に応じてサポートに連絡し、必要なクォータを有効にしてください。

2. サブスクリプションに、ソース VM と一致するサイズの VM をサポートするための十分なリソースがあることを確認します。 Site Recovery を使用してターゲットにデータをコピーする場合、ターゲット VM には、同じサイズ (または可能な限り近いサイズ) が選択されます。

3. ソース ネットワーク レイアウトから特定されたすべてのコンポーネントについてターゲット リソースを作成します。 これは、ターゲット リージョンへの切り替え後、ソースにあったすべての機能を VM に確保するうえで重要となります。

    > [!NOTE]
    > ソース VM のレプリケーションを有効にすると、Azure Site Recovery によって仮想ネットワークとストレージ アカウントが自動的に検出されて作成されます。または、自分でこれらのリソースを事前に作成しておき、レプリケーションを有効にする過程で VM に割り当てることもできます。 ただし後述のように、その他のリソースについては、ターゲット リージョンに手動で作成する必要があります。

     最も一般的に使用されるネットワーク リソースについては、次のドキュメントを参照して、実際の環境に適したものをソース VM の構成に基づいて作成してください。

    - [ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [ロード バランサー](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials
        
     - [パブリック IP ](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
   その他のネットワーク コンポーネントについては、ネットワークに関する[ドキュメント](https://docs.microsoft.com/azure/#pivot=products&panel=network)を参照してください。 

> [!IMPORTANT]
> ターゲットでは、ゾーン冗長ロード バランサーを使用する必要があります。 詳細については、[こちら](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)を参照してください。

4. ターゲット リージョンへの最終的な切り替えを行う前に構成をテストしたい場合は、ターゲット リージョンに手動で[非運用ネットワークを作成](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)します。 運用環境への影響が最小限で済むため、この方法をお勧めします。


> [!NOTE]
> 以下の手順は、単一の VM を想定したものですが、同じ手順を複数の VM に適用することができます。その場合は、Recovery Services コンテナーに移動し、[+ レプリケート] をクリックして、対象となる VM をまとめて選択してください。

## <a name="enable-replication"></a>レプリケーションを有効にする
以下の手順では、最終的にデータを Availability Zones に移動する前に、Azure Site Recovery を使用して、ターゲット リージョンへのデータのレプリケーションを有効にする方法を説明しています。

1. Azure portal で **[仮想マシン]** をクリックして、可用性ゾーンに移動する VM を選択します。
2. **[操作]** で、**[ディザスター リカバリー]** をクリックします。
3. **[Configure disaster recovery]\(ディザスター リカバリーを構成する\)** > **[ターゲット リージョン]** で、レプリケート先のターゲット リージョンを選択します。 このリージョンが可用性ゾーンを[サポート](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones)していることを確認してください。
![enable-rep-1.PNG](media/azure-vms-to-zones/enable-rep-1.PNG)

1. **[次へ:詳細設定]** を選択します。
2. ターゲット サブスクリプション、ターゲット VM リソース グループ、仮想ネットワークに適切な値を選択します。
3. **[可用性]** セクションで、VM の移動先となる可用性ゾーンを選択します。 
> [!NOTE]
> 可用性セットまたは可用性ゾーンのオプションが表示されない場合は、[前提条件](#prepare-the-source-vms)が満たされていること、またソース VM の[準備](#prepare-the-source-vms)が完了していることを確認してください。

   ![enable-rep-2.PNG](media/azure-vms-to-zones/enable-rep-2.PNG)

7. [レプリケーションを有効にする] をクリックします。 これにより VM レプリケーションを有効にするジョブが開始されます。

## <a name="verify-settings"></a>設定の確認

レプリケーション ジョブの完了後、レプリケーションの状態を確認し、レプリケーションの設定を変更して、デプロイをテストできます。

1. VM のメニューで、**[ディザスター リカバリー]** をクリックします。
2. レプリケーションの正常性、作成された復旧ポイント、およびマップ上のソース リージョンとターゲット リージョンを確認できます。

   ![レプリケーションの状態](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>構成をテストする

1. 仮想マシンのメニューで **[ディザスター リカバリー]** をクリックします。
2. **[テスト フェールオーバー]** アイコンをクリックします。
3. **[テスト フェールオーバー]** で、フェールオーバーに使用する復旧ポイントを選択します。

   - **最後に処理があった時点**:Site Recovery サービスによって処理された最新の復旧ポイントに VM をフェールオーバーします。 タイム スタンプが表示されます。 このオプションを使用すると、データの処理に時間がかからないため、低い RTO (Recovery Time Objective: 回復時刻の目標) を提示します。
   - **最新のアプリ整合性**:このオプションでは、すべての VM が最新のアプリ整合性の復旧ポイントにフェールオーバーされます。 タイム スタンプが表示されます。
   - **カスタム**:任意の復旧ポイントを選択します。

3. Azure VM の移動先となるテスト用のターゲット Azure 仮想ネットワークを選択し、構成をテストします。 

> [!IMPORTANT]
> テスト フェールオーバーには、VM の最終的な移動先となるターゲット リージョンの運用環境のネットワークではなく、別個の Azure VM ネットワークを使用することをお勧めします。

4. 移動テストを開始するには、**[OK]** をクリックします。 進行状況を追跡するには、VM をクリックして、そのプロパティを開きます。 また、**[設定]** > **[ジョブ]** > **[Site Recovery ジョブ]** で、**テスト フェールオーバー** ジョブをクリックすることもできます。
5. フェールオーバーの完了後、レプリカの Azure VM は、Azure Portal の **[仮想マシン]** に表示されます。 VM が実行中で、適切なサイズであること、また、適切なネットワークに接続されていることを確認してください。
6. 移動テストの過程で作成した VM を削除する場合は、[レプリケートされたアイテム] の **[テスト フェールオーバーのクリーンアップ]** をクリックします。 **[メモ]** を使用して、テストに関連する観察結果をすべて記録し、保存します。

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>ターゲット リージョンへの移動を実行して確認する

1.  仮想マシンのメニューで **[ディザスター リカバリー]** をクリックします。
2. **[フェールオーバー]** アイコンをクリックします。
3. **[フェールオーバー]** で **[最新]** を選択します。 
4. **[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。 Site Recovery は、フェールオーバーを開始する前にソース VM をシャットダウンしようとします。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 フェールオーバーの進行状況は **[ジョブ]** ページで確認できます。 
5. ジョブが完了したら、ターゲット Azure リージョンに VM が正しく表示されることを確認します。
6. **[レプリケートされたアイテム]** で [VM] > **[コミット]** を右クリックします。 これでターゲット リージョンへの移動プロセスは完了です。 コミット ジョブが完了するまでお待ちください。

## <a name="discard-the-resource-in-the-source-region"></a>ソース リージョンのリソースを破棄する 

1. VM に移動します。  **[レプリケーションの無効化]** をクリックします。  これで VM のデータをコピーするプロセスが停止します。  

> [!IMPORTANT]
> これは、移動後の Site Recovery のレプリケーションに対する請求を避けるうえで重要な手順となります。 ソース レプリケーションの設定は自動的にクリーンアップされます。 レプリケーションの一部としてインストールされた Site Recovery 拡張機能は削除されないため、手動で削除する必要がある点に注意してください。 

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure VM を可用性セットまたは可用性ゾーンに移動することによってその可用性を強化しました。 次には、移動した VM のディザスター リカバリーを構成できます。

> [!div class="nextstepaction"]
> [移行後のディザスター リカバリーのセットアップ](azure-to-azure-quickstart.md)



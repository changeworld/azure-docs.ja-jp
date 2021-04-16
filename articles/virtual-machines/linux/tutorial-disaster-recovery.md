---
title: チュートリアル - Azure Site Recovery を使用して Linux VM のディザスター リカバリーを設定する
description: Azure Site Recovery サービスを使用して、別の Azure リージョンへの Linux VM のディザスター リカバリーを設定する方法について説明します。
author: rayne-wiselman
ms.service: virtual-machines
ms.collection: linux
ms.subservice: recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b5e83f883b5e1e35842ab128e4732e993fb937a0
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383695"
---
# <a name="tutorial-set-up-disaster-recovery-for-linux-virtual-machines"></a>チュートリアル:Linux 仮想マシンのディザスター リカバリーを設定する

このチュートリアルでは、Linux を実行する Azure VM のディザスター リカバリーを設定する方法について説明します。 この記事では、次のことについて説明します:

> [!div class="checklist"]
> * Linux VM のディザスター リカバリーを有効にする
> * ディザスター リカバリーのテストを実行して、期待どおりに動作することを確認する
> * 訓練後に VM のレプリケートを停止する

VM のレプリケーションを有効にすると、Site Recovery Mobility Service 拡張機能が VM にインストールされ、[Azure Site Recovery](../../site-recovery/site-recovery-overview.md) に登録されます。 レプリケーション中、VM ディスクへの書き込みは、ソース VM リージョンのキャッシュ ストレージ アカウントに送信されます。 データは、そこからターゲット リージョンに送信され、そのデータから復旧ポイントが生成されます。  ディザスター リカバリーの過程で別のリージョンに VM をフェールオーバーすると、復旧ポイントを使用してターゲット リージョンに VM が作成されます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

## <a name="prerequisites"></a>前提条件

1. ご利用の Azure サブスクリプションからターゲット リージョンに VM を作成できることを確認します。 無料の Azure アカウントを作成したばかりであれば、ご自身がサブスクリプションの管理者であり、必要なアクセス許可を持っています。
2. サブスクリプションの管理者でなければ、管理者に、次のロールの割り当てを依頼します。
    - "仮想マシン共同作成者" 組み込みロール。または次の作業を行うための個別のアクセス許可。
        - 選択した仮想ネットワーク内に VM を作成する。
        - Azure Storage アカウントに書き込む。
        - Azure マネージド ディスクに書き込む。
     - コンテナーにおける Site Recovery の操作を管理するための "Site Recovery 共同作成者" 組み込みロール。 
3. [サポート対象のオペレーティング システム](../../site-recovery/azure-to-azure-support-matrix.md#linux)が Linux VM で実行されていることを確認します。
4. VM のアウトバウンド接続で URL ベースのプロキシが使用されている場合、次の URL にアクセスできることを確認します。 認証済みプロキシの使用はサポートされていません。

    **名前** | **パブリック クラウド** | **政府機関向けクラウド** | **詳細**
    --- | --- | --- | ---
    ストレージ | `*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`| VM からソース リージョンのキャッシュ ストレージ アカウントへのデータの書き込み。 
    Azure AD  | `login.microsoftonline.com` | `login.microsoftonline.us`| Site Recovery サービスの URL に対する承認と認証。 
    レプリケーション | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`  |VM と Site Recovery サービスの通信。 
    Service Bus | `*.servicebus.windows.net` | `*.servicebus.usgovcloudapi.net` | VM から Site Recovery への監視データおよび診断データの書き込み。 

4. ネットワーク セキュリティ グループ (NSG) を使用して VM のネットワーク トラフィックを制限している場合は、次のサービス タグ (IP アドレスのグループ) を使用して VM のアウトバウンド接続 (HTTPS 443) を許可する NSG ルールを作成します。 ルールは、まずテスト NSG で試してください。

    **Tag** | **許可** 
    --- | --- 
    Storage タグ | VM からキャッシュ ストレージ アカウントへのデータの書き込みを許可します。
    Azure AD タグ | Azure AD に対応するすべての IP アドレスへのアクセスを許可します。
    EventsHub タグ | Site Recovery 監視へのアクセスを許可します。
    AzureSiteRecovery タグ | 任意のリージョンの Site Recovery Service へのアクセスを許可します。
    GuestAndHybridManagement | レプリケーションが有効な VM 上で稼働している Site Recovery モビリティ エージェントを自動的にアップグレードしたい場合に使用します。
5. VM に最新のルート証明書があることを確認します。 Linux VM で、Linux ディストリビューターから提供されるガイダンスに従い、VM で最新の信頼されたルート証明書と証明書失効リストを取得します。

## <a name="create-a-vm-and-enable-disaster-recovery"></a>VM を作成してディザスター リカバリーを有効にする

必要に応じて、VM の作成時にディザスター リカバリーを有効にできます。

1. [Linux VM を作成](quick-create-portal.md)します。
2. **[管理]** タブの **[Site Recovery]** で、 **[Enable disaster recovery]\(ディザスター リカバリーを有効にする\)** を選択します。
3. **[セカンダリ リージョン]** で、ディザスター リカバリーのために VM をレプリケートするターゲット リージョンを選択します。
4. **[Secondary subscription]\(セカンダリ サブスクリプション\)** で、ターゲット VM が作成されるターゲット サブスクリプションを選択します。 ターゲット VM は、ソース リージョンからターゲット リージョンにソース VM をフェールオーバーするときに作成されます。
5. **[Recovery Services コンテナー]** で、レプリケーションに使用するコンテナーを選択します。 コンテナーがない場合は、 **[新規作成]** を選択します。 コンテナーを配置するリソース グループと、コンテナー名を選択します。
6. **[Site Recovery policy]\(Site Recovery ポリシー\)** で、既定のポリシーをそのまま使用するか、 **[新規作成]** を選択してカスタム値を設定します。

    - 復旧ポイントは、特定の時点で取得された VM のディスクのスナップショットから作成されます。 VM をフェールオーバーするときは、復旧ポイントを使用してターゲット リージョンに VM を復元します。 
    - クラッシュ整合性復旧ポイントは、5 分ごとに作成されます。 この設定を変更することはできません。 クラッシュ整合性スナップショットでは、スナップショットが作成されたときにディスクにあったデータがキャプチャされます。 メモリ内のものは含まれません。 
    - 既定では、Site Recovery では 24 時間分のクラッシュ整合性復旧ポイントが保持されます。 0 から 72 時間の範囲でカスタム値を設定できます。
    - アプリ整合性スナップショットは、4 時間ごとに作成されます。
    - 既定では、Site Recovery では 24 時間分の復旧ポイントが格納されます。

7. **[可用性オプション]** で、VM をスタンドアロンとしてデプロイするか、可用性ゾーンまたは可用性セットにデプロイするかを指定します。

    :::image type="content" source="./media/tutorial-disaster-recovery/create-vm.png" alt-text="VM の管理プロパティのページでレプリケーションを有効にする。":::

8. VM の作成を完了します。

## <a name="enable-disaster-recovery-for-an-existing-vm"></a>既存の VM のディザスター リカバリーを有効にする

既存の VM でディザスター リカバリーを有効にする場合は、この手順を使用します。

1. Azure portal で、VM プロパティ ページを開きます。
2. **[操作]** で、 **[ディザスター リカバリー]** を選択します。

    :::image type="content" source="./media/tutorial-disaster-recovery/existing-vm.png" alt-text="既存の VM のディザスター リカバリー オプションを開く。":::

3. **[基本]** で、VM が可用性ゾーンにデプロイされている場合は、可用性ゾーン間のディザスター リカバリーを選択できます。
4. **[ターゲット リージョン]** で、VM のレプリケート先とするリージョンを選択します。 ソース リージョンとターゲット リージョンは、同じ Azure Active Directory テナントに存在している必要があります。

    :::image type="content" source="./media/tutorial-disaster-recovery/basics.png" alt-text="VM の基本的なディザスター リカバリー オプションを設定する。":::

5. **詳細設定** を選択します。
6. **[詳細設定]** では、設定を確認し、値をカスタム設定に変更できます。 既定では、Site Recovery がソース設定をミラーリングすることによってターゲット リソースが作成されます。

    - **ターゲット サブスクリプション**。 フェールオーバー後にターゲット VM が作成されるサブスクリプション。
    - **ターゲット VM リソース グループ**。 フェールオーバー後にターゲット VM が作成されるリソース グループ。
    - **ターゲット仮想ネットワーク**。 フェールオーバー後に作成されるターゲット VM が配置される Azure 仮想ネットワーク。
    - **ターゲットの可用性**。 ターゲット VM が 1 つのインスタンスとして作成されるか、可用性セット内、または可用性ゾーン内に作成されるか。
    - **近接配置グループ**。 該当する場合は、フェールオーバー後にターゲット VM が配置される近接配置グループを選択します。
    - **[ストレージの設定] の [キャッシュ ストレージ アカウント]** 。 復旧では、ソース リージョンのストレージ アカウントが一時データ ストアとして使用されます。 ソース VM の変更は、このアカウントにキャッシュされた後、ターゲットの場所にレプリケートされます。
        - 既定では、コンテナーごとに 1 つのキャッシュ ストレージ アカウントが作成され、再利用されます。
        - VM のキャッシュ アカウントをカスタマイズする場合は、別のストレージ アカウントを選択できます。
    - **ストレージ設定 - レプリカ マネージド ディスク**。 既定では、Site Recovery により、ターゲット リージョン内にレプリカ マネージド ディスクが作成されます。
        -  既定では、ターゲット マネージド ディスクが、同じ種類のストレージ (Standard HDD/SSD、または Premium SSD) を使用して、ソース VM マネージド ディスクをミラー化します。
        - 必要に応じて、ストレージの種類をカスタマイズできます。
    - **[レプリケーションの設定]** 。 VM が配置されているコンテナーと、その VM に使用されているレプリケーション ポリシーが表示されます。 既定では、VM のために Site Recovery によって作成された復旧ポイントは 24 時間保持されます。
    - **[拡張機能の設定]** 。 レプリケートする VM にインストールされた Site Recovery Mobility Service 拡張機能への更新が Site Recovery によって管理される旨が表示されます。
        - 更新プロセスは、表示されている Azure Automation アカウントによって管理されます。
        - Automation アカウントはカスタマイズできます。

    :::image type="content" source="./media/tutorial-disaster-recovery/settings-summary.png" alt-text="ターゲットとレプリケーション設定の概要を表示するページ。":::

6. **[レプリケーションを確認して開始する]** を選択します。

7. **[レプリケーションの開始]** を選択します。 デプロイが開始され、Site Recovery がターゲット リソースの作成を開始します。 レプリケーションの進行状況は、[通知] で監視できます。

    :::image type="content" source="./media/tutorial-disaster-recovery/notifications.png" alt-text="レプリケーションの進行状況の通知。":::

## <a name="check-vm-status"></a>VM の状態を確認する

レプリケーション ジョブが完了したら、VM のレプリケーションの状態を確認できます。

1. VM のプロパティ ページを開きます。
2. **[操作]** で、 **[ディザスター リカバリー]** を選択します。
3. **[要点]** セクションを展開し、コンテナー、レプリケーション ポリシー、ターゲット設定の既定値を確認します。
4. **[正常性と状態]** で、VM のレプリケーション状態、エージェントのバージョン、フェールオーバーの準備、最新の復旧ポイントに関する情報を把握します。 

    :::image type="content" source="./media/tutorial-disaster-recovery/essentials.png" alt-text="VM のディザスター リカバリーの [要点] ビュー。":::

5. **[インフラストラクチャ ビュー]** で、ソース VM とターゲット VM、マネージド ディスク、キャッシュ ストレージ アカウントの概要を視覚的に把握します。

    :::image type="content" source="./media/tutorial-disaster-recovery/infrastructure.png" alt-text="VM のディザスター リカバリーのインフラストラクチャのビジュアル マップ。":::

## <a name="run-a-drill"></a>訓練を実施する

ディザスター リカバリーが正しく機能することを確認するための訓練を実施します。 テスト フェールオーバーを実行すると、進行中のレプリケーションや運用環境に影響を及ぼすことなく、VM のコピーが作成されます。

1. VM のディザスター リカバリー ページで **[テスト フェールオーバー]** を選択します。
2. **[テスト フェールオーバー]** の [復旧ポイント] は、既定の設定である **[Latest processed (low RPO)]\(最後に処理があった時点 (低 RPO)\)** のままにします。

   これは回復ポイントの目標 (RPO) が最も短くなるオプションであり、一般に、ターゲット リージョンに最短で VM がスピンアップされます。 これは、Site Recovery サービスに送信されたすべてのデータを最初に処理して、各 VM の復旧ポイントを作成してから、それにフェールオーバーします。 この復旧ポイントには、フェールオーバーがトリガーされたときに Site Recovery にレプリケートされたすべてのデータが含まれます。

3. フェールオーバー後に VM が配置される仮想ネットワークを選択します。 

     :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-settings.png" alt-text="テスト フェールオーバーのオプションを設定するためのページ。":::

4. テスト フェールオーバーの処理が開始されます。 進行状況は [通知] で監視できます。

    :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-notification.png" alt-text="テスト フェールオーバーの通知。"::: 
    
   テスト フェールオーバーが完了すると、 **[要点]** ページに表示される VM の状態が *[テスト フェールオーバーのクリーンアップが保留中]* になります。 
  
## <a name="clean-up-resources"></a>リソースをクリーンアップする

訓練が終わると、Site Recovery によって自動的に VM がクリーンアップされます。 
 
1. 自動クリーンアップを開始するには、 **[テスト フェールオーバーのクリーンアップ]** を選択します。

    :::image type="content" source="./media/tutorial-disaster-recovery/start-cleanup.png" alt-text="[要点] ページでクリーンアップを開始する。"::: 

6. **[テスト フェールオーバーのクリーンアップ]** で、フェールオーバーに関して何か記録しておきたいメモがあれば入力し、 **[テストが完了しました。テスト フェールオーバー仮想マシンを削除してください]** を選択します。 **[OK]** をクリックします。

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test.png" alt-text="メモを記録してテスト VM を削除するためのページ。"::: 

7. 削除処理が開始されます。 進行状況は [通知] で監視できます。

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test-notification.png" alt-text="テスト VM の削除を監視するための [通知]。"::: 


### <a name="stop-replicating-the-vm"></a>VM のレプリケートを停止する

ディザスター リカバリーの訓練が完了したら、続けて完全なフェールオーバーを試してみることをお勧めします。 完全なフェールオーバーを実行したくなければ、レプリケーションを無効にすることができます。 次の処理が実行されます。

- Site Recovery のレプリケートされたマシンの一覧から VM を削除します。
- VM に対する Site Recovery の課金を停止します。
- ソース レプリケーションの設定を自動的にクリーンアップします。

レプリケーションを停止するには、次の手順に従います。

1. VM のディザスター リカバリー ページで **[レプリケーションの無効化]** を選択します。
2. **[レプリケーションの無効化]** で、レプリケーションを無効にする理由を選択します。 **[OK]** をクリックします。

    :::image type="content" source="./media/tutorial-disaster-recovery/disable-replication.png" alt-text="レプリケーションを無効にして理由を入力するためのページ。"::: 


レプリケーションの過程で VM にインストールされた Site Recovery 拡張機能は、自動的には削除されません。 VM のレプリケーションを無効にし、その後もレプリケートする予定がない場合は、次の手順に従って Site Recovery 拡張機能を手動で削除できます。 

1. [VM] > **[設定]**  >  **[拡張機能]** に移動します。
2. **[拡張機能]** ページで、Linux に該当する *Microsoft.Azure.RecoveryServices* の各エントリを選択します。
3. 拡張機能のプロパティ ページで、 **[アンインストール]** を選択します。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure VM のディザスター リカバリーを構成し、ディザスター リカバリーの訓練を実施しました。 この後は、VM に対して完全なフェールオーバーを実行できます。

> [!div class="nextstepaction"]
> [VM を別のリージョンにフェールオーバーする](../../site-recovery/azure-to-azure-tutorial-dr-drill.md)

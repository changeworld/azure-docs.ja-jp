---
title: Azure Migrate を使用して AVS 評価を作成する | Microsoft Docs
description: Azure Migrate を使用して AVS 評価を作成する方法について説明します
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 72372e6365a2535e449681549a515c3f8594f2f1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786602"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Azure VMware Solution (AVS) の評価を作成する

この記事では、Azure Migrate: Discovery and Assessment を使用して、VMware 環境のオンプレミスのサーバー に対する Azure VMware Solution (AVS) 評価を作成する方法について説明します。

[Azure Migrate](migrate-services-overview.md) は、Azure への移行に役立ちます。 Azure Migrate には、オンプレミス インフラストラクチャ、アプリケーション、およびデータの検出、評価、および Azure への移行を追跡するための一元的なハブが用意されています。 このハブには、評価および移行のための Azure ツールのほか、サードパーティの独立系ソフトウェア ベンダー (ISV) のオファリングが用意されています。

## <a name="before-you-start"></a>開始する前に

- Azure Migrate プロジェクトを[作成](./create-manage-projects.md)していることを確認します。
- プロジェクトを既に作成している場合は、Azure Migrate: Discovery and Assessment ツールを[追加](how-to-assess.md)済みであることを確認します。
- 評価を作成するには、[VMware](how-to-set-up-appliance-vmware.md) 用の Azure Migrate アプライアンスを設定する必要があります。これにより、オンプレミスのサーバーが検出され、Azure Migrate: Discovery and Assessment にメタデータとパフォーマンス データが送信されます。 [詳細については、こちらを参照してください](migrate-appliance.md)。
- コンマ区切り値 (CSV) 形式で[サーバーのメタデータをインポート](./tutorial-discover-import.md)することもできます。


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Azure VMware Solution (AVS) の評価の概要

Azure Migrate: 検出および評価を使用して作成できる評価には、次の 3 種類があります。

***評価の種類** | **詳細**
--- | --- 
**Azure VM** | オンプレミスのサーバーを Azure 仮想マシンに移行するための評価。 この評価の種類を使用すると、[VMware](how-to-set-up-appliance-vmware.md) と [Hyper-V](how-to-set-up-appliance-hyper-v.md) の環境にあるオンプレミスのサーバー、および[物理サーバー](how-to-set-up-appliance-physical.md)を Azure VM に移行するために評価できます。
**Azure SQL** | オンプレミスの SQL サーバーを VMware 環境から Azure SQL Database または Azure SQL Managed Instance に移行するための評価。
**Azure VMware Solution (AVS)** | オンプレミスのサーバーを [Azure VMware Solution (AVS)](../azure-vmware/introduction.md) に移行するための評価。 この評価の種類を使用すると、Azure VMware Solution (AVS) への移行について、[VMware 環境](how-to-set-up-appliance-vmware.md)のオンプレミス サーバーを評価できます。 [詳細情報](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Azure VMware Solution (AVS) 評価は、VMware 環境のサーバーに対してのみ作成できます。


Azure VMware Solution (AVS) の評価作成に使用できるサイズ変更の設定基準には、次の 2 種類があります。

**評価** | **詳細** | **データ**
--- | --- | ---
**パフォーマンスベース** | オンプレミスのサーバーの収集されたパフォーマンス データに基づく評価。 | **推奨ノード サイズ**:CPU およびメモリ使用率のデータと、ノードの種類、ストレージの種類、および評価用に選択した FTT の設定に基づいています。
**オンプレミス** | オンプレミスのサイズ設定に基づく評価。 | **推奨ノード サイズ**: オンプレミスのサーバーのサイズ、ノードの種類、ストレージの種類、および評価用に選択した FTT 設定に基づいています。


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Azure VMware Solution (AVS) の評価を実行する

1.  **[概要]** ページの **[Windows, Linux and SQL Server]\(Windows、Linux、SQL Server\)** で、 **[サーバーの評価と移行]** をクリックします。
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Azure Migrate の [概要] ページ":::

1. **[Azure Migrate: Discovery and assessment]\(Azure Migrate: 検出および評価\)** で、 **[評価]** をクリックします。

   ![[評価] ボタンの場所](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. **[サーバーの評価]**  >  **[評価の種類]** で、 **[Azure VMware Solution (AVS)]** を選択します。

1. **[検出ソース]** で次の操作を行います。

    - アプライアンスを使用してサーバーを検出した場合、 **[Azure Migrate アプライアンスから検出されたサーバー]** を選択します。
    - インポートした CSV ファイルを使用してサーバーを検出した場合、 **[Imported servers]\(インポートされたサーバー\)** を選択します。 
    
1. **[編集]** をクリックして、評価のプロパティを確認します。

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="評価の設定を選択するためのページ":::
 

1. **[評価のプロパティ]**  >  **[ターゲット プロパティ]** で:

    - 移行先となる Azure リージョンを **[ターゲットの場所]** で指定します。
       - サイズとコストは、指定した場所に基づいて推奨されます。
   - **[ストレージの種類]** は、既定で **[vSAN]** に設定されています。 AVS プライベート クラウドでは、これが既定のストレージの種類となります。
   - AVS ノードでは現在、**予約インスタンス** はサポートされません。
1. **[VM サイズ]** では:
    - **[ノードの種類]** は、既定で **[AV36]** に設定されています。 サーバーを AVS に移行する必要のあるノードが Azure Migrate によって推奨されます。
    - **[FTT 設定、RAID レベル]** で、許容するエラーと RAID の組み合わせを選択します。  選択した FTT オプションとオンプレミスのサーバー ディスク要件を組み合わせて、AVS で必要とされる vSAN ストレージの合計が決定されます。
    - **[CPU Oversubscription]\(CPU オーバーサブスクリプション\)** で、AVS ノードの 1 つの物理コアに関連付けられる仮想コアの比率を指定します。 オーバーサブスクリプションが 4 対 1 を上回る場合、パフォーマンスが低下する可能性がありますが、Web サーバー タイプのワークロードには使用できます。
    - **[Memory overcommit factor]\(メモリのオーバーコミット率\)** に、クラスター上のメモリのオーバーコミットの比率を指定します。 値 1 は 100% のメモリ使用量、0.5 は 50%、2 は使用可能なメモリの 200% を使用していることを表します。 0\.5 から 10 までの、最大で小数点以下 1 桁の値のみ追加できます。
    - **[Dedupe and compression factor]\(重複排除と圧縮率\)** で、ワークロードで予想される重複排除と圧縮率を指定します。 実際の値は、オンプレミスの vSAN またはストレージ構成から取得できます。これは、ワークロードによって異なる場合があります。 値 3 は 3 倍を意味し、300 GB のディスクでは 100 GB のストレージのみが使用されます。 値 1 は、重複排除も圧縮もないことを意味します。 1 から 10 までの、最大で小数点以下 1 桁の値のみを追加できます。
1. **[ノード サイズ]** で、次の操作を行います。 
    - **[サイズ変更の設定基準]** で、静的メタデータを評価の基準にするか、パフォーマンスベースのデータを評価の基準にするかを選択します。 パフォーマンス データを使用する場合:
        - 評価の基準とするデータ期間を **[パフォーマンス履歴]** で指定します。
        - パフォーマンス サンプルに使用するパーセンタイル値を **[百分位の使用率]** で指定します。 
    - 評価中に使用したいバッファーを **[快適性係数]** で指定します。 ここでは、季節ごとの使用量、短期間のパフォーマンス履歴、将来に使用量が増える可能性などの問題が考慮されます。 たとえば、快適性係数を 2 とした場合、
    
        **コンポーネント** | **有効使用率** | **快適性係数の追加 (2.0)**
        --- | --- | ---
        コア | 2  | 4
        メモリ | 8 GB | 16 GB  

1. **[価格]** では:
    - **[プラン]** には、登録されている [Azure プラン](https://azure.microsoft.com/support/legal/offer-details/)が表示されます。 評価によって、そのプランのコストが見積もられます。
    - 自分のアカウントの請求通貨を **[通貨]** で選択します。
    - Azure プランとは別に適用されるサブスクリプション固有の割引を **[割引 (%)]** に追加します。 既定の設定は 0% です。

1. 変更内容を確定する場合は **[保存]** をクリックします。

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="評価のプロパティ":::

1. **[サーバーの評価]** で **[次へ]** をクリックします。

1. **[評価するサーバーの選択]**  >  **[評価名]** で、評価の名前を指定します。 
 
1. **[グループの選択または作成]** で **[新規作成]** を選択し、グループ名を指定します。 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="グループにサーバーを追加する":::
 
1. アプライアンスを選択し、グループに追加するサーバーを選択します。 続けて、 **[次へ]** をクリックします。

1. **[評価の確認と作成]** で評価の詳細を確認したら、 **[評価の作成]** をクリックしてグループを作成し、評価を実行します。

    > [!NOTE]
    > パフォーマンスベースの評価の場合は、検出の開始後、少なくとも 1 日経ってから評価を作成することをお勧めします。 これにより、パフォーマンス データを収集する時間が確保され、信頼度が上がります。 高い信頼度レーティングを得るためには、検出の開始後、指定したパフォーマンス期間 (日、週、月) を置くのが理想です。


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Azure VMware Solution (AVS) の評価を確認する

Azure VMware Solution (AVS) の評価の内容は次のとおりです。

- **Azure VMware Solution (AVS) 対応性**: オンプレミスのサーバーが Azure VMware Solution (AVS) への移行に適しているかどうか。
- **AVS ノード数**: サーバーの実行に必要な AVS ノードの予測数。
- **すべての AVS ノードの使用率**: すべてのノードにおける CPU、メモリ、および記憶域の使用率の予測。
    - 使用率では、vCenter Server、NSX Manager (大規模)、NSX Edge など、クラスター管理オーバーヘッドが事前に考慮されます。HCX がデプロイされている場合は、HCX Manager と IX アプライアンスによる消費 (圧縮と重複除去の前の最大 44vCPU (11 CPU)、75 GB の RAM、722 GB のストレージ) も考慮されます。
- **月間コスト見積もり**: オンプレミスの VM を実行しているすべての Azure VMware Solution (AVS) ノードの月間推定コスト。


### <a name="view-an-assessment"></a>評価を表示する

1. **[Windows, Linux and SQL Server]\(Windows、Linux、SQL Server\)**  >  **[Azure Migrate: Discovery and assessment]\(Azure Migrate: 検出および評価\)** で、**[Azure VMware Solution]** の横にある数字をクリックします。

1. **[評価]** で、評価を選択して開きます。 以下はその例です (見積もりとコストはあくまで例です)。 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="AVS 評価の概要":::

1. 評価の概要を確認します。 評価のプロパティを編集して、評価を再計算することもできます。

### <a name="review-azure-vmware-solution-avs-readiness"></a>Azure VMware Solution (AVS) の対応性の確認

1. **[Azure 対応性]** で、サーバーが AVS への移行の準備ができているかどうかを確認します。

2. サーバーの状態を確認します。
    - **AVS 対応**: サーバーを変更せずにそのまま Azure (AVS) に移行できます。 AVS で起動し、AVS のフル サポートを受けます。
    - **条件付きで対応**: サーバーには、現在の vSphere バージョンとの互換性に関する問題がある可能性があります。また、サーバーの全機能を AVS で使用するには、VMware ツールやその他の設定が必要になることもあります。
    - **AVS に未対応**: このサーバーは AVS で起動しません。 たとえば、オンプレミスのサーバーに、CD-ROM などの外部デバイスが接続されていると、VMotion 操作は失敗します (VMware VMotion を使用している場合)。
    - **Readiness 不明**: オンプレミス環境から収集したメタデータが不十分なために、Azure Migrate でサーバーの対応性が判断できませんでした。

3. 推奨されるツールを確認します。
    - **VMware HCX または Enterprise**: VMware サーバーの場合、オンプレミスのワークロードを Azure VMware Solution (AVS) プライベート クラウドに移行するために推奨される移行ツールは、VMware Hybrid Cloud Extension (HCX) ソリューションです。 [詳細については、こちらを参照してください](../azure-vmware/tutorial-deploy-vmware-hcx.md)。
    - **不明**: CSV ファイルを介してインポートされたサーバーの場合、既定の移行ツールは不明です。 VMware サーバーの場合は、VMware Hybrid Cloud Extension (HCX) ソリューションを使用することをお勧めします。 

4. **[AVS 対応性]** の状態をクリックします。 VM 対応性の詳細を表示し、ドリルダウンして、コンピューティング、ストレージ、ネットワークの設定など、VM の詳細を表示できます。

### <a name="review-cost-details"></a>コスト詳細の確認

このビューには、Azure VMware Solution (AVS) でサーバーを実行する際の推定コストが表示されます。

1. 月間合計コストを確認します。 評価対象のグループ内のすべてのサーバーのコストが集計されます。 

    - コストの見積もりは、すべてのサーバーのリソース要件全体を考慮した場合に必要な AVS ノードの数に基づきます。
    - Azure VMware Solution (AVS) の料金はノードごとであるため、総コストではコンピューティング コストとストレージ コストは配分されません。
    - コストの見積もりは、オンプレミスのサーバーを AVS で実行した場合の見積もりです。 AVS 評価では、PaaS や SaaS のコストは考慮されません。
    
2. 月間ストレージ コストの見積もりを確認できます。 このビューには、評価されたグループの集計されたストレージ コストが、ストレージ ディスクの種類ごとに分けて表示されます。

3. ドリルダウンして、特定のサーバーの詳細を確認できます。


### <a name="review-confidence-rating"></a>信頼度レーティングを確認する

パフォーマンスベースの評価を実行すると、評価に信頼度レーティングが割り当てられます。

![信頼度レーティング](./media/how-to-create-assessment/confidence-rating.png)

- 1 つ星 (最低) から 5 つ星 (最高) までの評価が与えられます。
- 信頼度レーティングは、評価による推奨サイズの信頼性を判断する目安となります。
- 信頼度レーティングは、評価の計算に必要なデータ ポイントの可用性に基づいています。
- パフォーマンスベースでサイズ変更する場合は、AVS 評価に CPU およびサーバー メモリの使用率データが必要です。 次のパフォーマンス データは収集されますが、AVS 評価のサイズ設定の推奨事項では使用されません。
  - サーバーに接続されているディスクごとの、ディスク IOPS とスループット データ。
  - サーバーに接続したネットワーク アダプターごとの、パフォーマンス ベースのサイズ設定を処理するためのネットワーク I/O。

評価の信頼度レーティングは、次のとおりです。

**データ ポイントの可用性** | **信頼度レーティング**
--- | ---
0% - 20% | 1 つ星
21% - 40% | 2 つ星
41% - 60% | 3 つ星
61% - 80% | 4 つ星
81% - 100% | 5 つ星

[パフォーマンス データの詳細](concepts-azure-vmware-solution-assessment-calculation.md) 


## <a name="next-steps"></a>次のステップ

- [依存関係マッピング](how-to-create-group-machine-dependencies.md)を使用して、信頼度の高いグループを作成する方法を確認します。
- AVS 評価の計算方法の[詳細を確認](concepts-azure-vmware-solution-assessment-calculation.md)します。
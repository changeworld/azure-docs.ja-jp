---
title: Azure Migrate Server Assessment を使用して Azure VM の評価を作成する | Microsoft Docs
description: Azure Migrate Server Assessment ツールを使用して Azure VM の評価を作成する方法について説明します
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: 178bdca78c6f011c607de8e1f5d5eabcdbaab7d4
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567696"
---
# <a name="create-an-azure-vm-assessment"></a>Azure VM の評価を作成する

この記事では、オンプレミスの VMware VM または Hyper-V VM に対する Azure VM の評価を、Azure Migrate: Server Assessment を使用して作成する方法について説明します。

[Azure Migrate](migrate-services-overview.md) は、Azure への移行に役立ちます。 Azure Migrate には、オンプレミス インフラストラクチャ、アプリケーション、およびデータの検出、評価、および Azure への移行を追跡するための一元的なハブが用意されています。 このハブには、評価および移行のための Azure ツールのほか、サードパーティの独立系ソフトウェア ベンダー (ISV) のオファリングが用意されています。 

## <a name="before-you-start"></a>開始する前に

- Azure Migrate プロジェクトを[作成](./create-manage-projects.md)していることを確認します。
- プロジェクトを既に作成してある場合は、次のツールを[追加済み](how-to-assess.md)であることを確認します。Azure Migrate: Server Assessment ツールを追加済みであることを確認してください。
- 評価を作成するには、[VMware](how-to-set-up-appliance-vmware.md) または [Hyper-V](how-to-set-up-appliance-hyper-v.md) 用の Azure Migrate アプライアンスを設定する必要があります。 アプライアンスでオンプレミスのマシンが検出されて、メタデータとパフォーマンス データが Azure Migrate: Server Assessment を使用して作成する方法について説明します。 [詳細については、こちらを参照してください](migrate-appliance.md)。


## <a name="azure-vm-assessment-overview"></a>Azure VM の評価の概要
Azure Migrate: Server Assessment を使用して Azure VM の評価を作成するために使用できるサイズ設定基準には、次の 2 種類があります。Server Assessment を使用して作成する方法について説明します。

**評価** | **詳細** | **データ**
--- | --- | ---
**パフォーマンスベース** | 収集されたパフォーマンス データに基づく評価 | **推奨される VM サイズ**: CPU とメモリの使用率データに基づきます。<br/><br/> **推奨されるディスクの種類 (Standard または Premium マネージド ディスク)** : オンプレミス ディスクの IOPS とスループットに基づきます。
**オンプレミス** | オンプレミスのサイズ設定に基づく評価。 | **推奨される VM サイズ**: オンプレミスの VM サイズに基づきます<br/><br> **推奨されるディスクの種類**: 評価用に選択するストレージの種類の設定に基づきます。

評価の詳細については[こちら](concepts-assessment-calculation.md)をご覧ください。

## <a name="run-an-assessment"></a>評価を実行する

評価を実行するには次のようにします。

1. **[サーバー]** ページの **[Windows と Linux のサーバー]** で、 **[サーバーの評価と移行]** をクリックします。

   ![[サーバーの評価と移行] ボタンの場所](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. **Azure Migrate:Server Assessment** で、**評価** をクリックします。

    ![[評価] ボタンの場所](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. **[サーバーの評価]**  >  **[評価の種類]** で、 **[Azure VM]** を選択します。
4. **[検出ソース]** で次の操作を行います。

    - アプライアンスを使用してマシンを検出した場合、 **[Azure Migrate アプライアンスから検出されたマシン]** を選択します。
    - インポートした CSV ファイルを使用してマシンを検出した場合、 **[インポートされたマシン]** を選択します。 
    
1. **[編集]** をクリックして、評価のプロパティを確認します。

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vm/assessment-name.png" alt-text="評価のプロパティを確認するための [編集] ボタンの場所":::

1. **[評価のプロパティ]**  >  **[ターゲット プロパティ]** で:
    - 移行先となる Azure リージョンを **[ターゲットの場所]** で指定します。
        - サイズとコストは、指定した場所に基づいて推奨されます。 ターゲットの場所を既定値から変更した場合、**予約インスタンス** と **VM シリーズ** を指定するように求められます。
        - Azure Government では、[こちらのリージョン](migrate-support-matrix.md#supported-geographies-azure-government)から評価の対象を選択できます。
    - **[ストレージの種類]** では、
        - パフォーマンスベースのデータを評価に使用したい場合、 **[自動]** を選択すると、ディスクの IOPS とスループットに基づいて、ストレージの種類がAzure Migrate によって推奨されます。
        - または、VM に使用したいストレージの種類を移行時に選択します。
    - VM の移行時に予約インスタンスを使用するかどうかを **[予約済みインスタンス]** で指定します。
        - 予約インスタンスを使用するように選択した場合は、 **[割引 (%)]** や **[VM のアップタイム]** は指定できません。 
        - [詳細については、こちらを参照してください](https://aka.ms/azurereservedinstances)。
 1. **[VM サイズ]** では:
     - **[サイズ変更の設定基準]** で、マシン構成データ (またはメタデータ) とパフォーマンスベースのデータのどちらを評価の基準にするかを選択します。 パフォーマンス データを使用する場合:
        - 評価の基準とするデータ期間を **[パフォーマンス履歴]** で指定します。
        - パフォーマンス サンプルに使用するパーセンタイル値を **[百分位の使用率]** で指定します。 
    - 検討したい Azure VM シリーズを **[VM シリーズ]** で指定します。
        - パフォーマンスベースの評価を使用する場合、Azure Migrate によって自動的に値が提示されます。
        - 必要に応じて設定を調整します。 たとえば、Azure 内に A シリーズの VM を必要とする運用環境がない場合、シリーズの一覧から A シリーズを除外することができます。
    - 評価中に使用したいバッファーを **[快適性係数]** で指定します。 ここでは、季節ごとの使用量、短期間のパフォーマンス履歴、将来に使用量が増える可能性などの問題が考慮されます。 たとえば、快適性係数を 2 とした場合、
    
        **コンポーネント** | **有効使用率** | **快適性係数の追加 (2.0)**
        --- | --- | ---
        コア | 2  | 4
        メモリ | 8 GB | 16 GB
   
1. **[価格]** では:
    - [Azure プラン](https://azure.microsoft.com/support/legal/offer-details/)に登録している場合、 **[プラン]** にそれを指定します。 Server Assessment はそのプランのコストを見積もります。
    - 自分のアカウントの請求通貨を **[通貨]** で選択します。
    - Azure プランとは別に適用されるサブスクリプション固有の割引を **[割引 (%)]** に追加します。 既定の設定は 0% です。
    - **[VM のアップタイム]** に、VM が実行される期間 (1 か月あたりの日数、1 日あたりの時間数など) を指定します。
        - これは、継続的には実行されない Azure VM に便利です。
        - コストの見積もりは、指定した期間に基づいて算出されます。
        - 既定値は、1 か月あたり 31 日、1 日あたり 24 時間です。
    - Enterprise Agreement (EA) サブスクリプションの割引をコスト見積もりの計算に含めるかどうかを **[EA サブスクリプション]** で指定します。 
    - Windows Server ライセンスを既に所有しているかどうかを **[Azure ハイブリッド特典]** で指定します。 ライセンスを所有しており、アクティブな Windows Server サブスクリプションのソフトウェア アシュアランスの対象となっている場合は、ライセンスを Azure に持ち込む際に [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-use-benefit/)を申請することができます。

1. 変更内容を確定する場合は **[保存]** をクリックします。

    ![評価のプロパティ](./media/tutorial-assess-vmware-azure-vm/assessment-properties.png)

1. **[サーバーの評価]** で **[次へ]** をクリックします。

1. **[評価するマシンの選択]**  >  **[評価名]** で、評価の名前を指定します。 

1. **[グループの選択または作成]** で **[新規作成]** を選択し、グループ名を指定します。 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vm/assess-group.png" alt-text="VM をグループに追加する":::

1. アプライアンスを選択し、グループに追加したい VM を選択します。 続けて、 **[次へ]** をクリックします。


1. **[評価の確認と作成]** で評価の詳細を確認したら、 **[評価の作成]** をクリックしてグループを作成し、評価を実行します。

1. 評価が作成されたら、それを表示します ( **[サーバー]**  >  **[Azure Migrate: Server Assessment]**  >  **[評価]** )。

1. **[評価のエクスポート]** をクリックし、Excel ファイルとしてダウンロードします。
    > [!NOTE]
    > パフォーマンスベースの評価の場合は、検出の開始後、少なくとも 1 日経ってから評価を作成することをお勧めします。 これにより、パフォーマンス データを収集する時間が確保され、信頼度が上がります。 高い信頼度レーティングを得るためには、検出の開始後、指定したパフォーマンス期間 (日、週、月) を置くのが理想です。


## <a name="review-an-azure-vm-assessment"></a>Azure VM の評価を確認する

Azure VM の評価で評される内容は次のとおりです。

- **Azure 対応性**: VM が Azure への移行に適しているかどうか。
- **月間コスト見積もり**: Azure で VM を実行するためのコンピューティングとストレージの月間推定コスト。
- **月間ストレージ コスト見積もり**: 移行後のディスク ストレージの推定コスト。

### <a name="view-an-azure-vm-assessment"></a>Azure VM の評価を表示する

1. **[移行の目標]**  >   **[サーバー]** で、 **[評価]** を **[Azure Migrate: Server Assessment]** でクリックします。
2. **[評価]** で、評価をクリックして開きます。

    ![評価の概要](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure 対応性の確認

1. **[Azure 対応性]** で、VM が Azure への移行の準備ができているかどうかを確認します。
2. VM の状態を確認します。
    - **[Azure に対応]** : Azure Migrate によって、評価内の VM の VM サイズとコスト見積もりが推奨されます。
    - **[条件付きで対応]** : 問題と推奨される修復方法が示されます。
    - **[Azure に未対応]** : 問題と推奨される修復方法が示されます。
    - **[Readiness 不明]** : データの可用性の問題により、Azure Migrate が対応性を評価できない場合に使用されます。

3. **[Azure 対応性]** の状態をクリックします。 VM 対応性の詳細を表示し、ドリルダウンして、コンピューティング、ストレージ、ネットワークの設定など、VM の詳細を表示できます。



### <a name="review-cost-details"></a>コスト詳細の確認

このビューには、Azure で VM を実行する際のコンピューティングおよびストレージの推定コストが表示されます。

1. 月間のコンピューティングおよびストレージのコストを確認します。 コストは、評価されるグループ内のすべての VM について集計されます。

    - コストの見積もりは、マシンの推奨サイズ、およびそのディスクとプロパティに基づいています。
    - コンピューティングとストレージの月間推定コストが表示されます。
    - コストの見積もりは、IaaS VM としてのオンプレミス VM の実行に対するものです。 Azure Migrate Server Assessment では、PaaS または SaaS のコストは考慮されません。

2. 月間ストレージ コストの見積もりを確認できます。 このビューには、評価されたグループの集計されたストレージ コストが、ストレージ ディスクの種類ごとに分けて表示されます。
3. ドリルダウンして、特定の VM の詳細を見ることができます。


### <a name="review-confidence-rating"></a>信頼度レーティングを確認する

パフォーマンスベースの評価を実行すると、評価に信頼度レーティングが割り当てられます。

![信頼度レーティング](./media/how-to-create-assessment/confidence-rating.png)

- 1 つ星 (最低) から 5 つ星 (最高) までの評価が与えられます。
- 信頼度レーティングは、評価による推奨サイズの信頼性を判断する目安となります。
- 信頼度レーティングは、評価の計算に必要なデータ ポイントの可用性に基づいています。

評価の信頼度レーティングは、次のとおりです。

**データ ポイントの可用性** | **信頼度レーティング**
--- | ---
0% - 20% | 1 つ星
21% - 40% | 2 つ星
41% - 60% | 3 つ星
61% - 80% | 4 つ星
81% - 100% | 5 つ星




## <a name="next-steps"></a>次のステップ

- [依存関係マッピング](how-to-create-group-machine-dependencies.md)を使用して、信頼度の高いグループを作成する方法を確認します。
- 評価の計算方法の[詳細を確認](concepts-assessment-calculation.md)します。
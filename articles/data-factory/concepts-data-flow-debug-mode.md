---
title: マッピング データ フローのデバッグ モード
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Synapse Analytics を使用してデータ フローの構築時に対話型デバッグ セッションを開始します。
ms.author: makromer
author: kromerm
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 10/01/2021
ms.openlocfilehash: 8d052be49a545e34ce44dc53174a5dbca7c3d220
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2021
ms.locfileid: "129402968"
---
# <a name="mapping-data-flow-debug-mode"></a>マッピング データ フローのデバッグ モード

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>概要

Azure Data Factory および Synapse Analytics のマッピング データ フローのデバッグ モードを使用すると、データ フローの構築およびデバッグ時にデータ シェイプの変換を対話的に監視できます。 デバッグ セッションは、Data Flow のデザイン セッションでも、データ フローのパイプライン デバッグ実行中でも使用できます。 デバッグ モードを有効にするには、データ フロー アクティビティがある状態で、データ フロー キャンバスまたはパイプライン キャンバスの上部のバーにある **[Data Flow のデバッグ]** ボタンを使用します。

:::image type="content" source="media/data-flow/debug-button.png" alt-text="[デバッグ] スライダーの場所を示すスクリーンショット 1":::

:::image type="content" source="media/data-flow/debug-button-4.png" alt-text="[デバッグ] スライダー の場所を示すスクリーンショット 2":::

スライダーを有効にすると、使用する統合ランタイム構成を選択するように求めるメッセージが表示されます。 AutoResolveIntegrationRuntime を選択した場合は、既定の 60 分の Time to Live が設定された一般的なコンピューティングの 8 個のコアを持つクラスターがスピンアップされます。 セッションのタイムアウトまでのアイドル時間を増やす場合は、より高い TTL 設定を選択できます。 データ フロー統合ランタイムの詳細については、「[Integration Runtime のパフォーマンス](concepts-integration-runtime-performance.md)」を参照してください。

:::image type="content" source="media/data-flow/debug-new-1.png" alt-text="デバッグ IR の選択":::

デバッグ モードが有効なときは、アクティブな Spark クラスターを使用して対話的にデータ フローを構築します。 デバッグを無効にすると、セッションは終了します。 デバッグ セッションを有効にしている間に Data Factory によって発生する 1 時間あたりの料金を把握しておく必要があります。

ほとんどの場合は、デバッグ モードでデータ フローを構築し、ビジネス ロジックを検証してデータ変換を確認してから、作業内容を公開することをお勧めします。 パイプライン パネルの [デバッグ] ボタンを使用して、パイプライン内部のデータ フローをテストします。

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
:::image type="content" source="media/iterative-development-debugging/view-dataflow-debug-sessions.png" alt-text="データ フロー デバッグ セッションの表示":::

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)
:::image type="content" source="media/iterative-development-debugging/view-dataflow-debug-sessions-synapse.png" alt-text="データ フロー デバッグ セッションの表示":::

---

> [!NOTE]
> ユーザーがブラウザー UI から開始するすべてのデバッグ セッションは、独自の Spark クラスターを使用する新しいセッションです。 上記のデバッグ セッションの監視ビューを使用して、デバッグ セッションを表示および管理できます。 TTL 時間を含め、各デバッグ セッションの実行時間に対し、時間単位で課金されます。

## <a name="cluster-status"></a>クラスターの状態

クラスターのデバッグの準備が整うと、デザイン サーフェスの上部にあるクラスター状態インジケーターが緑色に変わります。 クラスターが既に実行されている場合、緑色のインジケーターはほぼ瞬時に表示されます。 デバッグ モードに入ったときにクラスターがまだ実行されていなかった場合、Spark クラスターではコールド ブートが実行されます。 インジケーターは、対話型デバッグのための環境の準備が整うまでスピンします。

デバッグが終了したら、[デバッグ] スイッチをオフにして、Spark クラスターを終了できるようにし、デバッグ アクティビティについて課金されないようにします。

## <a name="debug-settings"></a>デバッグの設定

デバッグ モードを有効にすると、データ フローでデータをプレビューする方法を編集できます。 デバッグ設定は、Data Flow のキャンバス ツール バーにある [Debug Settings] (デバッグ設定) をクリックすることで編集できます。 ここで、各ソース変換に使用する行制限やファイル ソースを選択できます。 この設定の行制限の対象となるのは、現在のデバッグ セッションのみです。 Azure Synapse Analytics ソースに使用するステージング リンク サービスを選択することもできます。 

:::image type="content" source="media/data-flow/debug-settings.png" alt-text="デバッグ設定":::

データフローまたは参照されているデータセットにパラメーターがある場合は、**[パラメーター]** タブを選択して、デバッグ中に使用する値を指定できます。

ここでサンプリング設定を使用して、データのサンプル ファイルまたはサンプル テーブルを参照します。これにより、ソース データセットを変更する必要がなくなります。 ここでサンプル ファイルまたはテーブルを使用することで、データのサブセットに対してテストを行うときに、データ フロー内で同じロジックやプロパティの設定を維持することができます。

:::image type="content" source="media/data-flow/debug-settings2.png" alt-text="デバッグ設定のパラメーター":::

データ フローでデバッグ モードに使用される既定の IR は、4 コアの単一ドライバー ノードを持つ小さな 4 コアのシングル ワーカー ノードです。 これは、データ フロー ロジックをテストするときの小さなデータ サンプルで問題なく動作します。 データ プレビューの間にデバッグ設定で行の制限を拡張する場合、またはパイプライン デバッグの間にソースでサンプリングされる行数に高い値を設定する場合は、新しい Azure Integration Runtime でさらに大きいコンピューティング環境を設定することを検討できます。 その後、より大きなコンピューティング環境を使用してデバッグ セッションを再開できます。

## <a name="data-preview"></a>データのプレビュー

デバッグが有効な場合、下部のパネルに [Data Preview]\(データのプレビュー\) タブが表示されます。 デバッグが有効ではない場合、Data Flow では、各変換に含まれる、または含まれない現在のメタデータのみが [Inspect]\(検査\) タブに表示されます。データのプレビューでは、デバッグ設定で制限として設定した行数のみのクエリが実行されます。 **[最新の情報に更新]** をクリックすると、データのプレビューがフェッチされます。

:::image type="content" source="media/data-flow/datapreview.png" alt-text="データのプレビュー":::

> [!NOTE]
> ファイル ソースでは、読み取られる行ではなく、表示される行のみを制限します。 データセットが非常に大規模な場合は、そのファイルのごく一部を取得して、ご自身のテストに使用することをお勧めします。 [Debug Settings]\(デバッグ設定\) では、ファイル データセットの種類のソースごとに一時ファイルを選択できます。

デバッグ モードで Data Flow を実行している場合、データはシンク変換に書き込まれません。 デバッグ セッションは、変換のテスト ハーネスとして機能することを目的としています。 シンクはデバッグ中には必要ではなく、データ フローでは無視されます。 シンクへのデータの書き込みをテストする場合は、パイプラインから Data Flow を実行し、パイプラインからデバッグの実行を使用します。

データ プレビューは、Spark メモリ内のデータ フレームからの、行制限とデータ サンプリングを使用して変換されたデータのスナップショットです。 そのため、このシナリオでは、シンク ドライバーは使用またはテストされません。

### <a name="testing-join-conditions"></a>結合条件のテスト

Join、Exists、または Lookup の変換を単体テストする場合、ご自身のテストには必ず小規模な既知のデータを使用するようにしてください。 上述の [Debug Settings]\(デバッグ設定\) オプションを使用すると、ご自身のテストで使用する一時ファイルを設定できます。 これは、大規模なデータセットから行を制限したり、サンプリングしたりするときに、どの行およびどのキーがテストのフローに読み込まれるかを予測できないために必要です。 結果は非決定論的であり、ご自身の結合条件が失敗する可能性があります。

### <a name="quick-actions"></a>クイック アクション

データのプレビューが表示されたら、クイック変換を生成して、列に対する型キャスト、削除、または変更を行うことができます。 列見出しをクリックした後、[データのプレビュー] ツールバーからいずれかのオプションを選択します。

:::image type="content" source="media/data-flow/quick-actions1.png" alt-text="スクリーンショットには、次のオプションが含まれる [データのプレビュー] ツールバーが示されています:[型キャスト]、[変更]、[統計]、および [削除]。":::

[変更] を選択すると、データのプレビューがすぐに更新されます。 新しい変換を生成するには、右上隅にある **[確認]** をクリックします。

:::image type="content" source="media/data-flow/quick-actions2.png" alt-text="スクリーンショットには [確認] ボタンが示されています。":::

**[型キャスト]** と **[変更]** では派生列変換が生成され、**[削除]** では選択変換が生成されます。

:::image type="content" source="media/data-flow/quick-actions3.png" alt-text="スクリーンショットには、派生列の設定が示されています。":::

> [!NOTE]
> データフローを編集する場合は、クイック変換を追加する前にデータのプレビューを再フェッチする必要があります。

### <a name="data-profiling"></a>データ プロファイル

[データのプレビュー] タブで列を選択し、[データのプレビュー] ツール バーで **[統計]** をクリックすると、データ グリッドの右端にグラフがポップアップ表示され、各フィールドに関する詳細な統計情報が示されます。 このサービスでは、表示するグラフの種類のデータ サンプリングに基づいて判断が下されます。 カーディナリティが高いフィールドの既定は NULL/NOT NULL グラフです。一方、カーディナリティが低いカテゴリ データや数値データでは、データ値の頻度を示す棒グラフが表示されます。 また、文字列フィールドの最大/長さ、数値フィールドの最小/最大値、標準偏差、百分位、カウント、および平均も表示されます。

:::image type="content" source="media/data-flow/stats.png" alt-text="列の統計":::

## <a name="next-steps"></a>次の手順

* データ フローの構築とデバッグが完了したら、[パイプラインからデータ フローを実行します。](control-flow-execute-data-flow-activity.md)
* データ フローでパイプラインをテストする場合は、パイプラインの[デバッグ実行の実行オプションを使用します。](iterative-development-debugging.md)

---
title: Azure Security Center のブック ギャラリー
description: 統合された Azure Monitor ブック ギャラリーを使用して、Azure Security Center データの豊富な対話型レポートを作成する方法を学習します
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 07/07/2021
ms.openlocfilehash: 2d145e62392ee95189a212c1cd0b294134ce1109
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739075"
---
# <a name="create-rich-interactive-reports-of-security-center-data"></a>Security Center データの豊富な対話型レポートを作成する

[Azure Monitor ブック](../azure-monitor/visualize/workbooks-overview.md)には、Azure portal 内でデータを分析し、豊富なビジュアル レポートを作成するための柔軟なキャンバスが用意されています。 Azure 全体から複数のデータ ソースを活用し、それらを結合して、統合された対話型エクスペリエンスにすることができます。

ブックには、Azure データを視覚化するための豊富な機能セットが用意されています。 各視覚化の種類の詳細な例については、[視覚化の例とドキュメント](../azure-monitor/visualize/workbooks-text-visualizations.md)を参照してください。 

Azure Security Center 内で、組み込みのブックにアクセスして組織のセキュリティ体制を追跡できます。 また、カスタム ブックを作成し、Security Center またはその他のサポートされているデータ ソースからのさまざまなデータを表示することもできます。

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="経過時間に応じたセキュア スコアのブック。":::

## <a name="availability"></a>可用性

| 側面                          | 詳細                                                                                                                                      |
|---------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| リリース状態:                  | 一般公開 (GA)                                                                                                                    |
| 価格:                        | Free                                                                                                                                         |
| 必要なロールとアクセス許可: | ブックを保存するには、少なくともターゲット リソース グループに対する[ブックの共同作成者](../role-based-access-control/built-in-roles.md#workbook-contributor)のアクセス許可が必要です |
| クラウド:                         | :::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/yes-icon.png"::: ナショナル/ソブリン (Azure Government、Azure China 21Vianet) |
|                                 |                                                                                                                                              |

## <a name="workbooks-gallery-in-azure-security-center"></a>Azure Security Center のブック ギャラリー

統合された Azure ブック機能を使用すると、Azure Security Center で独自のカスタム対話型ブックを簡単に作成できます。 Security Center には、カスタマイズ可能な以下のブックを含むギャラリーも含まれています。

- ['経過時間に応じたセキュア スコア' ブック](#use-the-secure-score-over-time-workbook) - サブスクリプションのスコアと、リソースに対する推奨事項の変化を追跡します
- ['システムの更新プログラム' ブック](#use-the-system-updates-workbook) - リソース、OS、重要度などに応じて、不足しているシステムの更新プログラムを表示します
- ['脆弱性評価の結果' ブック](#use-the-vulnerability-assessment-findings-workbook) - Azure リソースを対象とした脆弱性スキャンの結果を表示します
- ['経過時間に応じたコンプライアンス' ブック](#use-the-compliance-over-time-workbook) - 選択した規制または業界標準に対するサブスクリプションのコンプライアンスの状態を表示します 

:::image type="content" source="media/custom-dashboards-azure-workbooks/workbooks-gallery-security-center.png" alt-text="Azure Security Center の組み込みブックのギャラリー。":::

提供されているブックのいずれかを選ぶか、独自のものを作成します。

> [!TIP]
> **[編集]** ボタンを使用して、提供されているブックのいずれかを満足のいくまでカスタマイズします。 編集が完了したら、 **[保存]** を選択します。これにより、変更内容が新しいブックに保存されます。
> 
> :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-supplied-workbooks.png" alt-text="提供されているブックを編集し、特定のニーズに合わせてカスタマイズする。":::

### <a name="use-the-secure-score-over-time-workbook"></a>'経過時間に応じたセキュア スコア' ブックを使用する

このブックでは、Log Analytics ワークスペースからのセキュア スコア データを使用します。 このデータは連続エクスポート ツールからエクスポートする必要があります。詳細については、「[Azure portal で Security Center のページから連続エクスポートを構成する](continuous-export.md?tabs=azure-portal)」を参照してください。

連続エクスポートを設定するときに、**ストリーミング更新** と **スナップショット** の両方にエクスポートの頻度を設定します。

:::image type="content" source="media/custom-dashboards-azure-workbooks/export-frequency-both.png" alt-text="経過時間に応じたセキュア スコア ブックでは、連続エクスポート構成のエクスポートの頻度設定から、これらのオプションの両方を選択する必要があります。":::

> [!NOTE]
> スナップショットは週単位でエクスポートされるので、このブックでデータを表示するには、最初のスナップショットがエクスポートされるまで少なくとも 1 週間待つ必要があります。

> [!TIP]
> 組織全体で連続エクスポートを構成するには、[大規模な連続エクスポートの構成](continuous-export.md?tabs=azure-policy)に関するページで説明されている指定された Azure Policy の "DeployIfNotExist" ポリシーを使用します。

経過時間に応じたセキュア スコア ブックには、選択されたワークスペースに報告されるサブスクリプションのグラフが 5 つあります。

|グラフ  |例  |
|---------|---------|
|**過去 1 週間と 1 か月のスコア傾向**<br>このセクションを使用して、サブスクリプションのスコアの現在のスコアと全般的な傾向を監視します。|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-1.png" alt-text="組み込みブックでのセキュア スコアの傾向。":::|
|**選択されたすべてのサブスクリプションの集計スコア**<br>傾向線の任意の位置にマウスポインターを合わせると、選択された時間範囲の任意の日付の集計されたスコアが表示されます。|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-2.png" alt-text="選択されたすべてのサブスクリプションの集計スコア。":::|
|**最も多く異常な状態になったリソースに関する推奨事項**<br>このテーブルは、選択された期間に最も多く異常な状態になったリソースに関する推奨事項をトリアージするのに役立ちます。|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-3.png" alt-text="最も多く異常な状態になったリソースに関する推奨事項。":::|
|**特定のセキュリティ コントロールのスコア**<br>Security Center のセキュリティ コントロールは、推奨事項を論理的にグループ化したものです。 このグラフを見れば、すべてのコントロールの週単位のスコアが一目でわかります。|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-4.png" alt-text="選択された期間のセキュリティ コントロールのスコア。":::|
|**リソースの変化**<br>選択された期間中に状態 (正常、異常、または該当なし) が最も多く変化したリソースに関する推奨事項は、ここに一覧表示されています。 一覧から任意の推奨事項を選択して、特定のリソースを一覧表示する新しいテーブルを開きます。|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-5.png" alt-text="正常性状態が最も多く変化したリソースに関する推奨事項。":::|

### <a name="use-the-system-updates-workbook"></a>'システムの更新プログラム' ブックを使用する

このブックは、"システムの更新プログラムをマシンにインストールする必要がある" というセキュリティ推奨事項に基づくものです。

このブックは、未完了の更新プログラムがあるコンピューターを特定するのに役立ちます。

以下に応じて、選択されたサブスクリプションの状況を確認できます。

- 更新プログラムが未完了のリソースの一覧
- リソースに適用されていない更新プログラムの一覧

:::image type="content" source="media/custom-dashboards-azure-workbooks/system-updates-report.png" alt-text="不足している更新プログラムのセキュリティ推奨事項に基づく Security Center のシステム更新プログラム ブック":::

### <a name="use-the-vulnerability-assessment-findings-workbook"></a>'脆弱性評価の結果' ブックを使用する

Security Center には、コンピューター、コンテナー レジストリ内のコンテナー、および SQL サーバーの脆弱性スキャナーが含まれています。

これらのスキャナーの使用の詳細については、以下を参照してください。

- [統合 VA スキャナーでコンピューターをスキャンする](deploy-vulnerability-assessment-vm.md)
- [レジストリ イメージの脆弱性をスキャンする](defender-for-container-registries-usage.md)
- [SQL リソースの脆弱性をスキャンする](defender-for-sql-on-machines-vulnerability-assessment.md)

これらの各スキャナーの結果は、別の推奨事項で報告されます。

- 仮想マシンの脆弱性を修復する必要がある
- Azure Container Registry イメージの脆弱性を修復する必要がある (Qualys を利用)
- SQL データベースの脆弱性評価の結果を修復する必要がある
- マシン上の SQL サーバーの脆弱性評価の結果を修復する必要がある

このブックではこれらの結果が収集され、重要度、リソースの種類、カテゴリ別に整理されます。

:::image type="content" source="media/custom-dashboards-azure-workbooks/vulnerability-assessment-findings-report.png" alt-text="Security Center の脆弱性評価の結果レポート。":::


### <a name="use-the-compliance-over-time-workbook"></a>'経過時間に応じたコンプライアンス' ブックを使用する

Azure Security Center では、リソースの構成が業界標準、規制、ベンチマークの要件と継続的に比較されます。 組み込みの標準には、NIST SP 800-53、SWIFT CSP CSCF v2020、カナダ連邦 PBMM、HIPAA HITRUST などがあります。 規制コンプライアンス ダッシュボードを使用して、自分の組織に該当する特定の標準を選択できます。 詳細については、「[規制コンプライアンス ダッシュボードで標準セットをカスタイマイズする](update-regulatory-compliance-packages.md)」を参照してください。

このブックを使用すると、ダッシュボードに追加したさまざまな標準を使用して、経過時間に応じたコンプライアンスの状態を追跡できます。

:::image type="content" source="media/custom-dashboards-azure-workbooks/compliance-over-time-select-standards.png" alt-text="経過時間に応じたコンプライアンス レポートに使用する標準を選択します。":::

レポートの概要の領域から標準を選択すると、下部のペインにより詳しい内訳が表示されます。

:::image type="content" source="media/custom-dashboards-azure-workbooks/compliance-over-time-details.png" alt-text="特定の標準に関する変更の詳しい内訳。":::

推奨事項のレベルまでドリルダウンし続け、各コントロールに合格または不合格となったリソースを表示できます。 

> [!TIP]
> レポートの各パネルに対して、[Excel にエクスポート] オプションを使用してデータを Excel にエクスポートできます。
>
> :::image type="content" source="media/custom-dashboards-azure-workbooks/export-workbook-data.png" alt-text="コンプライアンス ブックのデータを Excel にエクスポートする。":::


## <a name="import-workbooks-from-other-workbook-galleries"></a>他のブック ギャラリーからブックをインポートする

他の Azure サービスでブックをビルドし、それらを Azure Security Center ブック ギャラリーに移動する場合は、次のようにします。

1. ターゲットのブックを開きます。

1. ツール バーから、 **[編集]** を選択します。

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks.png" alt-text="Azure Monitor ブックの編集。":::

1. ツール バーから、 **[</>]** を選択して詳細エディターを起動します。

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-advanced-editor.png" alt-text="ギャラリー テンプレート JSON コードを取得するために詳細エディターを起動する。":::

1. ブックのギャラリー テンプレート JSON をコピーします。

1. Security Center でブック ギャラリーを開き、メニュー バーから **[新規]** を選択します。
1. **[</>]** を選択して詳細エディターを起動します。
1. ギャラリー テンプレート JSON 全体を貼り付けます。
1. **[適用]** を選択します。
1. ツール バーから、 **[名前を付けて保存]** を選択します。

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-save-as.png" alt-text="Security Center でギャラリーにブックを保存する。":::

1. ブックを保存するために必要な詳細を入力します。
   1. ブックの名前
   2. 目的のリージョン
   3. サブスクリプション、リソース グループ、および共有 (必要に応じて)。

保存されたブックは、 **[最近変更されたブック]** カテゴリに表示されます。


## <a name="next-steps"></a>次のステップ

この記事では、組み込みレポートと、独自のカスタム対話型レポートを作成するオプションを含む Security Center の統合された Azure Monitor ブック ページについて説明しました。

- [Azure Monitor ブック](../azure-monitor/visualize/workbooks-overview.md)についてさらに学習します
- 組み込みブックには Security Center の推奨事項からデータがプルされます。 「[セキュリティの推奨事項 - リファレンス ガイド](recommendations-reference.md)」の多くのセキュリティ推奨事項について学習します
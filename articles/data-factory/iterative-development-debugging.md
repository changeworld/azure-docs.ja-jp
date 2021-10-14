---
title: 反復開発とデバッグ
titleSuffix: Azure Data Factory & Azure Synapse
description: サービス UI を利用し、Data Factory と Synapse Analytics のパイプラインを対話式で開発し、デバッグする方法について説明します。
ms.date: 09/09/2021
ms.topic: conceptual
ms.service: data-factory
ms.subservice: authoring
ms.custom: synapse
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: kromerm
ms.author: makromer
ms.openlocfilehash: 343cdb521f3a984d7d80022940a75afbb7032b6e
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208071"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory-and-synapse-analytics-pipelines"></a>Data Factory と Synapse Analytics のパイプラインによる反復開発とデバッグ
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory と Synapse Analytics では、パイプラインの反復開発とデバッグをサポートしています。 これらの機能を使用すると、行った変更をテストした後で、pull request を作成したり、変更をサービスに公開したりできます。 

この機能の概要とデモンストレーションについては、以下の 8 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="debugging-a-pipeline"></a>パイプラインのデバッグ

パイプライン キャンバスを使用して作成するときは、**デバッグ** 機能を使用してアクティビティをテストできます。 テストの実行を行うときは、 **[デバッグ]** を選択する前に、サービスに変更を発行する必要はありません。 この機能は、ワークフローを更新する前に、変更が期待どおりに動作することを確認する場合に便利です。

:::image type="content" source="media/iterative-development-debugging/iterative-development-1.png" alt-text="パイプライン キャンバスのデバッグ機能":::

パイプラインが実行中の間は、パイプライン キャンバスの **[出力]** タブで各アクティビティの結果を確認できます。

テストの実行結果は、パイプライン キャンバスの **出力** ウィンドウに表示されます。

:::image type="content" source="media/iterative-development-debugging/iterative-development-2.png" alt-text="パイプライン キャンバスの出力ウィンドウ":::

テストの実行が成功したら、パイプラインにさらにアクティビティを追加し、反復的な方法でデバッグを続行します。 テストの実行中に、実行を **[キャンセル]** することもできます。

> [!IMPORTANT]
> **[デバッグ]** を選択すると、パイプラインが実際に実行されます。 たとえばパイプラインにコピー アクティビティが含まれていれば、テストの実行では、データがコピー元からコピー先にコピーされます。 その結果、デバッグ時のコピー アクティビティとその他のアクティビティでは、テスト フォルダーを使用することをお勧めします。 パイプラインのデバッグが終わったら、通常の操作で使用する実際のフォルダーに切り替えます。

### <a name="setting-breakpoints"></a>ブレークポイントの設定

このサービスを使用すると、パイプライン キャンバスの特定のアクティビティに到達するまで、パイプラインをデバッグできます。 アクティビティにテストの終了点となるブレークポイントを設定し、 **[デバッグ]** を選択します。 このサービスは、パイプライン キャンバスでブレークポイント アクティビティまでのみ、テストの実行を保証します。 この *特定の場所までデバッグする* 機能は、パイプライン全体ではなく、パイプライン内のアクティビティのサブセットのみをテストする場合に便利です。

:::image type="content" source="media/iterative-development-debugging/iterative-development-3.png" alt-text="パイプライン キャンバス上のブレークポイント":::

ブレークポイントを設定するには、パイプライン キャンバス上で要素を選択します。 *[Debug Until]\(特定の場所までデバッグする\)* オプションは、要素の右上隅に空の赤い円として表示されます。

:::image type="content" source="media/iterative-development-debugging/iterative-development-4.png" alt-text="選択した要素にブレークポイントを設定する前":::

*[Debug Until]\(特定の場所までデバッグする\)* オプションを選択した後は、赤い円が塗りつぶされ、ブレークポイントが有効になったことが示されます。

:::image type="content" source="media/iterative-development-debugging/iterative-development-5.png" alt-text="選択した要素にブレークポイントを設定した後":::

## <a name="monitoring-debug-runs"></a>デバッグ実行の監視

パイプラインのデバッグ実行を実行すると、パイプライン キャンバスの **[出力]** ウィンドウに結果が表示されます。 出力タブには、現在のブラウザー セッション中に行われた最新の実行のみが含まれます。 

:::image type="content" source="media/iterative-development-debugging/iterative-development-2.png" alt-text="パイプライン キャンバスの出力ウィンドウ":::

デバッグ実行の履歴ビューを表示したり、すべてのアクティブなデバッグ実行の一覧を表示したりするには、 **[監視]** エクスペリエンスにアクセスします。 

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
:::image type="content" source="media/iterative-development-debugging/view-debug-runs.png" alt-text="[View active debug runs] アイコンを選択":::

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)
:::image type="content" source="media/iterative-development-debugging/view-debug-runs-synapse.png" alt-text="[View active debug runs] アイコンを選択":::

---

> [!NOTE]
> このサービスでは、デバッグの実行履歴は 15 日間のみ保持されます 

## <a name="debugging-mapping-data-flows"></a>マッピング データ フローのデバッグ

マッピング データ フローを使用すると、大規模に実行されるコーディング不要のデータ変換ロジックを作成できます。 ロジックを構築する際に、デバッグ セッションをオンにして、ライブ Spark クラスターを使用してインタラクティブにデータを操作することができます。 詳細については、「[マッピング データ フローのデバッグ モード](concepts-data-flow-debug-mode.md)」を参照してください。

**[監視]** エクスペリエンスで、アクティブなデータ フロー デバッグ セッションを監視できます。

:::image type="content" source="media/iterative-development-debugging/view-dataflow-debug-sessions.png" alt-text="データ フロー デバッグ セッションの表示":::

データ フロー デザイナーのデータ プレビューとデータ フローのパイプライン デバッグは、小規模なデータ サンプルを使った場合に最適に機能するよう設計されています。 ただし、パイプラインまたはデータ フローのロジックを大量のデータに対してテストする必要がある場合は、デバッグ セッションで使用される Azure Integration Runtime のサイズを増やしてください (コア数を増やし、汎用的な計算を最小限に減らしてください)。
 
### <a name="debugging-a-pipeline-with-a-data-flow-activity"></a>データ フロー アクティビティが含まれるパイプラインのデバッグ

データ フローが含まれるパイプライン実行のデバッグを行うときに使用するコンピューティングには、2 つのオプションがあります。 既存のデバッグ クラスターを使用することも、データ フローに対して新しい Just-In-Time クラスターを作成することもできます。

既存のデバッグ セッションを使用すると、クラスターが既に稼働中であるためデータ フローの起動時間が大幅に短縮されますが、複数のジョブを一度に実行するときに失敗する場合があるため、複雑なワークロードや並列ワークロードには推奨されません。

アクティビティ ランタイムを使用すると、各データ フロー アクティビティの統合ランタイムで指定された設定を使用して新しいクラスターが作成されます。 これにより、各ジョブを分離することができるため、複雑なワークロードやパフォーマンス テストにはこれを使用する必要があります。 デバッグに使用されるクラスター リソースが、その期間内に追加のジョブ要求の処理のためにも使用できるように、Azure IR で TTL を制御することもできます。

> [!NOTE]
> データ フローが並列で実行されるパイプラインがある場合や、大きなデータセットを使ってテストしなければならないデータ フローがある場合は、データ フロー アクティビティで選択した Integration Runtime をこのサービスで使用できるように、[アクティビティランタイムを使用する] を選択します。 これで、データ フローを複数のクラスターで実行できるようになり、データ フローの並列実行に対応できます。

:::image type="content" source="media/iterative-development-debugging/iterative-development-dataflow.png" alt-text="データフローを使用したパイプラインの実行":::

## <a name="next-steps"></a>次のステップ

変更をテストした後、[継続的なインテグレーションとデプロイ](continuous-integration-delivery.md)を使用して、より上位の環境に昇格させます。

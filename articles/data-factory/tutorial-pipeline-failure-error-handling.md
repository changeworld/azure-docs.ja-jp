---
title: パイプラインの失敗とエラー メッセージ
description: パイプラインの失敗状態とエラー メッセージがどのように決定されるかを理解する
ms.service: data-factory
ms.subservice: orchestration
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: tutorial
ms.date: 11/08/2021
ms.openlocfilehash: bc9abf7319b3dc51572670f242b5d18a1a5d270d
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522329"
---
# <a name="understanding-pipeline-failure"></a>パイプラインの失敗について

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="error-handling"></a>エラー処理

Azure Data Factory と Synapse パイプラインのオーケストレーションでは、条件付きロジックが許可され、ユーザーは前のアクティビティの結果に基づいて異なる選択ができます。 さまざまなパスを使用すると、ユーザーは堅牢なパイプラインを構築し、ETL/ELT ロジックにエラー処理を組み込むことができるようになります。 合計で 4 つの条件付きパスが利用できます。

* 成功時 (既定のパス)
* 失敗時
* 完了時
* スキップ時

:::image type="content" source="media/tutorial-pipeline-failure-error-handling/pipeline-error-1-four-branches.png" alt-text="アクティビティからの 4 つの分岐を示すスクリーンショット。":::

### <a name="common-error-handling-mechanism"></a>一般的なエラー処理メカニズム

#### <a name="try-catch-block"></a>Try Catch ブロック

このアプローチでは、お客様はビジネス ロジックを定義し、前のアクティビティのエラーをキャッチする "_失敗時_" パスのみを定義します。 このアプローチでは、"_失敗時_" パスが成功した場合に、パイプラインが成功したことを示します。

:::image type="content" source="media/tutorial-pipeline-failure-error-handling/pipeline-error-2-try-catch-definition.png" alt-text="try catch ブロックの定義と結果を示すスクリーンショット。":::

#### <a name="do-if-else-block"></a>Do If Else ブロック

このアプローチでは、お客様はビジネス ロジックを定義し、"_失敗時_" と"_成功時_" の両方のパスを定義します。 このアプローチでは、"_失敗時_" パスが成功しても、パイプラインが失敗したことを示します。

:::image type="content" source="media/tutorial-pipeline-failure-error-handling/pipeline-error-3-do-if-else-definition.png" alt-text="do if else ブロックの定義と結果を示すスクリーンショット。":::

#### <a name="do-if-skip-else-block"></a>Do If Skip Else ブロック

このアプローチでは、顧客はビジネス ロジックを定義し、"_失敗時_" パスと、ダミーの "_スキップ時_" アクティビティが接続されている "_成功時_" パスの両方を定義します。 このアプローチでは、"_失敗時_" パスが成功した場合に、パイプラインが成功したことを示します。

:::image type="content" source="media/tutorial-pipeline-failure-error-handling/pipeline-error-4-do-if-skip-else-definition.png" alt-text="do if skip else ブロックの定義と結果を示すスクリーンショット。":::

### <a name="summary-table"></a>概要テーブル

アプローチ | 定義 | アクティビティが成功した場合のパイプライン全体としての表示 | アクティビティが失敗した場合のパイプライン全体としての表示
---------------------------- | ------------------- | ------------------- | -------------------
[Try-Catch](#try-catch-block) | "_失敗時_" パスのみ | 成功 |  成功
[Do-If-Else](#do-if-else-block) | "_失敗時_" パスと "_成功時_" パス | Success |  障害
[Do-If-Skip-Else](#do-if-skip-else-block) |  "_失敗時_" パスと、(最後に "_ダミーのスキップ時_" が付いた) "_成功時_" パス | 成功 |  成功

### <a name="how-pipeline-failure-are-determined"></a>パイプラインの失敗の決定方法

さまざまなエラー処理メカニズムを使用すると、パイプラインはさまざまな状態になります。一部のパイプラインが失敗する一方で、他のものは成功します。 パイプラインの成功と失敗は、次のように決定します。

* すべてのリーフ アクティビティの結果を評価します。 リーフ アクティビティがスキップされた場合は、代わりにその親アクティビティを評価します
* パイプラインの結果は、評価対象のすべてのノードが成功した場合にのみ成功します

"_失敗時_" アクティビティと "_ダミーのスキップ時_" アクティビティが成功したと仮定します。

* [Try-Catch](#try-catch-block) アプローチの場合、

  * 前のアクティビティが成功したとき: ノード "_失敗時_" がスキップされ、その親ノードは成功します。パイプライン全体は成功します
  * 前のアクティビティが失敗したとき: ノード "_失敗時_" が実行されます。パイプライン全体は成功します

* [Do-If-Else](#do-if-else-block) アプローチの場合、

  * 前のアクティビティが成功したとき: ノード "_成功時_" が成功し、ノード "_失敗時_" はスキップされます (その親ノードは成功します)。パイプライン全体は成功します
  * 前のアクティビティが失敗したとき: ノード "_成功時_" がスキップされ、その親ノードは失敗します。パイプライン全体は失敗します

* [Do-If-Skip-Else](#do-if-skip-else-block) アプローチの場合、

  * 前のアクティビティが成功したとき: ノード "_ダミーのスキップ時_" がスキップされ、その親ノードの "_成功時_" は成功します。その他のノード アクティビティ "_失敗時_" はスキップされ、その親ノードは成功します。パイプライン全体は成功します
  * 前のアクティビティが失敗したとき: ノード "_失敗時_" が成功し、"_ダミーのスキップ時_" も成功します。パイプライン全体は成功します

## <a name="next-steps"></a>次のステップ

[Data Factory のメトリックとアラート](monitor-metrics-alerts.md)

[視覚的な監視](monitor-visually.md#alerts)

---
title: Azure Data Factory でラングリング データ フローの使用を開始する
description: ラングリング データ フローを使用して Azure Data Factory データを準備する方法に関するチュートリアル
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: 43ff1591730f240e1ae3ebcfc4066fdc34835372
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355584"
---
# <a name="prepare-data-with-data-wrangling"></a>データ ラングリングを使用してデータを準備する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

データ ファクトリのデータ ラングリングを使用すると、対話型の Power Query マッシュアップを ADF でネイティブに構築し、ADF パイプライン内で大規模に実行することができます。

## <a name="create-a-power-query-activity"></a>Power Query アクティビティを作成する

Azure Data Factory に Power Query を作成するには、2 つの方法があります。 1 つの方法は、プラス記号アイコンをクリックし、ファクトリ リソース ペインで **[Power Query]** を選択することです。

:::image type="content" source="media/data-flow/power-query-wrangling.png" alt-text="ファクトリ リソース ペインでの Power Query を示すスクリーンショット。":::

もう 1 つの方法は、パイプライン キャンバスのアクティビティ ウィンドウで行います。 **Power Query** アコーディオンを開き、**Power Query** アクティビティをキャンバスにドラッグします。

:::image type="content" source="media/data-flow/power-query-activity.png" alt-text="データ ラングリング オプションを強調表示したスクリーンショット。":::

## <a name="author-a-power-query-data-wrangling-activity"></a>Power Query データ ラングリング アクティビティを作成する

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWJd3T]
> 
Power Query マッシュアップの **ソース データセット** を追加します。 既存のデータセットを選択するか、新しいデータセットを作成することができます。 マッシュアップを保存したら、Power Query データ ラングリング アクティビティをパイプラインに追加し、シンク データセットを選択して、データを格納する場所を ADF に通知できます。 1 つ以上のソース データセットを選択できますが、現時点では 1 つのシンクのみが許可されます。 シンク データセットの選択は省略可能ですが、少なくとも 1 つのソース データセットが必要です。

:::image type="content" source="media/wrangling-data-flow/tutorial4.png" alt-text="ラングリング":::

**[作成]** をクリックして、Power Query Online マッシュアップ エディターを開きます。

まず、マッシュアップ エディターのデータセット ソースを選択します。

:::image type="content" source="media/wrangling-data-flow/power-query-new-source.png" alt-text="Power Query ソース。":::

Power Query の作成が完了したら、それを保存し、マッシュアップをアクティビティとしてパイプラインに追加できます。 そのときに、シンク データセットのプロパティを設定します。

:::image type="content" source="media/wrangling-data-flow/power-query-new-sink.png" alt-text="Power Query シンク。":::

コーディング不要のデータ準備を使用して、ラングリング Power Query を作成します。 使用できる関数の一覧については、[変換関数](wrangling-functions.md)に関するページを参照してください。 ADF は、M スクリプトをデータ フロー スクリプトに変換し、Azure Data Factory データ フロー Spark 環境を使用して大規模に Power Query を実行できるようにします。

:::image type="content" source="media/wrangling-data-flow/tutorial6.png" alt-text="データ ラングリング Power Query を作成するためのプロセスを示すスクリーンショット。":::

## <a name="running-and-monitoring-a-power-query-data-wrangling-activity"></a>Power Query データ ラングリング アクティビティの実行と監視

Power Query アクティビティのパイプライン デバッグ実行を実行するには、パイプラインキャンバスの **[デバッグ]** をクリックします。 パイプラインを発行すると、 **[Trigger Now]\(今すぐトリガー\)** によって、最後に発行されたパイプラインのオンデマンド実行が実行されます。 Power Query パイプラインは、既存のすべての Azure Data Factory トリガーを使用してスケジュールできます。

:::image type="content" source="media/data-flow/power-query-activity.png" alt-text="Power Query データ ラングリング アクティビティを追加する方法を示すスクリーンショット。":::

**[モニター]** タブにアクセスして、トリガーされた Power Query アクティビティの実行の出力を視覚化します。

:::image type="content" source="media/wrangling-data-flow/tutorial2.png" alt-text="トリガーされたラングリング Power Query アクティビティの実行の出力を示すスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

[マッピング データ フローの作成](tutorial-data-flow.md)方法について確認します。

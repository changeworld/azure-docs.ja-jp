---
title: 'チュートリアル: パイプラインとの統合を開始する'
description: このチュートリアルでは、Synapse Studio を使用してパイプラインとアクティビティを統合する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 19bff62883341947eb5290118494b8244c5476ac
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518254"
---
# <a name="integrate-with-pipelines"></a>パイプラインと統合する

このチュートリアルでは、Synapse Studio を使用してパイプラインとアクティビティを統合する方法について説明します。 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>パイプラインを作成してノートブック アクティビティを追加する

1. Synapse Studio で、 **[統合]** ハブに移動します。
1. **[+]**  >  **[パイプライン]** を選択して新しいパイプラインを作成します。 新しいパイプライン オブジェクトをクリックして、パイプライン デザイナーを開きます。
1. **[アクティビティ]** で **[Synapse]** フォルダーを展開し、**Notebook** オブジェクトをデザイナーにドラッグします。
1. Notebook アクティビティのプロパティの **[設定]** タブを選択します。 ドロップダウン リストを使用して、現在の Synapse ワークスペースからノートブックを選択します。

## <a name="schedule-the-pipeline-to-run-every-hour"></a>パイプラインを 1 時間ごとに実行するようにスケジュールを設定する

1. パイプラインで、 **[トリガーの追加]**  >  **[New/edit]\(新規作成/編集\)** を選択します。
1. **[Choose trigger]\(トリガーの選択\)** で **[新規]** を選択し、 **[Recurrence]\(繰り返し\)** を "1 時間ごと" に設定します。
1. **[OK]** を選択します。 
1. **[すべて公開]** を選択します。 

## <a name="forcing-a-pipeline-to-run-immediately"></a>強制的にパイプラインを即座に実行する

パイプラインが発行されたら、1 時間経過するのを待たずにすぐに実行することができます。

1. パイプラインを開きます。
1. **[トリガーの追加]**  >  **[Trigger Now]\(今すぐトリガー\)** の順にクリックします。

## <a name="monitor-pipeline-execution"></a>パイプラインの実行を監視する

1. **[監視]** ハブに移動します。
1. パイプラインの実行の進行状況を監視するには、 **[パイプラインの実行]** を選択します。
1. このビューでは、表形式の **一覧** 表示とグラフィカルな **ガント** チャートを切り替えることができます。 
1. パイプライン名をクリックして、そのパイプラインのアクティビティの状態を確認します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Power BI でデータを視覚化する](get-started-visualize-power-bi.md)

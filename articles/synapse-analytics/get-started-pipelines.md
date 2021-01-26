---
title: チュートリアル:パイプラインとの統合の概要
description: このチュートリアルでは、Synapse Studio を使用してパイプラインとアクティビティを統合する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 10/27/2020
ms.openlocfilehash: af01d5b5e424dd5ea229115f7aa3570d0b7cd511
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744928"
---
# <a name="integrate-with-pipelines"></a>パイプラインと統合する

このチュートリアルでは、Synapse Studio を使用してパイプラインとアクティビティを統合する方法について説明します。 

## <a name="overview"></a>概要

Azure Synapse では、さまざまなタスクを統合できます。

1. Synapse Studio で、 **[統合]** ハブに移動します。
1. **[+]**  >  **[パイプライン]** を選択して新しいパイプラインを作成します。
1. **[開発]** ハブに移動し、先ほど作成したノートブックのいずれかを選択します。
1. そのノートブックをパイプラインにドラッグします ( **注** : [ドキュメント](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace)に指定されているように、モジュールのインポートのステップをノートブックに追加します。これらのモジュールは、パイプラインからの実行中に必要になります)
1. パイプラインで、 **[トリガーの追加]**  >  **[New/edit]\(新規作成/編集\)** を選択します。
1. **[Choose trigger]\(トリガーの選択\)** で **[新規]** を選択し、 **[Recurrence]\(繰り返し\)** を "1 時間ごと" に設定します。
1. **[OK]** を選択します。 
1. **[すべて公開]** を選択します。
1. 次の 1 時間を待機せずにパイプラインをすぐに実行する場合は、 **[トリガーの追加]**  >  **[Trigger now]\(今すぐトリガー\)** の順に選択します。



## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Power BI でデータを視覚化する](get-started-visualize-power-bi.md)
                                 

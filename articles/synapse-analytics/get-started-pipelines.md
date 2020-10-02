---
title: チュートリアル:パイプラインを使用したオーケストレーションの概要
description: このチュートリアルでは、Synapse Studio を使用してパイプラインとアクティビティのオーケストレーションを行う方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 72eea7c46dd005cd16ae5b8f0022c1174dd28f27
ms.sourcegitcommit: 0194a29a960e3615f96a2d9d8a7e681cf3e8f9ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2020
ms.locfileid: "89667489"
---
# <a name="orchestrate-with-pipelines"></a>パイプラインを使用したオーケストレーション

このチュートリアルでは、Synapse Studio を使用してパイプラインとアクティビティのオーケストレーションを行う方法について説明します。 

## <a name="overview"></a>概要

Azure Synapse では、さまざまなタスクを調整できます。

1. Synapse Studio で **[Orchestrate]\(調整\)** ハブに移動します。
1. **[+]**  >  **[パイプライン]** を選択して新しいパイプラインを作成します。
1. **[開発]** ハブに移動し、先ほど作成したノートブックのいずれかを選択します。
1. そのノートブックをパイプラインにドラッグします。
1. パイプラインで、 **[トリガーの追加]**  >  **[New/edit]\(新規作成/編集\)** を選択します。
1. **[Choose trigger]\(トリガーの選択\)** で **[新規]** を選択し、 **[繰り返し]** でトリガーを 1 時間ごとに実行するよう設定します。
1. **[OK]** を選択します。
1. **[すべて公開]** を選択します。 パイプラインが 1 時間おきに実行されます。
1. 1 時間待たずに今すぐパイプラインを実行する場合は、 **[トリガーの追加]**  >  **[New/edit]\(新規作成/編集\)** を選択します。



## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Power BI でデータを視覚化する](get-started-visualize-power-bi.md)
                                 

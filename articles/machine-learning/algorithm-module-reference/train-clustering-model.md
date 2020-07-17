---
title: クラスタリング モデルのトレーニング:モジュール リファレンス
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で Train Clustering Model (クラスタリング モデルのトレーニング) モジュールを使用して、クラスタリング モデルをトレーニングする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c29baf3cdda998a2ab78c84f3311b84d37086bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477427"
---
# <a name="train-clustering-model"></a>クラスタリング モデルのトレーニング

この記事では Azure Machine Learning デザイナー (プレビュー) 内のモジュールについて説明します。

このモジュールを使用してクラスタリング モデルをトレーニングします。

このモジュールは、[K-Means Clustering (K-Means クラスタリング)](k-means-clustering.md) モジュールを使用して既に構成済みのトレーニングされていないクラスタリング モデルを取得し、ラベル付きまたはラベルなしデータセットを使用してそのモデルをトレーニングします。 このモジュールは、予測に使用できるトレーニング済みモデルと、トレーニング データの各ケースに対するクラスター割り当てセットの両方を作成します。

> [!NOTE]
> 機械学習モデルをトレーニングするための汎用モジュールである [モデルのトレーニング](train-model.md) モジュールを使用して、クラスタリング モデルをトレーニングすることはできません。 これは、[Train Model (モデルのトレーニング)](train-model.md) が教師あり学習アルゴリズムでのみ動作するためです。 K 平均法と他のクラスタリング アルゴリズムは教師なし学習を可能にします。これは、アルゴリズムがラベルなしデータから学習できることを意味します。  
  
## <a name="how-to-use-train-clustering-model"></a>クラスタリング モデルのトレーニングの使用方法  

1.  **Train Clustering Model (クラスタリング モデルのトレーニング)** モジュールをデザイナーの自分のパイプラインに追加します。 このモジュールは、**Machine Learning モジュール**の **[トレーニング]** カテゴリにあります。  
  
2. [K-Means Clustering (K-Means クラスタリング)](k-means-clustering.md) モジュール、または互換性のあるクラスタリング モデルを作成する別のカスタム モジュールを追加し、クラスタリング モデルのパラメーターを設定します。  
    
3.  **[Train Clustering Model]\(クラスタリング モデルのトレーニング\)** の右側の入力にトレーニング データセットをアタッチします。
  
5.  **[Column Set]\(列セット\)** で、クラスターの構築に使用するデータセットの列を選択します。 適切な特徴となる列を必ず選択してください。たとえば、一意の値を持つ ID やその他の列、またはすべて同じ値を持つ列は使用しないようにします。

    ラベルが使用可能な場合は、特徴として使用することも、除外することもできます。  
  
6. トレーニング データを新しいクラスター ラベルとともに出力する場合は、 **[Check for append or uncheck for result only]\(追加をチェックまたは結果のみをチェック解除\)** オプションを選択します。

    このオプションの選択を解除すると、クラスターの割り当てだけが出力されます。 

7. パイプラインを送信するか、**Train Clustering Model (クラスタリング モデルのトレーニング)** モジュールをクリックして **[選択した項目を実行]** を選択します。  
  
### <a name="results"></a>結果

トレーニングの完了後、次の作業を行います。

+ トレーニングされたモデルのスナップショットを保存するには、 **[モデルのトレーニング]** モジュールの右側のパネルにある **[出力]** タブを選択します。 **[データセットの登録]** アイコンを選択して、再利用可能なモジュールとしてモデルを保存します。

+ モデルからスコアを生成するには、「[クラスターへのデータの割り当て](assign-data-to-clusters.md)」を使用します。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 
---
title: クラスターへのデータの割り当て:モジュール リファレンス
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service で Assign Data to Cluster (クラスターへのデータの割り当て) モジュールを使用して、クラスタリング モデルをスコア付けする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b370c6ef5be311ed9c8df2737fa1b01144d61011
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027721"
---
# <a name="assign-data-to-clusters"></a>クラスターへのデータの割り当て

この記事では、ビジュアル インターフェイスで [Assign Data to Clusters (クラスターへのデータの割り当て)](assign-data-to-clusters.md) モジュールを使用して、K-Meansクラスタリング アルゴリズムでトレーニングされたクラスタリング モデルを使用して予測を生成する方法について説明します。

このモジュールは、新しい各データ ポイントの割り当て候補を含むデータセットを返します。 


## <a name="how-to-use-assign-data-to-clusters"></a>クラスターへのデータの割り当てを使用する方法
  
1.  ビジュアル インターフェイスで、以前にトレーニングされたクラスタリング モデルを見つけます。 次のいずれかの方法を使用して、クラスタリング モデルを作成およびトレーニングできます。  
  
    - [K-Means Clustering (K-Means クラスタリング)](k-means-clustering.md) モジュールを使用して K-Means アルゴリズムを構成し、データセットと [Train Clustering Model (クラスタリング モデルのトレーニング)](train-clustering-model.md) モジュールを使用してそのモデルをトレーニングします。  
  
  
    また、トレーニング済みの既存のクラスタリング モデルを、ワークスペースの **[Saved Models]\(保存済みのモデル\)** グループから追加することもできます。

2. トレーニング済みのモデルを [Assign Data to Clusters](assign-data-to-clusters.md) (クラスターへのデータの割り当て) の左側の入力ポートにアタッチします。  

3. 新しいデータセットを入力としてアタッチします。 このデータセットでは、ラベルは省略可能です。 一般に、クラスタリングは教師なし学習法であるため、事前にカテゴリが判明していることは期待されていません。

    ただし、入力列はクラスタリング モデルのトレーニングで使用された列と同じである必要があり、同じでないとエラーが発生します。

    > [!TIP]
    > クラスター予測から出力される列の数を減らすには、[[データセット内の列の選択]](select-columns-in-dataset.md) を使用して、列のサブセットを選択します。 
    
4. 結果に、結果 (クラスター割り当て) を示す列とともに完全な入力データセットを含める場合は、**[Check for Append or Uncheck for Result Only]\(追加をチェックまたは結果のみをチェック解除\)** オプションを選択したままにします。
  
    このオプションの選択を解除すると、結果のみが返されます。 これは、Web サービスの一部として予測を作成する場合に便利です。
  
5.  実験を実行します。  
  
### <a name="results"></a>結果

 
+  データセットの値を表示するには、モジュールを右クリックし、**[Result datasets]\(結果データセット\)** を選択して **[可視化]** をクリックします。


---
title: 'クラスタリング モデルのトレーニング: コンポーネント リファレンス'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で Train Clustering Model (クラスタリング モデルのトレーニング) コンポーネントを使用して、クラスタリング モデルをトレーニングする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/17/2021
ms.openlocfilehash: 16d20e71dba5c83093ec572aad4910ec9a89ba06
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565741"
---
# <a name="train-clustering-model"></a>クラスタリング モデルのトレーニング

この記事では Azure Machine Learning デザイナーのコンポーネントについて説明します。

このコンポーネントを使用して、クラスタリング モデルをトレーニングします。

このコンポーネントは、[K-Means Clustering (K-Means クラスタリング)](k-means-clustering.md) コンポーネントを使用して既に構成済みのトレーニングされていないクラスタリング モデルを取得し、ラベル付きまたはラベルなしデータセットを使用してそのモデルをトレーニングします。 このコンポーネントは、予測に使用できるトレーニング済みモデルと、トレーニング データの各ケースに対するクラスター割り当てセットの両方を作成します。

> [!NOTE]
> 機械学習モデルをトレーニングするための汎用コンポーネントである [モデルのトレーニング](train-model.md) コンポーネントを使用して、クラスタリング モデルをトレーニングすることはできません。 これは、[Train Model (モデルのトレーニング)](train-model.md) が教師あり学習アルゴリズムでのみ動作するためです。 K 平均法と他のクラスタリング アルゴリズムは教師なし学習を可能にします。これは、アルゴリズムがラベルなしデータから学習できることを意味します。  
  
## <a name="how-to-use-train-clustering-model"></a>クラスタリング モデルのトレーニングの使用方法  

1.  **Train Clustering Model (クラスタリング モデルのトレーニング)** コンポーネントをデザイナーの自分のパイプラインに追加します。 このコンポーネントは、**Machine Learning コンポーネント** の **[トレーニング]** カテゴリにあります。  
  
2. [K-Means Clustering (K-Means クラスタリング)](k-means-clustering.md) コンポーネント、または互換性のあるクラスタリング モデルを作成する別のカスタム コンポーネントを追加し、クラスタリング モデルのパラメーターを設定します。  
    
3.  **[Train Clustering Model]\(クラスタリング モデルのトレーニング\)** の右側の入力にトレーニング データセットをアタッチします。
  
5.  **[Column Set]\(列セット\)** で、クラスターの構築に使用するデータセットの列を選択します。 適切な特徴となる列を必ず選択してください。たとえば、一意の値を持つ ID やその他の列、またはすべて同じ値を持つ列は使用しないようにします。

    ラベルが使用可能な場合は、特徴として使用することも、除外することもできます。  
  
6. トレーニング データを新しいクラスター ラベルとともに出力する場合は、**[Check for append or uncheck for result only]\(追加をチェックまたは結果のみをチェック解除\)** オプションを選択します。

    このオプションの選択を解除すると、クラスターの割り当てだけが出力されます。 

7. パイプラインを送信するか、**Train Clustering Model (クラスタリング モデルのトレーニング)** コンポーネントをクリックして **[選択した項目を実行]** を選択します。  
  
### <a name="results"></a>結果

トレーニングの完了後、次の作業を行います。

+ トレーニングされたモデルのスナップショットを保存するには、 **[モデルのトレーニング]** コンポーネントの右側のパネルにある **[出力]** タブを選択します。 **[データセットの登録]** アイコンを選んで、再利用可能なコンポーネントとしてモデルを保存します。

+ モデルからスコアを生成するには、「[クラスターへのデータの割り当て](assign-data-to-clusters.md)」を使用します。

> [!NOTE]
> トレーニング済みのモデルをデザイナーに配置する必要がある場合は、**Score Model (モデルのスコア付け)** ではなく、[Assign Data to Clusters (クラスターへのデータの割り当て)](assign-data-to-clusters.md) が、推論パイプラインの [Web Service Output (Web サービスの出力) コンポーネント](web-service-input-output.md)の入力に接続されていることを確認してください。

## <a name="next-steps"></a>次の手順

Azure Machine Learning で[使える一連のコンポーネント](component-reference.md)を参照してください。 
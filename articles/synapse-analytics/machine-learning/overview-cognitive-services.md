---
title: Azure Synapse Analytics での Cognitive Services
description: Azure Cognitive Services からの事前トレーニング済みモデルを使用して、Azure Synapse Analytics の人工知能 (AI) でご利用のデータを強化します。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye, negust, ruxu
ms.date: 06/30/2021
author: garyericson
ms.author: garye
ms.openlocfilehash: c9db8c1cf508d05dfafae672e5f03ff55bb046c0
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113114579"
---
# <a name="cognitive-services-in-azure-synapse-analytics"></a>Azure Synapse Analytics での Cognitive Services

Azure Cognitive Services からの事前トレーニング済みモデルを使用すると、Azure Synapse Analytics の人工知能 (AI) でご利用のデータを強化できます。

[Azure Cognitive Services](/azure/cognitive-services/what-are-cognitive-services) はクラウドベースのサービスであり、これによって、AI またはデータ サイエンスのスキルを持っていない場合でも、ご利用のデータにコグニティブなインテリジェンスを追加できます。 Synapse Analytics でご利用のデータに対してこれらのサービスを使用する方法はいくつかあります。

- Synapse Analytics の Cognitive Services ウィザードを使用すると、Spark テーブルのデータを使用してコグニティブ サービスに接続する PySpark コードが Synapse ノートブック内に生成されます。 その後、そのサービスでは、トレーニング済みの機械学習モデルを使用して、データに AI を追加するための処理をお客様に代わって行います。

- チュートリアル「[Microsoft Machine Learning for Apache Spark を使用して機械学習アプリケーションを構築する (プレビュー)](tutorial-build-applications-use-mmlspark.md)」では、Microsoft Machine Learning for Apache Spark (MMLSpark) を使用して多くのコグニティブ サービスを呼び出す方法を示しています。

- ウィザードによって生成された PySpark コード、またはチュートリアルで用意されているサンプル MMLSpark コードから始めることで、ご利用のデータに対して他のコグニティブ サービスを使用するための独自のコードを作成できます。 利用可能なサービスの詳細については、「[Azure Cognitive Services とは](/azure/cognitive-services/what-are-cognitive-services)」を参照してください。

## <a name="get-started"></a>はじめに

チュートリアル「[Azure Synapse Analytics で Cognitive Services を使用するための前提条件](tutorial-configure-cognitive-services-synapse.md)」では、Synapse Analytics で Cognitive Services を使用する前に行う必要があるいくつかの手順について説明します。

## <a name="tutorials"></a>チュートリアル

次のチュートリアルでは、Synapse Analytics で Cognitive Services を使用する完全な例を示します。

- [Cognitive Services を使用した感情分析](tutorial-cognitive-services-sentiment.md) - 顧客コメントのサンプル データ セットを使用して、各行でコメントのセンチメントが示される列を含む Spark テーブルを作成します。

- [Cognitive Services による異常検出](tutorial-cognitive-services-anomaly.md) - 時系列データのサンプル データ セットを使用して、各行のデータが異常であるかどうかを示す列を含む Spark テーブルを作成します。

- [Microsoft Machine Learning for Apache Spark を使用して機械学習アプリケーションを構築する (プレビュー)](tutorial-build-applications-use-mmlspark.md) - このチュートリアルでは、MMLSpark を使用して Cognitive Services から複数のモデルにアクセスする方法を示します。

## <a name="next-steps"></a>次のステップ

- [Azure Synapse Analytics の機械学習機能](what-is-machine-learning.md)
- [Cognitive Services とは](/azure/cognitive-services/what-are-cognitive-services)
- [Synapse Analytics ギャラリーからのサンプル ノートブックを使用する](quickstart-gallery-sample-notebook.md)
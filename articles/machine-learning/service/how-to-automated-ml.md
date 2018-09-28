---
title: 自動化された機械学習とは - Azure Machine Learning
description: この記事では、自動化された機械学習について説明します。 Azure Machine Learning サービスはアルゴリズムを自動的に選択し、そのアルゴリズムからモデルを生成できます。 自動化された機械学習を使用すると、指定したパラメーターと条件を使用してモデルに最適なアルゴリズムを選択することによって時間を節約できます。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: krishnan
author: krishnaanumalasetty
ms.date: 9/24/2018
ms.openlocfilehash: 2a9c05b68d05102fab80b2aa8fb1c1dad8a367ea
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960041"
---
# <a name="what-is-automated-machine-learning"></a>自動化された機械学習とは

この記事では、自動化された機械学習について説明します。 Azure Machine Learning サービスはアルゴリズムを自動的に選択し、そのアルゴリズムからモデルを生成できます。 自動化された機械学習を使用すると、指定したパラメーターと条件を使用してモデルに最適なアルゴリズムを選択することによって時間を節約できます。

## <a name="how-it-works"></a>動作のしくみ

1. 解決しようとしている機械学習の問題の種類を構成します。 監視された学習の 2 つのカテゴリがサポートされています。
   + 分類
   + 回帰

   [モデルの一覧](how-to-configure-auto-train.md#select-your-experiment-type)を参照してください。トレーニング時に Azure Machine Learning を試すことができます。

1. トレーニング データのソースと形式を指定します。 データにはラベルを付ける必要があり、開発環境または Azure Blob Storage に格納できます。 データを開発環境に格納する場合、トレーニング スクリプトと同じディレクトリに格納する必要があります。 このディレクトリは、トレーニング用に選択したコンピューティング ターゲットにコピーされます。

    トレーニング スクリプトでは、Numpy 配列または Pandas データフレームにデータを読み取ることができます。

    トレーニングと検証のデータを選択する分割オプションを構成するか、別のトレーニングと検証データ セットを指定できます。

1. モデルのトレーニングに使用する[コンピューティング ターゲット](how-to-set-up-training-targets.md)を構成します。

1. 自動化された機械学習構成を行います。 これは、最適モデルを決定する際に確認するさまざまなモデル、ハイパーパラメーター設定およびメトリックに対して、Azure Machine Learning が繰り返し使用するパラメーターを制御します。 

1. トレーニングの実行を送信します。

トレーニング中、Azure Machine Learning サービスは、さまざまなアルゴリズムとパラメーターを試行する多数のパイプラインを作成します。 これは、指定した繰り返し回数制限に達した場合、または指定したメトリックの目標値に到達した場合に停止します。

[ ![Automated Machine learning](./media/how-to-automated-ml/automated-machine-learning.png) ](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

ログされた実行情報を検査できます。これには、実行中に収集したメトリックが含まれています。 トレーニングを実行すると、モデルおよびデータ前処理を含む Python シリアル化オブジェクト (.pkl ファイル) も生成されます。

## <a name="next-steps"></a>次の手順

例を参照して、Automated Machine Learning を使用してモデルを構築する方法を学習してください。

+ [チュートリアル: Azure Automated Machine Learning で分類モデルを自動的にトレーニングする](tutorial-auto-train-models.md)

+ [自動トレーニングの設定を構成する方法](how-to-configure-auto-train.md)

+ [リモート リソースで自動トレーニングを使用する方法](how-to-auto-train-remote.md) 

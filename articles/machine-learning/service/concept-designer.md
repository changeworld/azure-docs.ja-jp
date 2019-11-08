---
title: デザイナーを使用して ML モデルを構築する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 用のデザイナーを構成する用語、概念、ワークフローについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: ee97322e58fe7ab3a1474f55c6294822b8ce90da
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73511832"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>Azure Machine Learning デザイナー (プレビュー) とは 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Azure Machine Learning 用のデザイナーでは、コードを記述することなく、機械学習モデルのデータの準備、トレーニング、テスト、デプロイ、管理、追跡を行うことができます。

プログラミングは必要ありません。[データセット](#datasets)と[モジュール](#module)を視覚的に接続して、モデルを構築します。

デザイナーでは、Azure Machine Learning [ワークスペース](concept-workspace.md)を使用して次の操作を行います。

+ ワークスペースで[パイプライン](#pipeline)を作成、編集、実行する
+ [データセット](#datasets)にアクセスする
+ ワークスペースで[コンピューティング リソース](#compute)を使用してパイプラインを実行する 
+ [モデル](concept-azure-machine-learning-architecture.md#models)を登録する
+ パイプラインを REST エンドポイントとして[公開](#publish)します。
+ モデルをワークスペースのコンピューティング リソースに、パイプライン エンドポイント (バッチ推論用) またはリアルタイム エンドポイントとして[デプロイ](#deployment)します。

![デザイナーの概要](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>ワークフロー

デザイナーには、モデルですばやくビルド、テスト、反復処理を行うために、対話型のビジュアル キャンバスが備わっています。 

+ [データセット](#datasets)と[モジュール](#module)をキャンバスにドラッグ アンド ドロップします。
+ 複数のモジュールに接続して[パイプライン](#pipeline)を形成します。
+ Machine Learning Service ワークスペースのコンピューティング リソースを使用してパイプラインを実行します。
+ パイプラインを編集してもう一度実行することで、モデル デザインを反復処理します。
+ 準備ができたら、**トレーニング パイプライン**を**予測パイプライン**に変換します。
+ Python コードが構築されていない状態で再送信する場合は、パイプラインを REST エンドポイントとして[公開](#publish)します。
+ モデルに他のユーザーがアクセスできるように、推論パイプラインをパイプライン エンドポイントまたはリアルタイム エンドポイントとして[デプロイ](#deployment)します。

## <a name="pipeline"></a>パイプライン

ML [パイプライン](concept-azure-machine-learning-architecture.md#ml-pipelines)を最初から作成するか、既存のサンプル パイプラインをテンプレートとして使用します。 パイプラインを実行するたびに、成果物は自分のワークスペースに格納されます。 パイプラインの実行は、[実験](concept-azure-machine-learning-architecture.md#experiments)内にグループ化されます。

パイプラインには、接続してモデルを構築するために、データセットと分析モジュールが含まれます。 有効なパイプラインに求められる具体的な条件を以下に示します。

* データセットはモジュールにのみ接続できる
* モジュールはデータセットにも別のモジュールにも接続できる
* モジュールのすべての入力ポートに、データ フローへの何らかの接続がある
* 各モジュールの必須パラメーターがすべて設定されている


デザイナーの使用を開始する方法については、「[チュートリアル: デザイナーを使用して自動車の価格を予測する](tutorial-designer-automobile-price-train-score.md)」を参照してください。

## <a name="datasets"></a>データセット

機械学習データセットによって、データへのアクセスと操作がより容易になります。 デザイナーには、実験に利用できる多数のサンプル データセットが含まれています。 必要に応じて、データセットをさらに[登録](./how-to-create-register-datasets.md)することができます。

## <a name="module"></a>モジュール

モジュールとは、データに対して実行できるアルゴリズムのことです。 デザイナーには、データのイングレス機能や、プロセスのトレーニング、スコアリング、検証などのさまざまなモジュールが用意されています。

モジュールに一連のパラメーターが含まれている場合、これらを使用してモジュールの内部アルゴリズムを構成することができます。 キャンバスでモジュールを選択すると、モジュールのパラメーターは、キャンバス右側の [プロパティ] ウィンドウに表示されます。 このウィンドウでパラメーターを変更することにより、モデルを微調整できます。

![モジュールのプロパティ](media/ui-concept-visual-interface/properties.png)

利用できる機械学習アルゴリズムのライブラリ内の移動に関するヘルプについては、「[アルゴリズムとモジュールのリファレンスの概要](../algorithm-module-reference/module-reference.md)」を参照してください。

## <a name="compute"></a> コンピューティング リソース

自分のワークスペースからコンピューティング リソースを使用して、パイプラインを実行し、デプロイ済みのモデルをリアルタイム エンドポイントまたはパイプライン エンドポイント (バッチ推論用) としてホストします。 サポートされているコンピューティング ターゲットを次に示します。

| コンピューティング ターゲット | トレーニング | Deployment |
| ---- |:----:|:----:|
| Azure Machine Learning コンピューティング | ✓ | |
| Azure Kubernetes Service | | ✓ |

コンピューティング先は、自分の Machine Learning [ワークスペース](concept-workspace.md)に接続されています。 [Azure Machine Learning Studio](https://ml.azure.com) で自分のワークスペースのコンピューティング先を管理します。

## <a name="publish"></a>発行

パイプラインの準備できたら、REST エンドポイントとして公開できます。 [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) は、それを構築した Python コードを使用せずに送信できます。

また、PublishedPipeline を使用して、異なる PipelineParameter 値と入力でパイプラインを再送信することもできます。

## <a name="deployment"></a>Deployment

予測モデルの準備が整ったら、それをデザイナーからパイプライン エンドポイントまたはリアルタイム エンドポイントとしてデプロイします。

パイプライン エンドポイントは PublishedPipeline です。これは、バッチ推論では異なる PipelineParameter 値と入力でパイプラインを再送信することができます。

リアルタイム エンドポイントでは、アプリケーションと自分のスコアリング モデルの間のインターフェイスが提供されます。 外部のアプリケーションでは、スコアリング モデルとリアルタイムで通信できます。 リアルタイム エンドポイントを呼び出すと、予測結果が外部のアプリケーションに返されます。 リアルタイム エンドポイントの呼び出しを実行するには、エンドポイントのデプロイ時に作成された API キーを渡します。 エンドポイントは、Web プログラミング プロジェクトで広く使われているアーキテクチャの REST に基づいています。

モデルのデプロイ方法の詳細については、「[チュートリアル: デザイナーで機械学習モデルをデプロイする](tutorial-designer-automobile-price-deploy.md)」を参照してください。

## <a name="moving-from-the-visual-interface-to-the-designer"></a>ビジュアル インターフェイスからデザイナーへの移行

ビジュアル インターフェイス (プレビュー) が更新され、Azure Machine Learning デザイナー (プレビュー) になりました。 デザイナーは、Azure Machine Learning の他の機能と完全に統合された、パイプラインベースのバックエンドを使用するように再設計されています。 

これらの更新の結果として、ビジュアル インターフェイスのいくつかの概念と用語が変更されたり、名前が変更されたりしています。 最も重要な概念の変更については、次の表を参照してください。 

| デザイナーの概念 | ビジュアル インターフェイスでの従来の概念 |
| ---- |:----:|
| パイプラインのドラフト | 実験 |
| リアルタイム エンドポイント | Web サービス |

### <a name="migrating-to-the-designer"></a>デザイナーへの移行

デザイナーでは、既存のビジュアル インターフェイス実験と Web サービスをパイプラインとリアルタイム エンドポイントに変換することができます。 ビジュアル インターフェイスのアセットを移行するには、次の手順を使用します。

[!INCLUDE [migrate from the visual interface](../../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>次の手順

* 予測分析と機械学習の基本について学習する: [チュートリアル: デザイナーを使用して自動車の価格を予測する](tutorial-designer-automobile-price-train-score.md)
* いずれかのサンプルを使用して、ニーズに合わせて変更します。

- [サンプル 1 - 回帰: 自動車の価格を予測する](how-to-designer-sample-regression-automobile-price-basic.md)
- [サンプル 2 - 回帰: 自動車の価格予測のためのアルゴリズムを比較する](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [サンプル 3 -特徴選択による分類: 収入予測](how-to-designer-sample-classification-predict-income.md)
- [サンプル 4 - 分類: 信用リスクを予測する (費用重視)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [サンプル 5 - 分類:顧客離れを予測する](how-to-designer-sample-classification-churn.md)
- [サンプル 6 - 分類:フライトの遅延を予測する](how-to-designer-sample-classification-flight-delay.md)
- [サンプル 7 - テキスト分類:Wikipedia SP 500 データセット](how-to-designer-sample-text-classification.md)


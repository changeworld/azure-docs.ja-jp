---
title: Azure Machine Learning と Machine Learning Studio (クラシック)
description: Azure Machine Learning と Machine Learning Studio (クラシック) の違い
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: a3122a3ae1687369b87d193efc693b3b7c659aac
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75974133"
---
# <a name="how-azure-machine-learning-differs-from-machine-learning-studio-classic"></a>Azure Machine Learning と Machine Learning Studio (クラシック) の違い

この記事では、Azure Machine Learning と Machine Learning Studio (クラシック) の特徴、機能、およびインターフェイスを比較します。 

## <a name="about-machine-learning-studio-classic"></a>Machine Learning Studio (クラシック) について
[Machine Learning Studio (クラシック)](studio/what-is-ml-studio.md) は、共同作業に対応するドラッグ アンド ドロップ式の視覚的なワークスペースです。コードを記述することなく機械学習ソリューションを構築、テスト、およびデプロイすることができます。 事前に構築および構成された機械学習アルゴリズムとデータ処理モジュールが、専用コンピューティング プラットフォームと共に使用されます。

## <a name="about-azure-machine-learning"></a>Azure Machine Learning について

一方、[Azure Machine Learning](overview-what-is-azure-ml.md) には、デザイナー (プレビュー) と呼ばれる Web インターフェイスと、複数の SDK および CLI が**両方**用意されており、すぐにデータを準備し、機械学習モデルのトレーニングとデプロイを行うことができます。 また、Azure Machine Learning では、スケール、複数のフレームワークのサポート、高度な ML 機能 (自動機械学習、パイプライン サポートなど) を利用できます。

Azure Machine Learning デザイナーでは、Studio (クラシック) と同様のドラッグ アンド ドロップ操作を利用できます。 ただし、Studio (クラシック) の専用コンピューティング プラットフォームとは異なり、デザイナーではユーザー独自のコンピューティング リソースが使用されています。また、スケーラブルで、Azure Machine Learning に完全に統合されています。  

> [!TIP]
> Machine Learning Studio (クラシック) を現在使用しているか評価しているお客様は、[Azure Machine Learning デザイナー](https://docs.microsoft.com/azure/machine-learning/concept-designer) (プレビュー) をぜひお試しください。これは、ドラッグ アンド ドロップ ML モジュールが備わっているのに __加え__、スケーラビリティ、バージョン コントロール、エンタープライズ セキュリティも保証されます。

## <a name="comparison-azure-machine-learning-vs-machine-learning-studio-classic"></a>比較:Azure Machine Learning と Machine Learning Studio (クラシック)

以下で簡単に比較します。

||  Azure Machine Learning デザイナー|Studio (クラシック) |
|---| --- | --- |
||デザイナーはプレビュー段階で、Azure Machine Learning は GA です|一般提供 (GA) | 
|ドラッグ アンド ドロップ インターフェイス| はい | はい|
|実験| コンピューティング先に合わせてスケーリング|スケーリング (10 GB のトレーニング データの上限) | 
|インターフェイスのモジュール| [多数の人気のあるモジュール](algorithm-module-reference/module-reference.md) | 多数 |
|コンピューティング ターゲットのトレーニング| AML コンピューティング (GPU、CPU)|独自のコンピューティング先、CPU のみ|
|推論コンピューティング先| リアルタイム推論用の Azure Kubernetes Service <br/>バッチ推論用の AML コンピューティング|独自の Web サービス形式 (カスタマイズ不可) | 
|ML パイプライン| パイプラインの作成 <br/> 公開されたパイプライン <br/> パイプライン エンドポイント <br/> [ML パイプラインの詳細はこちら](concept-ml-pipelines.md)|サポートされていません | 
|ML 操作| 構成可能なデプロイ、モデル、およびパイプラインのバージョン管理|基本的なモデル管理とデプロイ | 
|モデル| 標準形式。トレーニング ジョブに依存|専用。ポータブル形式ではありません。| 
|自動化されたモデルのトレーニング|デザイナーではサポート対象外ですが、インターフェイスおよび SDK でサポートされます。| いいえ | 

## <a name="get-started-with-azure-machine-learning"></a>Azure Machine Learning の利用を開始

次のリソースは、Azure Machine Learning の利用を開始するのに役立ちます

- [Azure Machine Learning の概要](tutorial-first-experiment-automated-ml.md)に関するページをお読みください 

- [最初のデザイナー パイプラインを作成](tutorial-designer-automobile-price-train-score.md)して、自動価格を予測します。

![Azure Machine Learning デザイナーの例](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>次のステップ

Azure Machine Learning には、デザイナーのドラッグ アンド ドロップ機能のほか、他のツールも用意されています。  
  + [Python ノートブックを使用して、ML モデルをトレーニングおよびデプロイする](tutorial-1st-experiment-sdk-setup.md)
  + [R Markdown を使用して、ML モデルをトレーニングおよびデプロイする](tutorial-1st-r-experiment.md) 
  + [自動化された機械学習を使用して、ML モデルをトレーニングおよびデプロイする](tutorial-designer-automobile-price-train-score.md) 
  + [Machine Learning CLI を使用して、モデルをトレーニングおよびデプロイする](tutorial-train-deploy-model-cli.md)


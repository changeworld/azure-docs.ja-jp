---
title: Azure Machine Learning と Machine Learning Studio (クラシック)
description: Azure Machine Learning と Machine Learning Studio (classic) の違い
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 03/25/2020
ms.openlocfilehash: 5577a9847ff405397c553028a6dfdf2df80d03fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371834"
---
# <a name="azure-machine-learning-vs-machine-learning-studio-classic"></a>Azure Machine Learning と Machine Learning Studio (classic) の違い

この記事では、Azure Machine Learning と Machine Learning Studio (classic) の違いについて説明します。 

Azure Machine Learning には、Python と R の SDK に**加え**、機械学習モデルを作成してデプロイするための "ドラッグ アンド ドロップ" 式のデザイナーが用意されています。 Studio (クラシック) に用意されているのは、スタンドアロンのドラッグ アンド ドロップ エクスペリエンスのみです。

新しく使い始める方には、最先端の機械学習ツールを豊富に備えた Azure Machine Learning をお勧めします。

## <a name="quick-comparison"></a>比較早見表

次の表は、Azure Machine Learning と Studio (クラシック) の主な違いのいくつかをまとめたものです。

| | Machine Learning Studio (クラシック) | Azure Machine Learning |
|---| --- | --- |
| ドラッグ アンド ドロップ インターフェイス | サポートされています | サポートされています - [Azure Machine Learning デザイナー (プレビュー)](concept-designer.md) | 
| 実験 | スケーラブル (10 GB トレーニング データの上限) | コンピューティング先に合わせてスケーリング |
| コンピューティング ターゲットのトレーニング | 独自のコンピューティング ターゲット、CPU のサポートのみ | カスタマイズできる広範な[トレーニング コンピューティング先](concept-compute-target.md#train)。 GPU と CPU のサポートが含まれます | 
| デプロイのコンピューティング ターゲット | 独自の Web サービス形式 (カスタマイズ不可) | カスタマイズできる広範な[デプロイ コンピューティング先](concept-compute-target.md#deploy)。 GPU と CPU のサポートが含まれます |
| ML パイプライン | サポートされていません | ワークフローを自動化する柔軟性の高いモジュール式の[パイプライン](concept-ml-pipelines.md)を作成します |
| MLOps | 基本的なモデル管理とデプロイ | エンティティのバージョン管理 (モデル、データ、ワークフロー)、ワークフロー オートメーション、CICD ツールとの統合、[その他](concept-model-management-and-deployment.md) |
| モデル形式 | 専用の形式、Studio (クラシック) のみ | トレーニング ジョブの種類に応じて複数の形式がサポートされます |
| 自動化されたモデル トレーニングとハイパーパラメーター調整 |  サポートされていません | [SDK とビジュアル ワークスペースでサポートされます](concept-automated-ml.md) | 
| データ ドリフト検出 | サポートされていません | [SDK とビジュアル ワークスペースでサポートされます](how-to-monitor-datasets.md) |


## <a name="migrate-from-machine-learning-studio-classic"></a>Machine Learning Studio (classic) からの移行

現在、Studio (classic) の資産を Azure Machine Learning デザイナー (プレビュー) に移行する方法はありません。 現在の Studio (classic) ユーザーは、既にある機械学習の資産を引き続き使用できます。 ただし Microsoft では、すべてのユーザーにデザイナーの使用をお勧めします。デザイナーには、使い慣れたドラッグ アンド ドロップ式のエクスペリエンスと改良されたワークフローに**加え**、スケーラビリティ、バージョン コントロール、エンタープライズ セキュリティが備わっています。

## <a name="get-started-with-azure-machine-learning"></a>Azure Machine Learning の利用を開始

次のリソースは、Azure Machine Learning の利用を開始するのに役立ちます。 

- [Azure Machine Learning の概要](overview-what-is-azure-ml.md)に関するページをお読みください。

- [Python SDK で初めての実験を作成](tutorial-1st-experiment-sdk-setup.md)します。

- [最初のデザイナー パイプラインを作成](tutorial-designer-automobile-price-train-score.md)して、自動価格を予測します。

![Azure Machine Learning デザイナーの例](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>次のステップ

Azure Machine Learning には、デザイナーのドラッグ アンド ドロップ機能のほか、他のツールも用意されています。  
  + [Python ノートブックを使用して、ML モデルをトレーニングおよびデプロイする](tutorial-1st-experiment-sdk-setup.md)
  + [R Markdown を使用して、ML モデルをトレーニングおよびデプロイする](tutorial-1st-r-experiment.md) 
  + [自動化された機械学習を使用して、ML モデルをトレーニングおよびデプロイする](tutorial-first-experiment-automated-ml.md)  
  + [Machine Learning CLI を使用して、モデルをトレーニングおよびデプロイする](tutorial-train-deploy-model-cli.md)


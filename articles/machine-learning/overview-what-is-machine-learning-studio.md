---
title: Azure Machine Learning スタジオとは
description: スタジオは、Azure Machine Learning ワークスペース用の Web ポータルです。 このスタジオでは、包括的なデータ サイエンス プラットフォームのために、コードなしのエクスペリエンスとコードファースト エクスペリエンスを組み合わせています。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: peterclu
ms.author: peterlu
ms.date: 08/24/2020
ms.openlocfilehash: 4212c76d052fe1f272963003e836425b50d6f105
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677616"
---
# <a name="what-is-azure-machine-learning-studio"></a>Azure Machine Learning スタジオとは

この記事では、Azure Machine Learning スタジオについて説明します。これは、[Azure Machine Learning](overview-what-is-azure-ml.md) でのデータ サイエンティスト開発者向けの Web ポータルです。 このスタジオでは、包括的なデータ サイエンス プラットフォームのために、コードなしのエクスペリエンスとコードファースト エクスペリエンスを組み合わせています。

この記事では、次のことについて説明します。
>[!div class="checklist"]
> - スタジオで[機械学習プロジェクトを作成する](#author-machine-learning-projects)方法。
> - スタジオで[アセットとリソースを管理する](#manage-assets-and-resources)方法。
> - [Azure Machine Learning スタジオと ML Studio (classic)](#ml-studio-classic-vs-azure-machine-learning-studio) の違い。

オペレーティング システムと互換性のある最新ブラウザーを使うことをお勧めします。 次のブラウザーがサポートされています。
  * Microsoft Edge (新しい Microsoft Edge の最新バージョンです。 Microsoft Edge レガシではありません)。
  * Safari (最新バージョン、Mac のみ)
  * Chrome (最新バージョン)
  * Firefox (最新バージョン)

## <a name="author-machine-learning-projects"></a>機械学習プロジェクトを作成する

スタジオでは、プロジェクトの種類とユーザー エクスペリエンスのレベルに応じて、複数の作成エクスペリエンスが提供されます。

+ **Notebook**

  スタジオに直接統合されているマネージド [Jupyter Notebook サーバー](how-to-run-jupyter-notebooks.md)で独自のコードを記述して実行します。 

:::image type="content" source="media/overview-what-is-azure-ml-studio/notebooks.gif" alt-text="スクリーンショット: ノートブックでコードを記述して実行する":::

+ **Azure Machine Learning デザイナー**

  デザイナーを使用すると、コードを書かなくても、機械学習モデルのトレーニングとデプロイを行うことができます。 ML パイプラインを作成するには、データセットとモジュールをドラッグ アンド ドロップします。 [デザイナーのチュートリアル](tutorial-designer-automobile-price-train-score.md)をお試しください。

    ![Azure Machine Learning デザイナーの例](media/concept-designer/designer-drag-and-drop.gif)

+ **自動化された機械学習の UI**

  使いやすいインターフェイスで、[自動化された ML 実験](tutorial-first-experiment-automated-ml.md)を作成する方法を学ぶことができます。 

  [![Azure Machine Learning Studio のナビゲーション パネル](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

+ **データのラベル付け**

    [Azure Machine Learning のデータのラベル付け](how-to-create-labeling-projects.md)を使用すると、データのラベル付けプロジェクトを効率的にコーディネートすることができます。

## <a name="manage-assets-and-resources"></a>アセットとリソースを管理する

機械学習アセットをブラウザーで直接管理します。 アセットは、シームレスなエクスペリエンスを実現するために、同じワークスペースで SDK とスタジオの間で共有されます。 スタジオを使用して、以下を管理します。

- モデル
- データセット
- データストア
- コンピューティング リソース
- ノートブック
- 実験
- 実行ログ
- パイプライン 
- パイプライン エンドポイント

経験豊富な開発者であっても、スタジオによってワークスペース リソースの管理方法が簡略化されます。

## <a name="ml-studio-classic-vs-azure-machine-learning-studio"></a>ML Studio (classic) と Azure Machine Learning スタジオ

2015 年にリリースされた **ML Studio (classic)** は、初めてのドラッグ アンド ドロップ機械学習ビルダーでした。 それは、ビジュアル エクスペリエンスだけを提供するスタンドアロンのサービスです。 Studio (classic) は、Azure Machine Learning との相互運用はできません。

**Azure Machine Learning** は、完全なデータ サイエンス プラットフォームを提供する、独立した最新のサービスです。 コード ファースト エクスペリエンスと少量コード エクスペリエンスの両方がサポートされています。

**Azure Machine Learning スタジオ** は Azure Machine Learning "*内の*" Web ポータルであり、プロジェクト作成とアセット管理のための少量のコードおよびコードなしのオプションが用意されています。 

新しく使い始める方には、最新の各種データ サイエンス ツールのために、ML Studio (classic) ではなく、**Azure Machine Learning** を選択することをお勧めします。

### <a name="feature-comparison"></a>機能の比較

次の表は、ML Studio (classic) と Azure Machine Learning との主な違いをまとめたものです。

| 特徴量 | ML Studio (クラシック) | Azure Machine Learning |
|---| --- | --- |
| ドラッグ アンド ドロップ インターフェイス | クラシック エクスペリエンス | 更新されたエクスペリエンス - [Azure Machine Learning デザイナー](concept-designer.md)| 
| コード SDK | サポートされていない | [Azure Machine Learning Python](/python/api/overview/azure/ml/) および [R](https://github.com/Azure/azureml-sdk-for-r) SDK との完全な統合 |
| 実験 | スケーラブル (10 GB トレーニング データの上限) | コンピューティング先に合わせてスケーリング |
| コンピューティング ターゲットのトレーニング | 独自のコンピューティング ターゲット、CPU のサポートのみ | カスタマイズできる広範な[トレーニング コンピューティング先](concept-compute-target.md#train)。 GPU と CPU のサポートが含まれます | 
| デプロイのコンピューティング ターゲット | 独自の Web サービス形式 (カスタマイズ不可) | カスタマイズできる広範な[デプロイ コンピューティング先](concept-compute-target.md#deploy)。 GPU と CPU のサポートが含まれます |
| ML パイプライン | サポートされていません | ワークフローを自動化する柔軟性の高いモジュール式の[パイプライン](concept-ml-pipelines.md)を作成します |
| MLOps | 基本的なモデル管理とデプロイ。CPU のみのデプロイ | エンティティのバージョン管理 (モデル、データ、ワークフロー)、ワークフロー オートメーション、CICD ツールとの統合、CPU および GPU のデプロイ、[その他](concept-model-management-and-deployment.md) |
| モデル形式 | 専用の形式、Studio (クラシック) のみ | トレーニング ジョブの種類に応じて複数の形式がサポートされます |
| 自動化されたモデル トレーニングとハイパーパラメーター調整 |  サポートされていません | [サポートされています](concept-automated-ml.md)。 コード ファースト オプションとコードなしオプション。 | 
| データ ドリフト検出 | サポートされていません | [サポートされています](how-to-monitor-datasets.md) |
| プロジェクトのラベル付けデータ | サポートされていません | [サポートされています](how-to-create-labeling-projects.md) |

## <a name="troubleshooting"></a>トラブルシューティング

* **スタジオにユーザー インターフェイス項目が見つからない**: Azure のロールベースのアクセス制御を使用すると、Azure Machine Learning で実行できるアクションを制限できます。 これらの制限によって、Azure Machine Learning スタジオにユーザー インターフェイス項目を表示しないようにすることができます。 たとえば、コンピューティング インスタンスを作成できないロールがユーザーに割り当てられている場合、コンピューティング インスタンスを作成するオプションは、スタジオには表示されません。 詳細については、「[ユーザーとロールを管理する](how-to-assign-roles.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

[スタジオ](https://ml.azure.com)にアクセスするか、以下のチュートリアルを使用して別の作成オプションを調べます。  

- + [個人の開発環境で使用を開始する](tutorial-1st-experiment-sdk-setup-local.md)
  + [コンピューティング インスタンスで Jupyter Notebook を使用してモデルのトレーニングとデプロイを行う](tutorial-1st-experiment-sdk-setup.md)
  + [自動化された機械学習を使用して、モデルをトレーニングおよびデプロイする](tutorial-first-experiment-automated-ml.md)  
  + [デザイナーを使用して、モデルをトレーニングおよびデプロイする](tutorial-designer-automobile-price-train-score.md)
  + [セキュリティで保護された仮想ネットワークでスタジオを使用する](how-to-enable-studio-virtual-network.md)
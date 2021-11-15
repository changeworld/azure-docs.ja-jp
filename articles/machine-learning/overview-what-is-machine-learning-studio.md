---
title: Azure Machine Learning スタジオとは
description: スタジオは、Azure Machine Learning ワークスペース用の Web ポータルです。 このスタジオでは、包括的なデータ サイエンス プラットフォームのために、コードなしのエクスペリエンスとコードファースト エクスペリエンスを組み合わせています。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: peterclu
ms.author: peterlu
ms.date: 10/21/2021
adobe-target: true
ms.openlocfilehash: 09f4a047d3cccb5b34834a9315893c4c1687bc1b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556238"
---
# <a name="what-is-azure-machine-learning-studio"></a>Azure Machine Learning スタジオとは

この記事では、Azure Machine Learning スタジオについて説明します。これは、[Azure Machine Learning](overview-what-is-azure-machine-learning.md) でのデータ サイエンティスト開発者向けの Web ポータルです。 このスタジオでは、包括的なデータ サイエンス プラットフォームのために、コードなしのエクスペリエンスとコードファースト エクスペリエンスを組み合わせています。

この記事では、次のことについて説明します。
>[!div class="checklist"]
> - スタジオで[機械学習プロジェクトを作成する](#author-machine-learning-projects)方法。
> - スタジオで[アセットとリソースを管理する](#manage-assets-and-resources)方法。
> - [Azure Machine Learning スタジオと ML Studio (classic)](#ml-studio-classic-vs-azure-machine-learning-studio) の違い。

オペレーティング システムと互換性のある最新ブラウザーを使うことをお勧めします。 次のブラウザーがサポートされています。
  * Microsoft Edge (最新バージョン)
  * Safari (最新バージョン、Mac のみ)
  * Chrome (最新バージョン)
  * Firefox (最新バージョン)

## <a name="author-machine-learning-projects"></a>機械学習プロジェクトを作成する

スタジオでは、プロジェクトの種類とユーザー エクスペリエンスのレベルに応じて、複数の作成エクスペリエンスが提供されます。

+ **Notebook**

  スタジオに直接統合されているマネージド [Jupyter Notebook サーバー](how-to-run-jupyter-notebooks.md)で独自のコードを記述して実行します。 

:::image type="content" source="media/overview-what-is-azure-ml-studio/notebooks.gif" alt-text="スクリーンショット: ノートブックでコードを記述して実行する":::

+ **Azure Machine Learning デザイナー**

  デザイナーを使用すると、コードを書かなくても、機械学習モデルのトレーニングとデプロイを行うことができます。 ML パイプラインを作成するには、データセットとコンポーネントをドラッグ アンド ドロップします。 [デザイナーのチュートリアル](tutorial-designer-automobile-price-train-score.md)をお試しください。

    ![Azure Machine Learning デザイナーの例](media/concept-designer/designer-drag-and-drop.gif)

+ **自動化された機械学習の UI**

  使いやすいインターフェイスで、[自動化された ML 実験](tutorial-first-experiment-automated-ml.md)を作成する方法を学ぶことができます。 

  ![Azure Machine Learning スタジオのナビゲーション ウィンドウの AutoML](./media/overview-what-is-azure-ml-studio/azure-machine-learning-automated-ml-ui.jpg)

+ **データのラベル付け**

    Azure Machine Learning のデータのラベル付けを使用すると、[画像のラベル付け](how-to-create-image-labeling-projects.md)または[テキストのラベル付け](how-to-create-text-labeling-projects.md)プロジェクトを効率的にコーディネートできます。

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

[!INCLUDE [ML Studio (classic) retirement](../../includes/machine-learning-studio-classic-deprecation.md)]

2015 年にリリースされた **ML Studio (クラシック)** は、Azure で初めてのドラッグ アンド ドロップ機械学習ビルダーでした。 **ML Studio (classic)** は、ビジュアル エクスペリエンスだけを提供するスタンドアロンのサービスです。 Studio (classic) は、Azure Machine Learning との相互運用はできません。

**Azure Machine Learning** は、完全なデータ サイエンス プラットフォームを提供する、独立した最新のサービスです。 コード ファースト エクスペリエンスと少量コード エクスペリエンスの両方がサポートされています。

**Azure Machine Learning スタジオ** は Azure Machine Learning "*内の*" Web ポータルであり、プロジェクト作成とアセット管理のための少量のコードおよびコードなしのオプションが用意されています。 

新しいユーザーの場合は、ML Studio (クラシック) の代わりに **Azure Machine Learning** を選択してください。 完全な ML プラットフォームとして、Azure Machine Learning では以下が提供されます。

- 大規模なトレーニング用のスケーラブルなコンピューティング クラスター。
- エンタープライズ セキュリティとガバナンス。
- 一般的なオープンソース ツールとの相互運用が可能。
- エンド ツー エンドの MLOps。

### <a name="feature-comparison"></a>機能の比較

[!INCLUDE [aml-compare-classic](../../includes/machine-learning-compare-classic-aml.md)]

## <a name="troubleshooting"></a>トラブルシューティング

* **スタジオにユーザー インターフェイス項目が見つからない**: Azure のロールベースのアクセス制御を使用すると、Azure Machine Learning で実行できるアクションを制限できます。 これらの制限によって、Azure Machine Learning スタジオにユーザー インターフェイス項目を表示しないようにすることができます。 たとえば、コンピューティング インスタンスを作成できないロールがユーザーに割り当てられている場合、コンピューティング インスタンスを作成するオプションは、スタジオには表示されません。 詳細については、「[ユーザーとロールを管理する](how-to-assign-roles.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

[スタジオ](https://ml.azure.com)にアクセスするか、以下のチュートリアルを使用して別の作成オプションを調べます。  

最初に、[Azure Machine Learning の利用開始作に関するクイックスタート](quickstart-create-resources.md)をお読みください。  その後、これらのリソースを使用して、お好きな方法で最初の実験を作成します。

  + ["Hello world!" Python スクリプトを実行する (パート 1/3)](tutorial-1st-experiment-hello-world.md)
  + [Jupyter Notebook を使用して画像分類モデルをトレーニングする](tutorial-train-models-with-aml.md)
  + [自動化された機械学習を使用して、モデルをトレーニングおよびデプロイする](tutorial-first-experiment-automated-ml.md)  
  + [デザイナーを使用して、モデルをトレーニングおよびデプロイする](tutorial-designer-automobile-price-train-score.md)
  + [セキュリティで保護された仮想ネットワークでスタジオを使用する](how-to-enable-studio-virtual-network.md)
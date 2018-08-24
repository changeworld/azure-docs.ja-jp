---
title: Azure Machine Learning Package for Computer Vision と Team Data Science Process (TDSP) を使用した画像分類 | Microsoft Docs
description: Team Data Science Process (TDSP) と Azure Machine Learning Package for Computer Vision を使用した画像分類について説明します。
services: machine-learning, team-data-science-process
documentationcenter: ''
author: deguhath
ms.author: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.openlocfilehash: 427ea1f3f22855b2c54beacbfb89a8f7fd37cce0
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246649"
---
# <a name="skin-cancer-image-classification-with-the-azure-machine-learning-package-for-computer-vision-and-team-data-science-process"></a>Azure Machine Learning Package for Computer Vision と Team Data Science Process を使用した皮膚がんの画像分類

この記事では、[Azure Machine Learning Package for Computer Vision](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) を使用して、*画像分類*モデルのトレーニング、テスト、およびデプロイを行う方法について示します。 このサンプルでは、[Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation) で Team Data Science Process (TDSP) 構造とテンプレートを使用します。 このチュートリアルには、完全なサンプルが用意されています。 ディープ ラーニング フレームワークとして [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) が使用され、[データ サイエンス仮想マシン](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) GPU マシンでトレーニングが実行されます。 デプロイでは、Azure Machine Learning の操作化 CLI を使用します。

コンピューター ビジョンの分野における多くのアプリケーションは、画像分類の問題として構成されます。 これには、"画像にオブジェクトが存在するか?" などの簡単な質問に答えるモデルの構築が含まれます。 ここで、オブジェクトは犬、自動車、船などです。 また、"この患者の網膜のスキャンには、どの程度の眼病の深刻度が示されているか?" といったより複雑な質問への回答となるモデルの構築も含まれます。 Azure Machine Learning Package for Computer Vision では、イメージ分類のデータ処理とモデリング パイプラインが効率化されます。 

## <a name="link-to-the-github-repository"></a>GitHub リポジトリへのリンク
この記事はサンプルの概要を説明するドキュメントです。 さらに詳細なドキュメントについては、[GitHub サイト](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification)でご確認ください。

## <a name="team-data-science-process-walkthrough"></a>Team Data Science Process のチュートリアル

このチュートリアルでは、[Team Data Science Process](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) のライフサイクルを使用します。 チュートリアルに含まれるライフサイクルの手順は次のとおりです。

### <a name="1-data-acquisitionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1.データの取得](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
画像分類タスクには International Skin Imaging Collaboration (ISIC) データセットが使用されます。 ISIC は、黒色腫について研究し、その死亡率を下げるために役立つ皮膚のデジタル画像のアプリケーションを普及するための学術機関と産業界とのパートナーシップです。 [ISIC アーカイブ](https://isic-archive.com/#images)には、良性と悪性両方の各レベルの皮膚病変画像が 13,000 件以上含まれています。 画像のサンプルを ISIC アーカイブからダウンロードします。

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2.モデリング](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
モデリングの手順では、次のサブステップが実行されます。

#### <a name="dataset-creation"></a>データセットの作成

Azure Machine Learning Package for Computer Vision でデータセット オブジェクトを作成するには、ローカル ディスク上の画像のルート ディレクトリを指定します。 

#### <a name="image-visualization-and-annotation"></a>画像の視覚化と注釈

データセット オブジェクト内にある画像を視覚化し、必要に応じてラベルを修正します。

#### <a name="image-augmentation"></a>画像の拡張

[imgaug](https://github.com/aleju/imgaug) ライブラリに記述されている変換を使用してデータセット オブジェクトを拡張します。

#### <a name="dnn-model-definition"></a>DNN モデルの定義

トレーニングの手順で使用されるモデルのアーキテクチャを定義します。 Azure Machine Learning Package for Computer Vision では、次の 6 つのトレーニング済みのディープ ニューラル ネットワーク モデルがサポートされています: AlexNet、Resnet-18、Resnet-34、Resnet-50、Resnet-101、Resnet-152。

#### <a name="classifier-training"></a>分類器のトレーニング

既定またはカスタム パラメーターでニューラル ネットワークをトレーニングします。

#### <a name="evaluation-and-visualization"></a>評価と視覚化

独立したテスト データセットに対するトレーニング済みモデルのパフォーマンスを評価する機能が提供されます。 評価メトリックには、精度、有効桁数、再現率、および ROC 曲線が含まれます。

このサブステップは、対応する Jupyter Notebook で詳しく説明されています。 この Notebook では、学習率、ミニ バッチ サイズ、ドロップアウト率などのパラメーターを変換してモデルのパフォーマンスをさらに改善するためのガイドラインも提供されています。

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3.デプロイ](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

この手順ではモデリングの手順から生成されたモデルを操作化します。 前提条件と必要な設定を導入します。 Web サービスの消費についても説明します。 このチュートリアルでは、Azure Machine Learning Package for Computer Vision を使用してディープ ラーニング モデルを構築し、Azure 内でモデルを操作化する方法について説明します。

## <a name="next-steps"></a>次の手順
- [Azure Machine Learning Package for Computer Vision](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) の追加のドキュメントを参照する。
- [Team Data Science Process](https://aka.ms/tdsp) のドキュメントを参照して作業を開始する。


## <a name="references"></a>参照

* [Azure Machine Learning Pckage for Computer Vision](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [データ サイエンス仮想マシン](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)


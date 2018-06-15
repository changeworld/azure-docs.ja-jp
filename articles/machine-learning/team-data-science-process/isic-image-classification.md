---
title: Azure Machine Learning (AML) Package for Computer Vision (AMLPCV) と Team Data Science Process (TDSP) を使用した画像分類 | Microsoft Docs
description: TDSP (Team Data Science Process) と AMLPCV の画像分類での使用について説明します
services: machine-learning, team-data-science-process
documentationcenter: ''
author: xibingao
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: xibingao
ms.openlocfilehash: a3dcfd8a9292d31c7342b8d50ec58c0da53318d3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837220"
---
# <a name="skin-cancer-image-classification-with-azure-machine-learning-aml-package-for-computer-vision-amlpcv-and-team-data-science-process-tdsp"></a>Azure Machine Learning (AML) Package for Computer Vision (AMLPCV) と Team Data Science Process (TDSP) を使用した皮膚がんの画像分類

## <a name="introduction"></a>はじめに

この記事では、[Azure Machine Learning Package for Computer Vision (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) を使用して、**画像分類**モデルのトレーニング、テスト、およびデプロイを行う方法について示します。 このサンプルでは、[Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation) で TDSP 構造とテンプレートを使用します。 完全なサンプルは、このチュートリアルに用意されています。 ディープ ラーニング フレームワークとして [CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) が使用され、[データ サイエンス VM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) GPU マシンでトレーニングが実行されます。 デプロイでは、Azure ML の操作化 CLI を使用します。

コンピューター ビジョンの分野における多くのアプリケーションは、画像分類の問題として構成されます。 これには、"画像にオブジェクトが存在するか?"  (オブジェクトは*犬*、*自動車*、*船*など) のような質問や、"この患者の網膜のスキャンには、どの程度の眼病の深刻度が示されているか?" といったより複雑な質問への回答となるモデルの構築が含まれます。 AMLPCV は画像分類のデータ処理とパイプラインのモデリングを簡略化します。 

## <a name="link-to-github-repository"></a>GitHub リポジトリへのリンク
ここでは、サンプルに関する概要ドキュメントを提供します。 さらに詳細なドキュメントについては、[GitHub サイト](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification)でご確認ください。

## <a name="team-data-science-process-tdsp-walkthrough-with-amlpcv"></a>AMLPCV を使用した Team Data Science Process (TDSP) のチュートリアル

このチュートリアルでは、[Team Data Science Process (TDSP)](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) のライフサイクルを使用します。

チュートリアルに含まれるライフサイクルの手順は次のとおりです。

### <a name="1-data-acquisionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1.データの取得](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
ISIC データセットは画像分類タスクに使用されます。 ISIC (The International Skin Imaging Collaboration) は、黒色腫について研究し、その死亡率を下げるために役立つ皮膚のデジタル画像のアプリケーションを普及するための学術機関と産業界とのパートナーシップです。 [ISIC アーカイブ](https://isic-archive.com/#images)には、良性と悪性両方の各レベルの皮膚病変画像が 13,000 件以上含まれています。 画像のサンプルを ISIC アーカイブからダウンロードします。

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2.モデリング](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
モデリングの手順では、次のサブステップが実行されます。 

<b>2.1 データセットの作成</b><br>

AMLPCV でデータセット オブジェクトを生成するには、ローカル ディスク上の画像のルート ディレクトリを指定します。 

<b>2.2 画像の視覚化と注釈</b><br>

データセット オブジェクト内にある画像を視覚化し、必要に応じて一部のラベルを修正します。

<b>2.3 画像の拡張</b><br>

[imgaug](https://github.com/aleju/imgaug) ライブラリに記述されている変換を使用してデータセット オブジェクトを拡張します。

<b>2.4 DNN モデルの定義</b><br>

トレーニングの手順で使用されるモデルのアーキテクチャを定義します。 AMLPCV では AlexNet、Resnet-18、Resnet-34、Resnet-50、Resnet-101、Resnet-152 という 6 種類のトレーニング別のディープ ニューラル ネットワーク モデルがサポートされています。

<b>2.5 分類器のトレーニング</b><br>

既定またはカスタム パラメーターでニューラル ネットワークをトレーニングします。

<b>2.6 評価と視覚化</b><br>

このサブステップでは、独立したテスト データセットに対するトレーニング済みモデルのパフォーマンスを評価する機能が提供されます。 評価メトリックには、精度、有効桁数と取り消し、および ROC 曲線が含まれます。

これらのサブステップは、対応する Jupyter Notebook で詳しく説明されています。 学習率、ミニ バッチ サイズ、ドロップアウト率などのパラメーターを変換してモデルのパフォーマンスをさらに改善するためのガイドラインも提供しています。

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3.デプロイ](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

この手順ではモデリングの手順から生成されたモデルを操作化します。 操作化の前提条件と設定について説明します。 最後に、Web サービスの消費についても説明します。 このチュートリアルを通して、AMLPCV でディープ ラーニング モデルを構築し、Azure でモデルを操作化する方法を理解できます。

## <a name="next-steps"></a>次の手順
最初に [Azure Machine Learning Package for Computer Vision (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) と [Team Data Science Process (TDSP)](https://aka.ms/tdsp) に関するその他のドキュメントを参照してください。


## <a name="references"></a>参照

* [Azure Machine Learning Package for Computer Vision (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [データ サイエンス VM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)


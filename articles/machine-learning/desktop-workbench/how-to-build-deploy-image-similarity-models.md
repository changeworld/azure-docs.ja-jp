---
title: Machine Learning Package for Computer Vision を使用して画像類似性モデルを構築してデプロイする
description: Azure Machine Learning Package for Computer Vision を使用して、コンピューター ビジョンの画像類似性モデルの構築、トレーニング、テスト、デプロイを行う方法を説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 05/20/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 371903e1ee080d2b98fd46ac4d6d9838416e1335
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973424"
---
# <a name="build-and-deploy-image-similarity-models-with-azure-machine-learning"></a>Azure Machine Learning で画像類似性モデルを構築してデプロイする

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


この記事では、Azure Machine Learning Package for Computer Vision (AMLPCV) を使用して、画像類似性モデルのトレーニング、テスト、およびデプロイを行う方法について説明します。 このパッケージとその詳細なリファレンス ドキュメントの概要については、 https://aka.ms/aml-packages/vision をご覧ください。

コンピューター ビジョンの分野における多数の問題は、画像類似性を使用して解決できます。 これらの問題には、以下のような質問に回答するモデルの構築が含まれます。
+ "_対象物が画像に存在するか。例: "犬"、"猫"、"船"など_"
+ "_この患者の網膜スキャンによって、どの程度の眼病の重症度が立証されるか。_"

このモデルを AMLPCV を使用して構築して配置するときは、次の手順に従います。
1. データセットの作成
2. 画像の視覚化と注釈
3. 画像の拡張
4. ディープ ニューラル ネットワーク (DNN) モデルの定義
5. 分類子のトレーニング
6. 評価と視覚化
7. Web サービスのデプロイ
8. Web サービスのロード テスト

ディープ ラーニング フレームワークとして [CNTK](https://www.microsoft.com/cognitive-toolkit/) が使用され、[ディープ ラーニング データ サイエンス VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) などの GPU を備えたマシンでトレーニングがローカルに実行され、展開では Azure ML Operationalization CLI が使用されます。

## <a name="prerequisites"></a>前提条件

1. Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

1. 次のアカウントとアプリケーションを設定してインストールする必要があります。
   - Azure Machine Learning 実験アカウント 
   - Azure Machine Learning モデル管理アカウント
   - Azure Machine Learning Workbench のインストール

   これら 3 つが作成またはインストールされていない場合は、[Azure Machine Learning のクイックスタートとワークベンチのインストール](../desktop-workbench/quickstart-installation.md)に関する記事に従ってください。 

1. Azure Machine Learning Package for Computer Vision をインストールする必要があります。 このパッケージのインストール方法については、 https://aka.ms/aml-packages/vision をご覧ください。

## <a name="sample-data-and-notebook"></a>サンプル データと Notebook

### <a name="get-the-jupyter-notebook"></a>Jupyter Notebook を取得する

ここで説明するサンプルを実行するための Notebook をダウンロードします。

> [!div class="nextstepaction"]
> [Jupyter Notebook を取得する](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>サンプル データセットを読み込む

次の例では、63 個のテーブルウェアの画像で構成されるデータセットを使用します。 各画像には、4 つの異なるクラス (ボウル、カップ、カトラリー、プレート) のいずれかに属していることを示すラベルが付いています。 この例では、画像の数を少なくして、このサンプルを迅速に実行できるようにしています。 実際には、クラスごとに少なくとも 100 枚の画像を用意する必要があります。 すべての画像は、*"../sample_data/imgs_recycling/"* のサブディレクトリ ("bowl"、"cup"、"cutlery"、および "plate") に配置されます。

![Azure Machine Learning のデータセット](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)

## <a name="next-steps"></a>次の手順

次の記事で、Azure Machine Learning Package for Computer Vision の詳細を確認します。

+ [このモデルの精度を向上させる](how-to-improve-accuracy-for-computer-vision-models.md)方法を確認します。

+ [パッケージの概要](https://aka.ms/aml-packages/vision)を読みます。

+ パッケージの[リファレンス ドキュメント](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision)を確認します。

+ [Azure Machine Learning 用の他の Python パッケージ](reference-python-package-overview.md)を確認します。

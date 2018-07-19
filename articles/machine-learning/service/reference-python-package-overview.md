---
title: Azure Machine Learning 用 Python パッケージ
description: Azure Machine Learning ユーザーが利用できる Python パッケージについて説明します。
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jmartens
author: j-martens
ms.date: 05/07/2018
ms.openlocfilehash: 36322c5fde7c992faa830fe272971b53fbf10211
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37871424"
---
# <a name="python-packages-for-azure-machine-learning"></a>Azure Machine Learning 用 Python パッケージ

Microsoft が提供する Azure Machine Learning 用の Python パッケージについて説明します。 これらのライブラリと関数を他のオープン ソースやサード パーティのパッケージと組み合わせて使用できますが、この専用パッケージを使用するには、インタープリターを提供するサービスまたはコンピューターで Python コードを実行する必要があります。

Azure Machine Learning パッケージは **Python pip でインストールできる Azure Machine Learning の拡張機能**であり、データ サイエンティストと AI 開発者は、この機能を使用して、さまざまな分野に応用できる非常に正確なマシン ラーニング モデルとディープ ラーニング モデルのビルドとデプロイを短時間で実行できます。

<a name="amlpcv"></a>
## <a name="azure-ml-package-for-computer-vision"></a>Azure ML Package for Computer Vision

Azure ML Package for Computer Vision を使用して、画像の分類、オブジェクトの検出、画像の類似度の計算を行うためのディープ ラーニング モデルのビルド、調整、およびデプロイを実行できます。

次の手順で、このパッケージを試してください。
1. パッケージを[ダウンロード](https://aka.ms/aml-packages/vision/download)します。
1. [インストール ドキュメント](https://aka.ms/aml-packages/vision)を読みます。
1. Jupyter ノートブックを使用して [コンピューター ビジョン モデルをビルドしてデプロイ](how-to-build-deploy-image-classification-models.md)します。
1. パッケージの[リファレンス ドキュメント](https://aka.ms/aml-packages/vision)を調べます。

<a name="amlpf"></a>
## <a name="azure-ml-package-for-forecasting"></a>Azure ML Package for Forecasting

Azure ML Package for Forecasting を使用して、金融と需要予測に関連するシナリオ向けの時系列予測モデルを作成してデプロイできます。

次の手順で、このパッケージを試してください。
1. パッケージを[ダウンロード](https://aka.ms/aml-packages/forecasting/download)します。
1. [インストール ドキュメント](https://aka.ms/aml-packages/forecasting)を読みます。
1. Jupyter ノートブックを使用して [予測モデルをビルドしてデプロイ](how-to-build-deploy-forecast-models.md)します。
1. パッケージの[リファレンス ドキュメント](https://aka.ms/aml-packages/forecasting)を調べます。

<a name="amlpta"></a>
## <a name="azure-ml-package-for-text-analytics"></a>Azure ML Package for Text Analytics

Azure ML Package for Text Analytics を使用して、テキストの分類、カスタム エンティティの抽出、および単語の埋め込みを行うためのディープ ラーニング モデルをビルドできます。

次の手順で、このパッケージを試してください。
1. パッケージを[ダウンロード](https://aka.ms/aml-packages/text/download)します。
1. [インストール ドキュメント](https://aka.ms/aml-packages/text)を読みます。
1. Jupyter ノートブックを使用して [テキスト分類をビルドしてデプロイ](how-to-build-deploy-text-classification-models.md)します。
1. パッケージの[リファレンス ドキュメント](https://aka.ms/aml-packages/text)を調べます。

<a name="amlrealtimeai"></a>
## <a name="amlrealtimeai-fpga-acceleration"></a>amlrealtimeai (FPGA アクセラレーション)

データ サイエンティストと AI 開発者は、Azure Machine Learning Hardware Acceleration パッケージを使用して、ResNet 50 の量子化されたバージョンで画像の特徴を抽出し、これらの特徴に基づいて分類子をトレーニングした後、モデルを Azure 上の [Field Programmable Gate Array (FPGA)](concept-accelerate-with-fpgas.md) にデプロイして、待機時間が極端に短い推論を行うことができます。

次の手順で、このパッケージを試してください。
1. パッケージを[ダウンロード](https://aka.ms/aml-real-time-ai-package)します。
1. [インストール ドキュメント](reference-fpga-package-overview.md)を読みます。
1. [モデルを FPGA にデプロイ](how-to-deploy-fpga-web-service.md)します。


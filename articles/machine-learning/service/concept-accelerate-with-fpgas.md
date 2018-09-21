---
title: FPGA とは – Project Brainwave – Azure Machine Learning
description: FPGA を使用してモデルとディープ ニューラル ネットワークを高速化する方法を説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/31/2018
ms.openlocfilehash: 5c8efcbb5f2e9014c9edabfc2aee37c8c29ea0b4
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2018
ms.locfileid: "35634300"
---
# <a name="what-is-fpga-and-project-brainwave"></a>FPGA および Project Brainwave とは

この記事では、FPGA (field-programmable gate arrays) について紹介し、リアルタイムの AI を提供するために FPGA がどのように Azure の機械学習と統合されているかを説明します。

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA とCPU、GPU、ASIC の違い

FPGA にはプログラミング可能なロジック ブロックの配列と、製造後にさまざまな方法でブロックを構成できるようにする再構成可能なインターコネクトの階層が含まれています。

FPGA は、その他のチップと比較して、プログラミング可能であることとパフォーマンスを兼ね備えています。

![Azure Machine Learning における FPGA の比較](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

- **CPU (中央処理装置)** は汎用のプロセッサです。 CPU のパフォーマンスは、画像やビデオの処理には最適ではありません。
- **GPU (グラフィック処理装置)** は並列処理を提供し、AI 計算処理の人気のある選択肢となっています。 GPU による並列処理では、CPU よりも高速にイメージのレンダリングが行われる結果となります。
- **ASIC (Application-specific integrated circuits)**。Google の TensorFlow Processor Unit のような ASIC は、カスタマイズされた回路です。 これらのチップは最高の効率を備えていますが、ASIC は柔軟性に欠けています。
- Azure で提供されているもののような **FPGA** は、ASIC に近いパフォーマンスを発揮しますが、後で再構成するための柔軟性が備わっています。

FPGA は、現在ではすべての新しい Azure サーバーに搭載されています。 Microsoft は既に、FPGA を Bing 検索の順位付け、ディープ ニューラル ネットワーク (DNN) の評価、ソフトウェア定義ネットワーク (SDN) のアクセラレーションに使用しています。 これらの FPGA の実装によって、CPU を他のタスクのために解放するのと同時に、待機時間が削減されています。

## <a name="project-brainwave-on-azure"></a>Azure 上での Project Brainwave

Project Brainwave は、Intel の FPGA デバイスに基づいて設計されたハードウェア アーキテクチャで、AI のリアルタイムの計算を高速化するために使用されています。 この経済的な FPGA 対応アーキテクチャの導入により、トレーニングしたニューラル ネットワークを可能な限り高速に、短い待機時間で実行できます。 Project Brainwave は、事前トレーニング済みの DNN を FPGA 間で並列化し、サービスをスケールアウトすることができます。

- [パフォーマンス]

    FPGA によって、リアルタイムの推論要求に関して短い待機時間を達成できるようになります。 バッチ処理とは、ハードウェアの使用率を向上させるため、要求をより小さな単位に分割に分割し、それらをプロセッサに供給することを意味します。 バッチ処理は効率的ではなく、待機時間が発生する場合があります。 Brainwave ではバッチ処理が必要とされないため、待機時間は CPU や GPU と比較して 10 倍短くなります。

- 柔軟性

    FPGA は、さまざまな種類の機械学習モデルに向けて再構成することができます。 この柔軟性により、最適な数値精度と使用されているメモリ モデルに基づいて、容易にアプリケーションを高速化できるようになっています。

    新しい機会学習技法が絶えることなく開発されており、Project Brainwave のハードウェア設計も急速に進化しています。 FPGA は再構成可能なため、急速に変化する AI アルゴリズムの要件に遅れることなく対応できます。

- スケール

    Microsoft Azure は、世界最大規模の、FPGA 分野でのクラウド関連投資です。 Brainwave は、Azure のスケール インフラストラクチャ上で実行できます。

現在、Azure Machine Learning と統合された Project Brainwave のプレビューをご利用いただけます。 そして、お客様自身の事業や施設でそのコンピューティング速度を活用できるように、Project Brainwave をエッジで利用できるようにする制限付きのプレビューも提供されています。

現在のプレビューでは、Brainwave は、TensorFlow のデプロイと、イメージの分類と認識を目的とした、Intel FPGA ハードウェア上で稼働する ResNet50 ベースのニューラル ネットワークのみを対象としています。 より多くの選り抜いたモデルとその他のフレームワークをサポートする計画があります。

以下のシナリオでは、Project Brainwave アーキテクチャ上で FPGA を使用します。

- 自動化された光学的検査システム。 [Microsoft がリアルタイム AI について、Project Brainwave のプレビューを発表した](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)ことの記事を参照してください。
- 土地被覆マッピング。 [テラバイト単位の航空画像に対して土地被覆マッピングを実行することを目的として、ディープ ラーニングの推論のために FPGA を使用する方法](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)についての記事を参照してください。

## <a name="how-to-deploy-a-web-service-to-an-fpga"></a>Web サービスを FPGA にデプロイする方法

Azure 内で、ResNet50 を特徴抽出器として使用して画像認識サービスを作成する場合の大まかな流れは、次のようになります。

1. ResNet50 は既に Brainwave 内にデプロイされています。 TensorFlow を使用してその他のグラフ (日付の入力、分類など) を作成し、サービス定義の json ファイルを使用してパイプライン (入力 -> 特徴抽出 -> 分類) を定義できます。 定義とグラフを zip ファイルに圧縮し、その zip ファイルを Azure Blob Storage にアップロードします。
2. zip ファイルが BLOB ストレージにある状態で、Azure ML モデル管理 API を使用してモデルを登録します。
3. Azure ML モデル管理 API を使用して、登録済みのモデルを含むサービスをデプロイします。

この記事で紹介したこのプロセスの詳細については、[Azure Machine Learning を使用するモデルを Web サービスとして FPGA 上にデプロイする](how-to-deploy-fpga-web-service.md)ことに関する記事を参照してください。


## <a name="start-using-fpga"></a>FPGA の使用開始

Azure Machine Learning のハードウェア 高速化モデルでは、トレーニング済みの DNN モデルを Azure クラウド内の FPGA にデプロイできます。 作業を開始するには、次のトピックをご覧ください。

- [モデルを Web サービスとして FPGA にデプロイする](how-to-deploy-fpga-web-service.md)
- [Project Brainwave が基盤となった Microsoft Azure Machine Learning のハードウェア高速化モデル](https://github.com/azure/aml-real-time-ai)。

## <a name="next-steps"></a>次の手順

[モデルを Web サービスとして FPGA にデプロイする](how-to-deploy-fpga-web-service.md)

[FPGA SDK のインストール方法を確認する](reference-fpga-package-overview.md)

[ハイパースケールのハードウェア: Azure および FPGA 上の大規模な ML: ビルド 2018 (ビデオ)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

[Microsoft の FPGA ベースの構成可能なクラウドについて (ビデオ)](https://channel9.msdn.com/Events/Build/2017/B8063)

[Microsoft、リアルタイム AI のための Project Brainwave を初公開](https://www.microsoft.com/research/blog/microsoft-unveils-project-brainwave/)
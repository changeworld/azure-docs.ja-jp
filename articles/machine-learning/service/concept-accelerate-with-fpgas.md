---
title: FPGA および Project Brainwave とは - Azure Machine Learning サービス
description: Azure 上の FPGA を使用してモデルとディープ ニューラル ネットワークを高速化する方法を説明します。 この記事では、Field-programmable Gate Arrays (FPGA) の概要と、Azure の FPGA にモデルをデプロイしたときに Azure Machine Learning サービスによってリアルタイムの人工知能 (AI) が提供される方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: tedway
author: tedway
ms.reviewer: jmartens
ms.date: 9/24/2018
ms.openlocfilehash: 411beacd65915c30338ab415b095acc1a0c8cbe6
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238857"
---
# <a name="what-is-fpga-and-project-brainwave"></a>FPGA および Project Brainwave とは

この記事では、Field-programmable Gate Arrays (FPGA) の概要と、Azure の FPGA にモデルをデプロイしたときに Azure Machine Learning サービスによってリアルタイムの人工知能 (AI) が提供される方法について説明します。

FPGA には、プログラミング可能なロジック ブロックの配列と、再構成可能な相互接続の階層が含まれています。 相互接続を使用して、製造後にさまざまな方法でこれらのブロックを構成できます。 FPGA は、他のチップと比較して、プログラミング可能であることとパフォーマンスを兼ね備えています。

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA とCPU、GPU、ASIC の違い

![Azure Machine Learning サービスにおける FPGA の比較](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|プロセッサ||説明|
|---|:-------:|------|
|特定用途向け集積回路|ASIC|Google の TensorFlow Processor Unit (TPU) などのカスタム回路は、最高の効率性を提供します。 ニーズに変化に応じて再構成することはできません。|
|フィールド プログラマブル ゲート アレイ|FPGA|Azure で使用できるものなどの FPGA は、ASIC に近いパフォーマンスを提供しますが、柔軟性があり、徐々に再構成して新しいロジックを実装できます。|
|グラフィックス処理装置|GPU|CPU より画像のレンダリングが速くなる、並列処理機能を提供する AI 計算用に人気のある選択肢です。|
|中央処理装置|CPU|パフォーマンスがグラフィックスや動画の処理には最適でない汎用プロセッサです。|

## <a name="project-brainwave-on-azure"></a>Azure 上での Project Brainwave

[Project Brainwave](https://www.microsoft.com/research/project/project-brainwave/) とは Microsoft の経済性に優れたハードウェア アーキテクチャであり、Intel の FPGA デバイスに基づき、データ サイエンティストや開発者がリアルタイムの AI 計算を高速化するために使用するものです。  この FPGA 対応のアーキテクチャでは、**パフォーマンス**、**柔軟性**、**スケール**が提供され、Azure で使用できます。

**FPGA によって、リアルタイムの推論要求に関して短い待機時間を達成できるようになります。** バッチ処理とは、ハードウェアの使用率を向上させるために、大量のデータを収集してプロセッサに供給することです。 バッチ処理では、処理の必要なデータが多くなるため待機時間は長くなる場合がありますが、スループットは向上する可能性があります。 ニューラル処理ユニットの Project Brainwave の実装にバッチ処理は必要ないので、待機時間は CPU や GPU と比較して何倍も短くなる場合があります。

### <a name="reconfigurable-power"></a>再構成可能な能力
**FPGA は、さまざまな種類の機械学習モデルに向けて再構成することができます。** この柔軟性により、最適な数値精度と使用されているメモリ モデルに基づいて、容易にアプリケーションを高速化できるようになっています。

新しい機会学習技法が絶えることなく開発されており、Project Brainwave のハードウェア設計も急速に進化しています。 FPGA は再構成可能なため、急速に変化する AI アルゴリズムの要件に遅れることなく対応できます。

### <a name="whats-supported-on-azure"></a>Azure でサポートされるもの
**Microsoft Azure は、世界最大規模の、FPGA 分野でのクラウド関連投資です。** Project Brainwave は、Azure のスケール インフラストラクチャ上で実行できます。

現在、Project Brainwave では次のものがサポートされています。
+ 画像の分類と認識のシナリオ
+ TensorFlow のデプロイ
+ DNN: ResNet 50、ResNet 152、VGG-16、SSD-VGG、DenseNet-121
+ Intel FPGA ハードウェア 

この FPGA 対応のハードウェア アーキテクチャを使用すると、トレーニング済みのニューラル ネットワークは迅速かつ低遅延で実行します。 Project Brainwave は、事前トレーニング済みのディープ ニューラル ネットワーク (DNN) を FPGA 間で並列化し、サービスをスケールアウトすることができます。 DNN は、転移学習用のディープ特徴抽出器として事前トレーニングすることも、または更新された重みで微調整することもできます。

### <a name="scenarios-and-applications"></a>シナリオとアプリケーション

Project Brainwave は Azure Machine Learning と統合されています。 Microsoft は、DNN 評価、Bing 検索ランキング、およびソフトウェア定義ネットワーク (SDN) アクセラレーション用に FPGA を使用して、待機時間を短縮しながら、他のタスク用に CPU を解放します。

以下のシナリオでは、Project Brainwave アーキテクチャ上で FPGA を使用します。
+ [自動化された光学的検査システム](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [土地被覆マッピング](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-to-fpgas-on-azure"></a>Azure で FPGA にデプロイする

サポートされている DNN を Azure FPGA へのデプロイ用の特徴抽出器として使用して Azure で画像認識サービスを作成するワークフローを次に示します。

1. Azure Machine Learning SDK for Python を使用してサービス定義を作成します。これは、TensorFlow に基づいてグラフ (入力、特徴抽出器、分類子) のパイプラインを記述しているファイルです。 デプロイ コマンドは、定義とグラフを ZIP ファイルに自動的に圧縮し、その ZIP を Azure Blob Storage にアップロードします。  Project Brainwave 上には、FPGA 上で実行するように DNN が既にデプロイされています。

1. SDK と Azure Blob Storage 内の ZIP ファイルを使用して、モデルを登録します。

1. SDK を使用し、登録されているモデルでサービスをデプロイします。

Azure クラウド内の FPGA にトレーニング済みの DNN モデルをデプロイする方法については、「**[Azure Machine Learning でモデルを Web サービスとして FPGA 上に配置する](how-to-deploy-fpga-web-service.md)**」をご覧ください。


## <a name="next-steps"></a>次の手順

次のビデオとブログをご覧ください。

+ [ハイパースケールのハードウェア: Azure および FPGA 上の大規模な ML: ビルド 2018 (ビデオ)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

+ [Microsoft の FPGA ベースの構成可能なクラウドについて (ビデオ)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [リアルタイム AI のための Project Brainwave: プロジェクト ホーム ページ](https://www.microsoft.com/research/project/project-brainwave/)

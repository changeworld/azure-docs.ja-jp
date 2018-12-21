---
title: Azure Batch AI サービス - AI トレーニング | Microsoft Docs
description: 管理された Azure Batch AI サービスを使用して、GPU と CPU のクラスター上で人工知能 (AI) やその他の機械学習モデルをトレーニングする方法について説明します。
services: batch-ai
documentationcenter: ''
author: alexsuttonms
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/01/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 37f1bf5dc20d097f7f5f560e3bf1fdd25dc38045
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408053"
---
# <a name="what-is-azure-batch-ai"></a>Azure Batch AI とは

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Azure Batch AI は、データ サイエンティストと AI 研究者が複雑なインフラストラクチャを管理することなく、Azure で機械学習と AI のモデルを大規模にトレーニングしてテストするのに役立つマネージド サービスです。 コンピューティング リソース、実行するジョブ、モデルの入力と出力の保存先を記述すれば、残りは Batch AI が処理します。

Batch AI は、スタンドアロンで使用することも、より大規模な開発ワークフローの一環としてモデル トレーニングを実行するために使用することもできます。

* Batch AI を単独で使用して、[GPU](../virtual-machines/linux/sizes-gpu.md) または CPU のクラスター上で、機械学習および AI モデルのトレーニング、テスト、バッチ スコアリングを実行します。 

* [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) や他の [Azure AI プラットフォーム ツール](https://azure.microsoft.com/overview/ai-platform/)を使用して、ワークフローで Batch AI クラスターを対象にします。 Azure ML は、データ準備、実験、ジョブ履歴のための豊富なエクスペリエンスを提供します。 また、Azure ML では、トレーニング済みモデルをコンテナーにパッケージ化し、推論のモデルをデプロイしたり、IoT デバイスにデプロイしたりできます。  

## <a name="train-machine-learning-and-ai-models"></a>機械学習および AI モデルのトレーニング

Batch AI を使用して、機械学習モデルおよびディープ ニューラル ネットワーク (ディープ ラーニング) や他の AI アプローチをトレーニングします。 Batch AI には、一般的なオープンソースの AI ライブラリとフレームワーク ([TensorFlow](https://github.com/tensorflow/tensorflow)、[PyTorch](https://github.com/pytorch/pytorch)、[Chainer](https://github.com/chainer/chainer)、[Microsoft Cognitive Toolkit (CNTK)](https://github.com/Microsoft/CNTK) など) のサポートが組み込まれています。

問題領域を特定し、データを準備したら、Batch AI を対話的に操作してモデルのアイデアをテストします。 その後、大規模に実験を行う準備が整ったら、MPI などの通信ライブラリを使用して複数の GPU でジョブを開始し、多数の実験を並列で実行します。

Batch AI を使用すると、さまざまな方法でモデルを大規模にトレーニングできます。 例:  

|  |  |
|---------|---------|
| **トレーニングの分散**<br/>![分散トレーニング](./media/overview/distributed-training.png)  | ネットワークに接続された多数の GPU 間で 1 つのジョブのトレーニングをスケールアップして、大量のデータを含むより大規模なネットワークをトレーニングします。|
| **実験**<br/>![実験](./media/overview/experimentation.png) | 複数のジョブを使用してトレーニングをスケールアウトします。 パラメーター スイープを実行して新しいアイデアを試したり、ハイパーパラメーターを調整して精度とパフォーマンスを最適化したりします。 |
| **並列実行**![並列実行](./media/overview/parallel-execution.png) | ジョブを短時間で完了するために、サーバー群でジョブを並列実行して、一度に多数のモデルのトレーニングやスコア付けを行います。|

モデルをトレーニングしたら、Batch AI を使用してモデルをテストするか (トレーニング スクリプトに含まれていない場合)、バッチ スコアリングを実行します。

## <a name="how-it-works"></a>動作のしくみ

Batch AI SDK、コマンド ライン スクリプト、または Azure portal を使用して、コンピューティング リソースを管理し、AI のトレーニングとテストのジョブをスケジュールします。 

* **VM のクラスターのプロビジョニングとスケーリング** - ノード (VM) 数を選択し、トレーニングのニーズに応じて、GPU 対応の VM サイズまたは他の VM サイズを選択します。 リソースを必要なときだけ使用するために、ノード数を自動または手動でスケールアップまたはスケールダウンします。 

* **依存関係とコンテナーの管理** - 既定では、Batch AI クラスターは、GPU または CPU でコンテナー ベースのトレーニング フレームワークを実行するための依存関係が事前インストールされた Linux VM イメージを実行します。 追加の構成では、カスタム イメージを提供するか、スタートアップ スクリプトを実行します。

* **データの配布** - 入力データと、スクリプトとジョブの出力を管理するための 1 つまたは複数のストレージ オプションを選択します。Azure Files、Azure Blob ストレージ、またはマネージド NFS サーバー。 Batch AI では、高性能並列ファイル システムなどのカスタム ストレージ ソリューションもサポートされています。 単純な構成ファイルを使用して、クラスター ノードとジョブ コンテナーにストレージ ファイル システムをマウントします。

* **ジョブのスケジュール** - 優先度ベースのジョブ キューにジョブを送信してクラスター リソースを共有し、低優先度 VM と予約済みインスタンスを利用します。

* **障害への対応** - ジョブの状態を監視し、長時間実行される可能性のあるジョブで VM の障害が発生した場合にジョブを再開します。

* **結果の収集** - 出力ログ、Stdout、Stderr、トレーニング済みモデルに簡単にアクセスできます。 マウントされたストレージに出力を直接プッシュするように Batch AI ジョブを構成します。

Azure サービスの 1 つである Batch AI では、セキュリティを確保するために、ロールベースのアクセス制御 (RBAC) や Azure 仮想ネットワークなどの一般的なツールがサポートされています。  

## <a name="next-steps"></a>次の手順

* 機械学習または AI モデルのトレーニングに使用する [Batch AI リソース](resource-concepts.md)の詳細を確認する。

* Batch AI を使用して、[サンプル ディープ ラーニング モデルのトレーニング](quickstart-tensorflow-training-cli.md)を開始する。

* 一般的な AI フレームワークのサンプル [トレーニング レシピ](https://github.com/Azure/BatchAI/blob/master/recipes)を確認する。

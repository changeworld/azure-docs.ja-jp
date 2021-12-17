---
title: GPU アクセラレータ プール
description: Synapse Analytics 内の GPU の概要。
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/10/2021
ms.author: midesa
ms.openlocfilehash: d4a4c38b61dcce01ec7fb73dc70605ce795b6412
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495259"
---
# <a name="gpu-accelerated-apache-spark-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics の GPU アクセラレータ Apache Spark プール
Azure Synapse Analytics では、グラフィックス処理ユニット (GPU) を使用した Apache Spark プールの高速化がサポートされるようになりました。 

NVIDIA GPU を使用すると、データ科学者やエンジニアは、データ統合パイプラインの実行、機械学習モデルのスコア付けなどに必要な時間を短縮できます。 この記事では、Azure Synapse Analytics で GPU アクセラレータ プールを作成して使用する方法について説明します。 この記事では、GPU 高速化ランタイムの一部としてプレインストールされている GPU ドライバーおよびライブラリについても詳しく説明します。

> [!NOTE]
> Azure Synapse GPU 対応プールは、現在、パブリック プレビュー段階にあります。

## <a name="create-a-gpu-accelerated-pool"></a>GPU アクセラレータ プールを作成する
プールの作成と管理のプロセスを簡略化するために、Azure Synapse は、低レベルのライブラリをプレインストールし、コンピューティング ノード間の複雑なネットワーク要件をすべて設定します。 この統合により、ユーザーはほんの数分で GPU アクセラレータ プールを使い始めることができます。 GPU アクセラレータ プールを作成する方法の詳細については、[GPU アクセラレータ プールを作成する](../quickstart-create-apache-gpu-pool-portal.md)方法のクイックスタートを参照してください。

> [!NOTE]
>  - GPU アクセラレータ プールは、米国東部、オーストラリア東部、北ヨーロッパにあるワークスペースで作成できます。
>  - GPU アクセラレータ プールは、Apache Spark 3 ランタイムでのみ使用できます。
>  - GPU 対応クラスターを作成するには、[上限の引き上げ](./apache-spark-rapids-gpu.md#quotas-and-resource-constraints-in-azure-synapse-gpu-enabled-pools)を要求することが必要になる場合があります。 
 
## <a name="gpu-accelerated-runtime"></a>GPU 高速化ランタイム

### <a name="nvidia-gpu-driver-cuda-and-cudnn"></a>NVIDIA GPU ドライバー、CUDA、cuDNN
Azure Synapse Analytics では、さまざまな NVIDIA ライブラリと構成を含む GPU 高速化 Apache Spark プールが提供されるようになりました。 既定では、Azure Synapse Analytics は、Spark ドライバーとワーカー インスタンスで GPU を使用するために必要な次の NVIDIA ドライバーとライブラリをインストールします。
 - CUDA 11.2
 - libnccl2=2.8.4
 - libnccl-dev=2.8.4
 - libcudnn8=8.1.1 
 - libcudnn8-dev=8.1.1

> [!NOTE]
> このソフトウェアには、NVIDIA Corporation によって提供されるソース コードが含まれています。 具体的には、GPU アクセラレータ プールをサポートするために、Azure Synapse Apache Spark プールには、[CUDA サンプル](https://docs.nvidia.com/cuda/eula/#nvidia-cuda-samples-preface)のコードが含まれています。

### <a name="nvidia-end-user-license-agreement-eula"></a>NVIDIA エンド ユーザー ライセンス契約 (EULA)
Synapse Spark で GPU 高速化ハードウェア オプションを選択すると、以下に関する NVIDIA EULA に記載されているご契約条件に暗黙的に同意したことになります。
  - CUDA 11.2: [EULA :: CUDA Toolkit ドキュメント (nvidia.com)](https://docs.nvidia.com/cuda/eula/index.html)
  - libnccl2=2.8.4: [nccl/LICENSE.txt at master · NVIDIA/nccl (github.com)](https://github.com/NVIDIA/nccl/blob/master/LICENSE.txt)
  - libnccl-dev=2.8.4: [nccl/LICENSE.txt at master · NVIDIA/nccl (github.com)](https://github.com/NVIDIA/nccl/blob/master/LICENSE.txt)
  - libcudnn8=8.1.1: [ソフトウェア ライセンス契約 :: NVIDIA ディープ ラーニング cuDNN ドキュメント](https://docs.nvidia.com/deeplearning/cudnn/sla/index.html)
  - libcudnn8-dev=8.1.1: [ソフトウェア ライセンス契約 :: NVIDIA ディープ ラーニング cuDNN ドキュメント](https://docs.nvidia.com/deeplearning/cudnn/sla/index.html)
  - CUDA、NCCL、および cuDNN ライブラリ、および NCCL ライブラリに [NVIDIA エンド ユーザー ライセンス契約 (NCCL 補足を含む)](https://docs.nvidia.com/deeplearning/nccl/sla/index.html#overview)

## <a name="accelerate-etl-workloads"></a>ETL ワークロードの高速化
[Apache Spark 用の NVIDIA の RAPIDS Accelerator](https://nvidia.github.io/spark-rapids/) の組み込みサポートにより、Azure Synapse の GPU 高速化 Spark プールは、コードを変更することなく、標準の分析ベンチマークと比較して大幅にパフォーマンスを向上させることができます。 NVIDIA CUDA と UCX の上に構築された NVIDIA RAPIDS では、GPU 高速化 SQL、データフレーム操作、Spark シャッフルが有効になっています。 これらのアクセラレーションを活用するために必要なコードは変更されていないため、ユーザーは、Linux Foundation の Delta Lake または Microsoft の Hyperspace インデックス作成に依存するデータ パイプラインを高速化することもできます。 

Azure Synapse Analytics の GPU 高速化プールと NVIDIA RAPIDS Accelerator を併用する方法の詳細については、[RAPIDS を使用してパフォーマンスを向上させる](apache-spark-rapids-gpu.md)方法に関するガイドを参照してください。

## <a name="improve-machine-learning-scoring-workloads"></a>機械学習のスコアリング ワークロードの向上
多くの組織は、限られた期間内に頻繁に実行する大規模なバッチ スコアリング ジョブに依存しています。 改善されたバッチ スコアリング ジョブを実現するために、Microsoft の [Hummingbird ライブラリ](https://github.com/Microsoft/hummingbird)と GPU 高速化 Spark プールを使用することもできます。 Hummingbird を使用すると、ユーザーは従来のツリーベースの ML モデルを使用して、それらをテンソル計算にコンパイルできます。 Hummingbird を使用すると、ユーザーはネイティブのハードウェア アクセラレーションとニューラル ネットワーク フレームワークをシームレスに利用して、モデルを書き直すことなく ML モデルのスコアリングを加速させることができます。  

## <a name="next-steps"></a>次のステップ
- [Azure Synapse Analytics](../overview-what-is.md)

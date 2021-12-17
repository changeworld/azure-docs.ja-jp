---
title: コンピューティング ターゲットとは
titleSuffix: Azure Machine Learning
description: Azure Machine Learning でモデルをトレーニングまたはデプロイするためのコンピューティング リソースまたは環境を指定する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 96f0f011382fb631416665cad58ca7d7324fcb24
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076618"
---
# <a name="what-are-compute-targets-in-azure-machine-learning"></a>Azure Machine Learning でのコンピューティング ターゲットとは

"*コンピューティング ターゲット*" とは、トレーニング スクリプトを実行したり、サービスのデプロイをホストしたりする、指定されたコンピューティング リソースまたは環境のことです。 この場所は、ローカル コンピューターでも、クラウドベースのコンピューティング リソースでもかまいません。 コンピューティング ターゲットを使用すると、コードを変更しなくても、後でコンピューティング環境を容易に変更できます。

一般的なモデル開発ライフサイクルでは、次のことを行う可能性があります。

1. 最初に、少量のデータを開発して実験します。 この段階では、コンピューティング ターゲットとしてローカル環境 (ローカル コンピューターやクラウド ベースの仮想マシン (VM) など) を使用します。
1. より大きなデータにスケールアップするか、またはこれらの[トレーニング コンピューティング ターゲット](#train)のいずれかを使用して[分散トレーニング](how-to-train-distributed-gpu.md)を実行します。
1. モデルの準備ができたら、これらの[デプロイ コンピューティング ターゲット](#deploy)のいずれかを使用して、そのモデルを Web ホスティング環境にデプロイします。

コンピューティング ターゲットに使用するコンピューティング リソースは[ワークスペース](concept-workspace.md)にアタッチされています。 ローカル コンピューター以外のコンピューティング リソースは、ワークスペースのユーザーによって共有されます。

## <a name="training-compute-targets"></a><a name="train"></a> トレーニング コンピューティング ターゲット

Azure Machine Learning では、異なるコンピューティング先に対してさまざまなサポートが提供されています。 典型的なモデル開発ライフサイクルは、少量のデータを用いた開発または実験から始まります。 このステージでは、ローカル コンピューターなどのローカル環境またはクラウド ベースの VM を使用します。 より大規模なデータ セットにトレーニングをスケールアップする、または[分散トレーニング](how-to-train-distributed-gpu.md)を実行する段階で、Azure Machine Learning コンピューティングを使用して、実行を送信するたびに自動スケーリングするシングルノードまたはマルチノード クラスターを作成します。 また、独自のコンピューティング リソースを接続することもできますが、シナリオごとにサポートは異なることがあります。

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

[コンピューティング先にトレーニングの実行を送信する](how-to-set-up-training-targets.md)方法の詳細を参照してください。

## <a name="compute-targets-for-inference"></a><a name="deploy"></a>推論のコンピューティング先

推論を実行すると、Azure Machine Learning によって、モデルおよびそれを使用するために必要な関連リソースをホストする Docker コンテナーが作成されます。 それから、このコンテナーをコンピューティング先で使用します。

[!INCLUDE [aml-deploy-target](../../includes/aml-compute-target-deploy.md)]

[モデルをコンピューティング ターゲットにデプロイする場所と方法](how-to-deploy-and-where.md)に関するページを参照してください。

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning コンピューティング (マネージド)

マネージド コンピューティング リソースは、Azure Machine Learning によって作成され、管理されます。 このコンピューティングは、機械学習ワークロード向けに最適化されています。 Azure Machine Learning コンピューティング クラスターと[コンピューティング インスタンス](concept-compute-instance.md)だけがマネージド コンピューティングです。

Azure Machine Learning コンピューティング インスタンスまたはコンピューティング クラスターは次の場所から作成できます。

* [Azure Machine Learning スタジオ](how-to-create-attach-compute-studio.md)
* Python SDK および Azure CLI:
    * [コンピューティング インスタンス](how-to-create-manage-compute-instance.md)
    * [コンピューティング クラスター](how-to-create-attach-compute-cluster.md)
* Azure Resource Manager テンプレート。 テンプレートの例については、[Azure Machine Learning コンピューティング クラスターの作成](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-amlcompute)に関する記事を参照してください。
* 機械学習の [Azure CLI 用拡張機能](reference-azure-machine-learning-cli.md#resource-management)

作成されると、これらのコンピューティング リソースは、他の種類のコンピューティング ターゲットとは異なり、自動的にワークスペースの一部になります。


|機能  |コンピューティング クラスター  |コンピューティング インスタンス  |
|---------|---------|---------|
|シングルノードまたはマルチノード クラスター     |    **&check;**       |    シングル ノード クラスター     |
|実行を送信するたびに自動スケーリング     |     **&check;**      |         |
|自動でのクラスター管理とジョブ スケジューリング     |   **&check;**        |     **&check;**      |
|CPU と GPU の両方のリソースをサポートします     |  **&check;**         |    **&check;**       |


> [!NOTE]
> コンピューティング "*クラスター*" がアイドル状態の場合、ノードは 0 に自動スケーリングされるため、使用されていないときは料金がかかりません。 コンピューティング" *インスタンス*" は常にオンであり、自動スケーリングされません。 追加コストを回避するには、使用していないときに[コンピューティング インスタンスを停止](how-to-create-manage-compute-instance.md#manage)します。

### <a name="supported-vm-series-and-sizes"></a>サポートされている VM シリーズおよびサイズ

Azure Machine Learning でマネージド コンピューティング リソースのノード サイズを選択すると、Azure で使用可能な VM サイズの中から選択できます。 Azure では、さまざまなワークロードに対応する Linux および Windows の幅広いサイズが提供されています。 詳細については、[VM の種類とサイズ](../virtual-machines/sizes.md)に関するページを参照してください。

VM サイズを選択する際には次のような例外と制限事項があります。

* 一部の VM シリーズは、Azure Machine Learning でサポートされていません。
* 一部の VM シリーズは制限されています。 制限付きシリーズを使用するには、サポートに連絡し、そのシリーズのクォータの引き上げを依頼してください。 サポートへのお問い合わせ方法については、[Azure のサポート オプション](https://azure.microsoft.com/support/options/)に関するページを参照してください。

サポートされているシリーズと制限の詳細については、次の表を参照してください。

| **サポートされている VM シリーズ**  | **制限事項** | **カテゴリ** | **サポートしているもの** |
|------------|------------|------------|------------|
| [DDSv4](../virtual-machines/ddv4-ddsv4-series.md#ddsv4-series) | [なし] : | 汎用 | コンピューティングのクラスターとインスタンス |
| [Dv2](../virtual-machines/dv2-dsv2-series.md#dv2-series) | [なし] : | 汎用 | コンピューティングのクラスターとインスタンス |
| [Dv3](../virtual-machines/dv3-dsv3-series.md#dv3-series) | [なし] :| 汎用 | コンピューティングのクラスターとインスタンス |
| [DSv2](../virtual-machines/dv2-dsv2-series.md#dsv2-series) | [なし] : | 汎用 | コンピューティングのクラスターとインスタンス |
| [DSv3](../virtual-machines/dv3-dsv3-series.md#dsv3-series) | [なし] :| 汎用 | コンピューティングのクラスターとインスタンス |
| [EAv4](../virtual-machines/eav4-easv4-series.md) | [なし] : | メモリ最適化 | コンピューティングのクラスターとインスタンス |
| [Ev3](../virtual-machines/ev3-esv3-series.md) | [なし] : | メモリ最適化 | コンピューティングのクラスターとインスタンス |
| [FSv2](../virtual-machines/fsv2-series.md) | [なし] : | コンピューティング最適化 | コンピューティングのクラスターとインスタンス |
| [FX](../virtual-machines/fx-series.md) | 承認が必要。 | コンピューティング最適化 | コンピューティング クラスター |
| [H](../virtual-machines/h-series.md) | [なし] : | ハイ パフォーマンス コンピューティング | コンピューティングのクラスターとインスタンス |
| [HB](../virtual-machines/hb-series.md) | 承認が必要。 | ハイ パフォーマンス コンピューティング | コンピューティングのクラスターとインスタンス |
| [HBv2](../virtual-machines/hbv2-series.md) | 承認が必要。 |  ハイ パフォーマンス コンピューティング | コンピューティングのクラスターとインスタンス |
| [HBv3](../virtual-machines/hbv3-series.md) | 承認が必要。 |  ハイ パフォーマンス コンピューティング | コンピューティングのクラスターとインスタンス |
| [HC](../virtual-machines/hc-series.md) | 承認が必要。 |  ハイ パフォーマンス コンピューティング | コンピューティングのクラスターとインスタンス |
| [LSv2](../virtual-machines/lsv2-series.md) | [なし] : |  ストレージ最適化 | コンピューティングのクラスターとインスタンス |
| [M](../virtual-machines/m-series.md) | 承認が必要。 | メモリ最適化 | コンピューティングのクラスターとインスタンス |
| [NC](../virtual-machines/nc-series.md) | [なし] : |  GPU | コンピューティングのクラスターとインスタンス |
| [NC Promo](../virtual-machines/nc-series.md) | [なし] : | GPU | コンピューティングのクラスターとインスタンス |
| [NCv2](../virtual-machines/ncv2-series.md) | 承認が必要。 | GPU | コンピューティングのクラスターとインスタンス |
| [NCv3](../virtual-machines/ncv3-series.md) | 承認が必要。 | GPU | コンピューティングのクラスターとインスタンス |
| [ND](../virtual-machines/nd-series.md) | 承認が必要。 | GPU | コンピューティングのクラスターとインスタンス |
| [NDv2](../virtual-machines/ndv2-series.md) | 承認が必要。 | GPU | コンピューティングのクラスターとインスタンス |
| [NV](../virtual-machines/nv-series.md) | [なし] : | GPU | コンピューティングのクラスターとインスタンス |
| [NVv3](../virtual-machines/nvv3-series.md) | 承認が必要。 | GPU | コンピューティングのクラスターとインスタンス |
| [NCasT4_v3](../virtual-machines/nct4-v3-series.md) | 承認が必要。 | GPU | コンピューティングのクラスターとインスタンス |
| [NDasrA100_v4](../virtual-machines/nda100-v4-series.md) | 承認が必要。 | GPU | コンピューティングのクラスターとインスタンス |


これらの VM シリーズは Azure Machine Learning でサポートされていますが、すべての Azure リージョンで使用できるとは限りません。 VM シリーズが使用可能かどうかを確認するには、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)」を参照してください。

> [!NOTE]
> Azure Machine Learning は、Azure Compute がサポートするすべての VM サイズをサポートしているわけではありません。 使用可能な VM サイズを一覧表示するには、次のいずれかのメソッドを使用します。
> * [REST API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable/2020-08-01/examples/ListVMSizesResult.json)
> * [Python SDK](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
>

GPU 対応のコンピューティング ターゲットを使用する場合は、トレーニング環境に適切な CUDA ドライバーがインストールされていることを確認することが重要です。 次の表を使用して、使用する適切な CUDA バージョンを決定します。

| **GPU アーキテクチャ**  | **Azure VM シリーズ** | **サポートされている CUDA のバージョン** |
|------------|------------|------------|
| Ampere | NDA100_v4 | 11.0+ |
| Turing | NCT4_v3 | 10.0 以降 |
| Volta | NCv3、NDv2 | 9.0 以上 |
| Pascal | NCv2、ND | 9.0 以上 |
| Maxwell | NV、NVv3 | 9.0 以上 |
| Kepler | NC、NC Promo| 9.0 以上 |

CUDA のバージョンとハードウェアに互換性があることを確認するだけでなく、CUDA のバージョンが、使用している機械学習フレームワークのバージョンと互換性があることを確認します。 

- PyTorch の場合は、[こちら](https://pytorch.org/get-started/previous-versions/)で互換性を確認できます。 
- Tensorflow の場合は、[こちら](https://www.tensorflow.org/install/source#gpu)で互換性を確認できます。

### <a name="compute-isolation"></a>コンピューティングの分離

Azure Machine Learning コンピューティングにより、特定のハードウェアの種類に分離される、単一顧客専用の VM サイズが提供されます。 分離された VM サイズは、コンプライアンスや規制上の要件を満たすなどの理由で、他の顧客のワークロードからの高いレベルの分離を必要とするワークロードに最適です。 分離されたサイズを利用することで、お使いの VM がその特定のサーバー インスタンス上で実行されている唯一のマシンであることが保証されます。

現在の分離された VM のプランには、以下が含まれます。

* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3*

*RDMA 対応

分離の詳細については、「[Azure パブリック クラウドでの分離](../security/fundamentals/isolation-choices.md)」を参照してください。

## <a name="unmanaged-compute"></a>アンマネージド コンピューティング

アンマネージド コンピューティング先は、Azure Machine Learning によって管理 *されません*。 この種類のコンピューティング ターゲットは、Azure Machine Learning の外部で作成してからワークスペースに接続します。 アンマネージド コンピューティング リソースでは、機械学習ワークロードのパフォーマンスを維持するため、または向上させるために追加の手順が必要になる場合があります。 

Azure Machine Learning は、次の種類のアンマネージド コンピューティングをサポートしています。

* ユーザーのローカル コンピューター
* リモート仮想マシン
* Azure HDInsight
* Azure Batch
* Azure Databricks
* Azure Data Lake Analytics
* Azure Container Instances
* Azure Kubernetes Service および Azure Arc 対応 Kubernetes (プレビュー)

詳細については、「[モデルのトレーニングとデプロイのためのコンピューティング ターゲットを設定する](how-to-attach-compute-targets.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

具体的には、次の方法を学習します。
* [モデルをトレーニングするためのコンピューティング ターゲットを使用する](how-to-set-up-training-targets.md)
* [モデルをコンピューティング ターゲットにデプロイする](how-to-deploy-and-where.md)

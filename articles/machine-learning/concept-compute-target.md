---
title: コンピューティング先とは
titleSuffix: Azure Machine Learning
description: Azure Machine Learning を使用してモデルをトレーニングまたはデプロイする場所を定義します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 03/30/2020
ms.openlocfilehash: ed65d69c18f2dbcd53324fe3cc18af8c51c546b2
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780115"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Azure Machine Learning でのコンピューティング先とは 

**コンピューティング ターゲット**とは、トレーニング スクリプトを実行したり、サービスのデプロイをホストしたりする、指定されたコンピューティング リソース/環境のことです。 この場所は、ローカル コンピューターでも、クラウドベースのコンピューティング リソースでもかまいません。 コンピューティング ターゲットを使用すると、コードを変更しなくても、後でコンピューティング環境を容易に変更できます。  

一般的なモデル開発ライフサイクルでは、次のことを行う可能性があります。
1. 最初に、少量のデータを開発して実験します。 この段階では、コンピューティング ターゲットとしてローカル環境 (ローカル コンピューターまたはクラウド ベースの VM) をお勧めします。 
2. より大きなデータにスケールアップするか、またはこれらの[トレーニング コンピューティング ターゲット](#train)のいずれかを使用して分散トレーニングを実行します。  
3. モデルの準備ができたら、これらの[デプロイ コンピューティング ターゲット](#deploy)のいずれかを使用して、それを Web ホスティング環境または IoT デバイスにデプロイします。

コンピューティング ターゲットに使用するコンピューティング リソースは[ワークスペース](concept-workspace.md)にアタッチされています。 ローカル コンピューター以外のコンピューティング リソースは、ワークスペースのユーザーによって共有されます。

## <a name="training-compute-targets"></a><a name="train"></a> トレーニング コンピューティング ターゲット

Azure Machine Learning では、異なるコンピューティング リソースに対してさまざまなサポートが提供されています。  また、独自のコンピューティング リソースを接続することもできますが、シナリオごとにサポートは異なることがあります。

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

[モデル トレーニングのためのコンピューティング ターゲットの設定と使用](how-to-set-up-training-targets.md)に関するページを参照してください。

## <a name="deployment-targets"></a><a name="deploy"></a>デプロイ ターゲット

次のコンピューティング リソースを使用してモデル デプロイをホストできます。

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

[モデルをコンピューティング ターゲットにデプロイする場所と方法](how-to-deploy-and-where.md)に関するページを参照してください。

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning コンピューティング (マネージド)

マネージド コンピューティング リソースは、Azure Machine Learning によって作成され、管理されます。 このコンピューティングは、機械学習ワークロード向けに最適化されています。 Azure Machine Learning コンピューティング クラスターと[コンピューティング インスタンス](concept-compute-instance.md)だけがマネージド コンピューティングです。 今後、他のマネージド コンピューティング リソースが追加される予定です。

Azure Machine Learning コンピューティング インスタンス (プレビュー) またはコンピューティング クラスターは次の場所から作成できます。
* Azure Machine Learning Studio
* Azure portal
* Python SDK [ComputeInstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py) と [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py) クラス
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets)
* Resource Manager テンプレート

また、[Azure CLI 用の機械学習拡張機能](tutorial-train-deploy-model-cli.md#create-the-compute-target-for-training)を使用してコンピューティング クラスターを作成することもできます。

作成されると、これらのコンピューティング リソースは、他の種類のコンピューティング ターゲットとは異なり、自動的にワークスペースの一部になります。

### <a name="compute-clusters"></a>コンピューティング クラスター

Azure Machine Learning コンピューティング クラスターをトレーニングやバッチ推論 (プレビュー) に使用できます。  このコンピューティング リソースは、次のものを備えています。

* シングルノードまたはマルチノード クラスター
* 実行を送信するたびに自動スケーリング 
* 自動でのクラスター管理とジョブ スケジューリング 
* CPU と GPU の両方のリソースをサポートします

### <a name="supported-vm-series-and-sizes"></a>サポートされている VM シリーズおよびサイズ

Azure Machine Learning でマネージド コンピューティング リソースのノード サイズを選択すると、Azure で使用可能な VM サイズの中から選択できます。 Azure では、さまざまなワークロードに対応する Linux および Windows の幅広いサイズが提供されています。 さまざまな [VM の種類とサイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)の詳細については、こちらを参照してください。

VM サイズを選択する際には次のような例外と制限事項があります。
* 一部の VM シリーズは、Azure Machine Learning でサポートされていません。
* 一部の VM シリーズは制限されています。 制限付きシリーズを使用するには、サポートに連絡し、そのシリーズのクォータの引き上げを依頼してください。 サポートへのお問い合わせについては、[Azure のサポート オプション](https://azure.microsoft.com/support/options/)に関するページを参照してください。

サポートされているシリーズと制限の詳細については、次の表を参照してください。 

| **サポートされている VM シリーズ**  | **制限事項** |
|------------|------------|
| D | なし |
| Dv2 | なし |  
| DSv2 | なし |  
| FSv2 | なし |  
| M | 承認が必要 |
| NC | なし |    
| NCsv2 | 承認が必要 |
| NCsv3 | 承認が必要 |  
| NDs | 承認が必要 |
| NDv2 | 承認が必要 |
| NV | なし |
| NVv3 | 承認が必要 | 


これらの VM シリーズは Azure Machine Learning でサポートされていますが、すべての Azure リージョンで使用できるとは限りません。 VM シリーズが使用可能かどうかを確認するには、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)」を参照してください。

## <a name="unmanaged-compute"></a>アンマネージド コンピューティング

アンマネージド コンピューティング先は、Azure Machine Learning によって管理*されません*。 この種類のコンピューティング ターゲットは、Azure Machine Learning の外部で作成してからワークスペースに接続します。 アンマネージド コンピューティング リソースでは、機械学習ワークロードのパフォーマンスを維持するため、または向上させるために追加の手順が必要になる場合があります。

## <a name="next-steps"></a>次のステップ

具体的には、次の方法を学習します。
* [モデルをトレーニングするためのコンピューティング ターゲットを設定する](how-to-set-up-training-targets.md)
* [モデルをコンピューティング ターゲットにデプロイする](how-to-deploy-and-where.md)

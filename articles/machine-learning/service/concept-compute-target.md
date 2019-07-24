---
title: コンピューティング ターゲット
titleSuffix: Azure Machine Learning service
description: コンピューティング ターゲットでは、トレーニング スクリプトを実行したり、サービスのデプロイをホストしたりする場所であるコンピューティング リソースを指定します。 この場所は、ローカル コンピューターでも、クラウドベースのコンピューティング リソースでもかまいません。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/30/2019
ms.openlocfilehash: 42c0f5460a63b781aafdd43410761e2d7b17944d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755475"
---
#  <a name="what-is-a-compute-target-in-azure-machine-learning-service"></a>Azure Machine Learning service のコンピューティング ターゲットとは 

コンピューティング ターゲットでは、トレーニング スクリプトを実行したり、サービスのデプロイをホストしたりする場所であるコンピューティング リソースを指定します。 この場所は、ローカル コンピューターでも、クラウドベースのコンピューティング リソースでもかまいません。

コンピューティング ターゲットを使用すれば、コードを変更しなくてもコンピューティング環境を簡単に変更できます。  一般的なモデル開発ライフサイクル:

* 少量のデータで開発/実験を始めます。 この段階では、ローカル環境を使用することをお勧めします。 たとえば、ローカル コンピューターやクラウドベースの VM などです。
* より大きなデータ セットでトレーニングをスケールアップするか、[トレーニング ターゲット](#train)のいずれかを使用して分散トレーニングを行います。  
* いずれかの[デプロイ ターゲット](#deploy)を使用して、複数の Web ホスティング環境に、または IoT デバイスにデプロイします。

コンピューティング ターゲットに使用するコンピューティング リソースは[ワークスペース](concept-workspace.md)にアタッチされています。 ローカル コンピューター以外のコンピューティング リソースは、ワークスペースのユーザーによって共有されます。

## <a name="train"></a> トレーニング ターゲット

Azure Machine Learning service では、異なるコンピューティング リソースに対してさまざまなサポートが提供されています。  独自のコンピューティング リソースを接続することもできますが、以下で説明するように、シナリオによってサポートが異なる場合があります:

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


## <a name="deploy"></a>デプロイ ターゲット

次のコンピューティング リソースを使用してモデル デプロイをホストできます。

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]


## <a name="managed-compute"></a>マネージド コンピューティング

マネージド コンピューティング リソースは、Azure Machine Learning service によって作成および管理されます。 このコンピューティングは、機械学習ワークロード向けに最適化されています。 2019 年 5 月 30 日の時点では、Azure Machine Learning コンピューティングが唯一のマネージド コンピューティングとなります。 今後、他のマネージド コンピューティング リソースが追加される予定です。

### <a name="amlcompute"></a> Azure Machine Learning コンピューティング

Azure Machine Learning コンピューティングをトレーニングやバッチ推論 (プレビュー) に使用できます。  このコンピューティング リソースは、次のものを備えています。

* シングルノードまたはマルチノード クラスター
* 実行を送信するたびに自動スケーリング 
* 自動でのクラスター管理とジョブ スケジューリング 
* CPU と GPU の両方のリソースをサポートします

Azure Machine Learning コンピューティング インスタンスは次のいずれかを使用して作成できます。

* Azure ポータル
* Azure Machine Learning SDK
* Azure CLI

他のコンピューティング リソースはすべて、ワークスペースの外部で作成してから、そのワークスペースに接続する必要があります。

## <a name="unmanaged-compute"></a>アンマネージド コンピューティング

アンマネージド コンピューティング リソースは、Azure Machine Learning service によって管理*されません*。 Azure Machine Learning の外部でこの種類のコンピューティングを作成してから、それをワークスペースにアタッチします。 アンマネージド コンピューティング リソースでは、機械学習ワークロードのパフォーマンスを維持するため、または向上させるために追加の手順が必要になる場合があります。

## <a name="next-steps"></a>次の手順

* [モデル トレーニング用のコンピューティング ターゲットを設定する](how-to-set-up-training-targets.md)
* [Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md)
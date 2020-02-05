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
ms.date: 11/04/2019
ms.openlocfilehash: 15bf6533106fe826c56b38cfb345c509d6d67966
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844094"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Azure Machine Learning でのコンピューティング先とは 

**コンピューティング ターゲット**とは、トレーニング スクリプトを実行したり、サービスのデプロイをホストしたりする、指定されたコンピューティング リソース/環境のことです。 この場所は、ローカル コンピューターでも、クラウドベースのコンピューティング リソースでもかまいません。 コンピューティング ターゲットを使用すると、コードを変更しなくても、後でコンピューティング環境を容易に変更できます。  

一般的なモデル開発ライフサイクルでは、次のことを行う可能性があります。
1. 最初に、少量のデータを開発して実験します。 この段階では、コンピューティング ターゲットとしてローカル環境 (ローカル コンピューターまたはクラウド ベースの VM) をお勧めします。 
2. より大きなデータにスケールアップするか、またはこれらの[トレーニング コンピューティング ターゲット](#train)のいずれかを使用して分散トレーニングを実行します。  
3. モデルの準備ができたら、これらの[デプロイ コンピューティング ターゲット](#deploy)のいずれかを使用して、それを Web ホスティング環境または IoT デバイスにデプロイします。

コンピューティング ターゲットに使用するコンピューティング リソースは[ワークスペース](concept-workspace.md)にアタッチされています。 ローカル コンピューター以外のコンピューティング リソースは、ワークスペースのユーザーによって共有されます。

## <a name="train"></a> トレーニング コンピューティング ターゲット

Azure Machine Learning では、異なるコンピューティング リソースに対してさまざまなサポートが提供されています。  また、独自のコンピューティング リソースを接続することもできますが、シナリオごとにサポートは異なることがあります。

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

[モデル トレーニングのためのコンピューティング ターゲットの設定と使用](how-to-set-up-training-targets.md)に関するページを参照してください。

## <a name="deploy"></a>デプロイ ターゲット

次のコンピューティング リソースを使用してモデル デプロイをホストできます。

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

[モデルをコンピューティング ターゲットにデプロイする場所と方法](how-to-deploy-and-where.md)に関するページを参照してください。

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning コンピューティング (マネージド)

マネージド コンピューティング リソースは、Azure Machine Learning によって作成され、管理されます。 このコンピューティングは、機械学習ワークロード向けに最適化されています。 Azure Machine Learning コンピューティング クラスターと[コンピューティング インスタンス](concept-compute-instance.md)だけがマネージド コンピューティングです。 今後、他のマネージド コンピューティング リソースが追加される予定です。

Azure Machine Learning コンピューティング インスタンス (プレビュー) またはコンピューティング クラスターは次の場所で作成できます。

| | Azure Machine Learning Studio | Azure portal | SDK | Resource Manager テンプレート | CLI |
|---| ----- | ----- | ----- | ----- | ----- |
| コンピューティング インスタンス | はい | はい | はい | はい |  |
| コンピューティング クラスター | はい | はい | はい | はい | はい |

作成されると、これらのコンピューティング リソースは、他の種類のコンピューティング ターゲットとは異なり、自動的にワークスペースの一部になります。

> [!NOTE]
> コンピューティング インスタンスは、**米国中北部**、** 米国東部 2**、**北ヨーロッパ**、または**英国南部**のワークスペースにのみ使用できます。
>ワークスペースが他のリージョンにある場合は、代わりに [Notebook VM](concept-compute-instance.md#notebookvm) を引き続き作成して使用することができます。 

### <a name="compute-clusters"></a>コンピューティング クラスター

Azure Machine Learning コンピューティング クラスターをトレーニングやバッチ推論 (プレビュー) に使用できます。  このコンピューティング リソースは、次のものを備えています。

* シングルノードまたはマルチノード クラスター
* 実行を送信するたびに自動スケーリング 
* 自動でのクラスター管理とジョブ スケジューリング 
* CPU と GPU の両方のリソースをサポートします



## <a name="unmanaged-compute"></a>アンマネージド コンピューティング

アンマネージド コンピューティング先は、Azure Machine Learning によって管理*されません*。 この種類のコンピューティング ターゲットは、Azure Machine Learning の外部で作成してからワークスペースに接続します。 アンマネージド コンピューティング リソースでは、機械学習ワークロードのパフォーマンスを維持するため、または向上させるために追加の手順が必要になる場合があります。

## <a name="next-steps"></a>次のステップ

具体的には、次の方法を学習します。
* [モデルをトレーニングするためのコンピューティング ターゲットを設定する](how-to-set-up-training-targets.md)
* [モデルをコンピューティング ターゲットにデプロイする](how-to-deploy-and-where.md)
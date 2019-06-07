---
title: MLOps:ML モデルを管理、デプロイ、および監視する
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service をモデルの MLOps (デプロイ、管理、および監視) に使用して、継続的にモデルを向上させる方法について説明します。 トレーニングしたモデルを、ローカル コンピューターまたは他のソースから Azure Machine Learning service を使用してデプロイできます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 416bebc070cfcad52c6180e65f0066c46c826cbe
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2019
ms.locfileid: "65849645"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps:Azure Machine Learning service でモデルを管理、デプロイ、および監視する

この記事では、Azure Machine Learning service を使用して、お使いのモデルをデプロイ、管理、および監視して、継続的に向上させる方法を説明します。 トレーニングしたモデルを、ローカル コンピューターまたは他のソースから Azure Machine Learning を使用してデプロイできます。 

次の図は、完全なデプロイ ワークフローを示しています。[![Azure Machine Learning のデプロイ ワークフロー](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

MLOps / デプロイ ワークフローには次の手順が含まれます。
1. **モデルを登録する**。お使いの Azure Machine Learning service ワークスペースでホストされているレジストリに登録します
1. **モデルを使用する**。IoT デバイス上で、または Power BI を使用した分析に、クラウド内の Web サービスでモデルを使用します。
1. **データを監視して収集する**
1. 新しいイメージを使用するようにデプロイを**更新します**。

各手順は、個別に、または 1 つのコマンドの一部として実行できます。 さらに、次の図に示すように、**CI/CD ワークフロー**を作成することもできます。

[!["Azure Machine Learning の継続的インテグレーション/継続的デプロイ (CI/CD) サイクル"](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="step-1-register-model"></a>手順 1:モデルの登録

モデルの登録によって、モデルを Azure クラウドに格納し、ワークスペースでバージョンを管理できます。 モデルのレジストリでは、トレーニングしたモデルの整理と追跡が容易に行えます。

> [!TIP]
> Azure Machine Learning service の外部でトレーニングされたモデルを登録することもできます。
 
登録されたモデルは、名前とバージョンによって識別されます。 モデルを登録するたびに、既存のモデルと同じ名前で登録され、レジストリによってバージョンがインクリメントされます。 モデルの検索時に使用できる追加のメタデータ タグを、登録中に指定することもできます。 Azure Machine Learning service は、Python 3.5.2 以上を使用して読み込むことができる任意のモデルをサポートしています。

アクティブ デプロイで使用されているモデルは削除できません。

詳細については、[モデルのデプロイ](how-to-deploy-and-where.md#registermodel)に関するページの、モデルの登録のセクションを参照してください。

pickle 形式で格納されているモデルの登録の例については、「[チュートリアル: 画像分類モデルをトレーニングする](tutorial-deploy-models-with-aml.md)」を参照してください。

## <a name="step-2-use-the-model"></a>手順 2:モデルの使用

機械学習モデルは、IoT Edge デバイスで、または Power BI などのサービスからの分析に Web サービスとして使用できます。

### <a name="web-service"></a>Web サービス

次のコンピューティング ターゲットを持つ **Web サービス**でモデルを使用することができます。

* Azure Container Instances
* Azure Kubernetes Service

モデルを Web サービスとしてデプロイするには、次を指定する必要があります。

* モデル、またはモデルのアンサンブル。
* モデルを使用するために必要な依存関係。 たとえば、要求を受け取って、モデルや Conda 依存関係などを起動するスクリプト。
* モデルのデプロイ方法とデプロイ先を記述するデプロイ構成。

詳細については、「[モデルのデプロイ](how-to-deploy-and-where.md)」を参照してください。

### <a name="iot-edge-devices"></a>IoT Edge デバイス

**Azure IoT Edge モジュール**を介してモデルを IoT デバイスで使用することができます。 IoT Edge モジュールはハードウェア デバイスに展開されるため、デバイス上で推論、つまりモデルのスコアリングを使用できます。

詳細については、「[モデルのデプロイ](how-to-deploy-and-where.md)」を参照してください。

### <a name="analytics"></a>Analytics

Microsoft Power BI は、データ分析への機械学習モデルの使用をサポートします。 詳しくは、「[Azure Machine Learning の Power BI への統合 (プレビュー)](https://docs.microsoft.com/power-bi/service-machine-learning-integration)」をご覧ください。

## <a name="step-3-monitor-models-and-collect-data"></a>手順 3:モデルを監視して、データを収集する

監視により、モデルにどのようなデータが送信されているか、および返される予測を理解することができます。

この情報は、自分のモデルがどのように使用されているかを理解するのに役立ちます。 収集された入力データは、モデルの将来のバージョンのトレーニングにも役立つ可能性があります。

詳細については、[モデル データ収集を有効にする方法](how-to-enable-data-collection.md)に関するページを参照してください。

## <a name="step-4-update-the-deployment"></a>手順 4:デプロイを更新する

デプロイは明示的に更新される必要があります。 詳細については、[モデルのデプロイ](how-to-deploy-and-where.md#update)に関するページの、更新のセクションを参照してください。

## <a name="next-steps"></a>次の手順

Azure Machine Learning サービスを使用して[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)を確認します。 デプロイの例については、「[チュートリアル: Azure Container Instances に画像分類モデルをデプロイする](tutorial-deploy-models-with-aml.md)」を参照してください。

[Azure Pipelines を使用して ML モデルの継続的な統合とデプロイ](/azure/devops/pipelines/targets/azure-machine-learning)を作成する方法について説明します。 

[デプロイされたモデルを Web サービスとして使用する](how-to-consume-web-service.md)クライアント アプリケーションとサービスの作成方法を確認します。

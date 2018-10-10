---
title: Azure Machine Learning service でモデルをデプロイする場所 | Microsoft Docs
description: Azure Machine Learning service を使用して運用環境にモデルをデプロイできるさまざまな方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/29/2018
ms.openlocfilehash: 606e8aed42bce69d5b3210b4e97f8cbfeaaf104c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961010"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Azure Machine Learning service を使用してモデルをデプロイする

Azure Machine Learning service には、トレーニング済みモデルをデプロイする方法が複数用意されています。 このドキュメントでは、Web サービスとして Azure クラウドに、または IoT エッジ デバイスに、モデルをデプロイする方法を説明します。

次のコンピューティング ターゲットにモデルをデプロイできます。

- Azure Container Instances (ACI)
- Azure Kubernetes Service (AKS)
- Azure IoT Edge
- フィールド プログラマブル ゲート アレイ (FPGA)

このドキュメントの残りの部分では、これらの各オプションについて詳しく説明します。

## <a name="azure-container-instances"></a>Azure Container Instances

以下の条件が 1 つでも満たされている場合は、Azure Container Instances を使用し REST API エンドポイントとしてモデルをデプロイします。
- モデルのスコアリングと検証のための簡単なデプロイ方法を探している。 ACI のデプロイは、通常、5 分もかからずに完了します。
- 開発環境またはテスト環境にモデルをデプロイする。 ACI では、サブスクリプションあたり 20 個のコンテナー グループをデプロイできます。 詳しくは、「[Azure Container Instances のクォータとリージョンの可用性](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)」をご覧ください。

詳しくは、[Azure Container Instances へのモデルのデプロイ](how-to-deploy-to-aci.md)に関するページをご覧ください。

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

高スケールの運用シナリオでは、Azure Kubernetes Service (AKS) にモデルをデプロイすることができます。 既存の AKS クラスターを使用するか、Azure Machine Learning SDK、CLI、または Azure portal を使用して新しいクラスターを作成できます。

AKS クラスターの作成は、ワークスペースに対する 1 回限りのプロセスです。 一度作成すれば、複数のデプロイでこのクラスターを再利用できます。 クラスターまたはそれを含むリソース グループを削除した場合、次回デプロイする必要があるときに、新しいクラスターを作成する必要があります。

AKS にデプロイすることで、ご利用の Web サービスに関する、自動スケーリング、ログ記録、モデル データ収集、および高速応答時間が提供されます。 

AKS クラスターを作成するプロセスには、約 20 分かかります。

詳しくは、[Azure Kubernetes Service へのモデルのデプロイ](how-to-deploy-to-aks.md)に関するページをご覧ください。

## <a name="azure-iot-edge"></a>Azure IoT Edge

IoT デバイスでは、デバイスでスコアリングを実行する方が、クラウドにデータを送信し、クラウドでホストされているモデルからデータが返されるのを待つより高速です。 Azure IoT Edge では、エッジ デバイスでモデルをホストできます。 次のいずれかの機能が必要な場合は、IoT Edge にモデルをデプロイします。
- クラウドに接続していなくても、ローカルで優先度の高いタスクを処理する
- クラウドから迅速にプルするには大きすぎる生成されたデータを操作する
- ローカル デバイスの内部または近くのインテリジェンスでリアルタイム処理を有効にする
- データのプライバシー関連の要件に対応する 

詳しくは、[Azure IoT Edge へのデプロイ](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)に関するドキュメントをご覧ください。

IoT Edge サービスについて詳しくは、[Azure IoT Edge のドキュメント](https://docs.microsoft.com/azure/iot-edge/)をご覧ください。


## <a name="field-programmable-gate-arrays-fpga"></a>フィールド プログラマブル ゲート アレイ (FPGA)

Project Brainwave を利用する Hardware Accelerated Models を使用すると、リアルタイムの推論要求に対して非常に短い待機時間を実現できます。 Project Brainwave では、Azure クラウドのフィールド プログラマブル ゲート アレイにデプロイされているディープ ニューラル ネットワーク (DNN) が高速化されます。 一般に使用される DNN は、転移学習の Featurizer として使用したり、独自のデータからトレーニングされた重みでカスタマイズしたりできます。

詳しくは、[FPGA へのデプロイ](how-to-deploy-fpga-web-service.md)に関するドキュメントをご覧ください。

## <a name="next-steps"></a>次の手順

特定のコンピューティング ターゲットにモデルをデプロイする方法については、次のドキュメントをご覧ください。

* [Azure Container Instances にモデルをデプロイする](how-to-deploy-to-aci.md)
* [Azure Kubernetes Service にモデルをデプロイする](how-to-deploy-to-aks.md)
* [Azure IoT Edge にモデルをデプロイする](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)
* [FPGA にモデルをデプロイする](how-to-deploy-fpga-web-service.md)

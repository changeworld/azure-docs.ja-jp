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
ms.openlocfilehash: 97ac405db3de4fa2c6f1173f813eafd41a5361ad
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209447"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Azure Machine Learning service を使用してモデルをデプロイする

Azure Machine Learning service には、トレーニング済みモデルをデプロイする方法が複数用意されています。 このドキュメントでは、Web サービスとして Azure クラウドに、または IoT エッジ デバイスに、モデルをデプロイする方法を説明します。

次のコンピューティング ターゲットにモデルをデプロイできます。

- [Azure Container Instances (ACI)](#aci): 高速なデプロイ。 開発またはテストに適しています。
- [Azure Kubernetes Service (AKS)](#aks): 高スケールの運用デプロイに適してします。 自動スケール、および高速な応答時間を実現します。
- [Azure IoT Edge](#iotedge): IoT デバイスにモデルをデプロイします。 推論はデバイス上で行われます。
- [フィールド プログラマブル ゲート アレイ (FPGA)](#fpga): 待機時間が非常に短いリアルタイムの推論。

このドキュメントの残りの部分では、これらの各オプションについて詳しく説明します。

## <a id="aci"></a>Azure Container Instances

以下の条件が 1 つでも満たされている場合は、Azure Container Instances を使用し REST API エンドポイントとしてモデルをデプロイします。
- モデルを迅速にデプロイおよび検証する必要があります。 ACI のデプロイは、5 分もかからずに完了します。
- 開発環境またはテスト環境にモデルをデプロイする。 ACI では、サブスクリプションあたり 20 個のコンテナー グループをデプロイできます。 詳しくは、「[Azure Container Instances のクォータとリージョンの可用性](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)」をご覧ください。

詳しくは、[Azure Container Instances へのモデルのデプロイ](how-to-deploy-to-aci.md)に関するページをご覧ください。

## <a id="aks"></a>Azure Kubernetes Service

高スケールの運用のシナリオでは、Azure Kubernetes Service (AKS) を使用します。 既存の AKS クラスターを使用するか、Azure Machine Learning SDK、CLI、または Azure portal を使用して新しいクラスターを作成できます。

AKS クラスターの作成は、ワークスペースに対する 1 回限りのプロセスです。 複数のデプロイでこのクラスターを再利用できます。 クラスターを削除した場合、次回デプロイする必要があるときに、新しいクラスターを作成する必要があります。

Azure Kubernetes Service には次の機能があります。

* 自動スケール
* ログの記録
* モデル データ収集
* Web サービスの高速な応答時間

AKS クラスターを作成するプロセスには、約 20 分かかります。

詳しくは、[Azure Kubernetes Service へのモデルのデプロイ](how-to-deploy-to-aks.md)に関するページをご覧ください。

## <a id="iotedge"></a>Azure IoT Edge

IoT デバイスでは、デバイスでスコアリングを実行する方が、スコアリングのためにクラウドにデータを送信するより高速です。 Azure IoT Edge では、エッジ デバイスでモデルをホストできます。 次のいずれかの機能が必要な場合は、IoT Edge にモデルをデプロイします。
- クラウドに接続していなくても、ローカルで優先度の高いタスクを処理する
- クラウドから迅速にプルするには大きすぎる生成されたデータを操作する
- ローカル デバイスの内部または近くのインテリジェンスでリアルタイム処理を有効にする
- データのプライバシー関連の要件に対応する 

詳しくは、[Azure IoT Edge へのデプロイ](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)に関するドキュメントをご覧ください。

IoT Edge サービスについて詳しくは、[Azure IoT Edge のドキュメント](https://docs.microsoft.com/azure/iot-edge/)をご覧ください。


## <a id="fpga"></a>フィールド プログラマブル ゲート アレイ (FPGA)

Project Brainwave を利用する Hardware Accelerated Models を使用すると、リアルタイムの推論要求に対して非常に短い待機時間を実現できます。 Project Brainwave では、Azure クラウドのフィールド プログラマブル ゲート アレイにデプロイされているディープ ニューラル ネットワーク (DNN) が高速化されます。 一般に使用される DNN は、転移学習の Featurizer として使用したり、独自のデータからトレーニングされた重みでカスタマイズしたりできます。

詳しくは、[FPGA へのデプロイ](how-to-deploy-fpga-web-service.md)に関するドキュメントをご覧ください。

## <a name="next-steps"></a>次の手順

特定のコンピューティング ターゲットにモデルをデプロイする方法については、次のドキュメントをご覧ください。

* [Azure Container Instances にモデルをデプロイする](how-to-deploy-to-aci.md)
* [Azure Kubernetes Service にモデルをデプロイする](how-to-deploy-to-aks.md)
* [Azure IoT Edge にモデルをデプロイする](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)
* [FPGA にモデルをデプロイする](how-to-deploy-fpga-web-service.md)

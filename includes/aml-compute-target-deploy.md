---
title: インクルード ファイル
description: インクルード ファイル
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/23/2019
ms.openlocfilehash: 1bf1202a635184ad7f0022cf5baa9d72e8547f14
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2019
ms.locfileid: "74549443"
---
| コンピューティング ターゲット | 使用対象 | GPU のサポート | FPGA のサポート | 説明 |
| ----- | ----- | ----- | ----- | ----- |
| [ローカル &nbsp;Web&nbsp; サービス](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | テスト/デバッグ | &nbsp; | &nbsp; | 制限付きのテストとトラブルシューティングに使用。 ハードウェア アクセラレーションは、ローカル システムでのライブラリの使用に依存します。
| [Azure Machine Learning コンピューティング インスタンス&nbsp;Web&nbsp;サービス](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | テスト/デバッグ | &nbsp; | &nbsp; | 制限付きのテストとトラブルシューティングに使用。
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | リアルタイムの推論 |  [はい](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md) (Web サービスのデプロイ) | [はい](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |高スケールの運用デプロイに使用。 高速な応答時間と、デプロイされたサービスの自動スケールを提供します。 Azure Machine Learning SDK では、クラスターの自動スケールはサポートされていません。 AKS クラスター内のノードを変更するには、Azure portal でお使いの AKS クラスター用の UI を使用します。 AKS は、デザイナーで使用できる唯一のオプションです。 |
| [Azure Container Instances](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | テストまたは開発 | &nbsp;  | &nbsp; | 必要な RAM が 48 GB より少ない低スケール CPU ベース ワークロードに使用。 |
| [Azure Machine Learning コンピューティング クラスター](../articles/machine-learning/service/how-to-run-batch-predictions.md) | (プレビュー) バッチ &nbsp;推論 | [はい](../articles/machine-learning/service/how-to-run-batch-predictions.md) (機械学習パイプライン) | &nbsp;  | サーバーレス コンピューティングでバッチ スコアリングを実行します。 優先順位が中程度または低い VM をサポートします。 |
| [Azure Functions](../articles/machine-learning/service/how-to-deploy-functions.md) | (プレビュー) リアルタイムの推論 | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | (プレビュー) IoT &nbsp;モジュール |  &nbsp; | &nbsp; | IoT デバイスに ML モデルをデプロイし、サービスを提供します。 |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | IoT Edge を使用 |  &nbsp; | はい | IoT デバイスに ML モデルをデプロイし、サービスを提供します。 |

> [!NOTE]
> ローカル、Azure Machine Learning コンピューティング インスタンス、Azure Machine Learning コンピューティング クラスターなどのコンピューティング先では、トレーニングと実験のために GPU をサポートしていますが、__Web サービスとしてデプロイされる場合__ に、推論に GPU を使用することは、Azure Kubernetes Service でのみサポートされています。
>
> __機械学習パイプラインでスコアリングするとき__、推論に GPU を使用することは、Azure Machine Learning コンピューティングでのみサポートされます。

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
ms.openlocfilehash: e402989c6bdbbc90ae10047fcf19f4f2d74328a6
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014783"
---
| コンピューティング ターゲット | 使用法 | GPU のサポート | FPGA のサポート | 説明 |
| ----- | ----- | ----- | ----- | ----- |
| [ローカル &nbsp;Web&nbsp; サービス](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | テスト/デバッグ | &nbsp; | &nbsp; | 制限付きのテストとトラブルシューティングに最適。 ハードウェア アクセラレーションは、ローカル システムでのライブラリの使用に依存します。
| [Notebook VM&nbsp;Web&nbsp;サービス](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | テスト/デバッグ | &nbsp; | &nbsp; | 制限付きのテストとトラブルシューティングに最適。 
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | リアルタイムの推論 |  [はい](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [はい](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |高スケールの運用デプロイに適しています。 高速な応答時間と、デプロイされたサービスの自動スケールを提供します。 Azure Machine Learning SDK では、クラスターの自動スケールはサポートされていません。 AKS クラスター内のノードを変更するには、Azure portal でお使いの AKS クラスター用の UI を使用します。 AKS は、ビジュアル インターフェイスに使用できる唯一のオプションです。 |
| [Azure Container Instances (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | テストまたは開発 | &nbsp;  | &nbsp; | 必要な RAM が 48 GB 未満で CPU ベースの小規模なワークロードに最適。 |
| [Azure Machine Learning コンピューティング](../articles/machine-learning/service/how-to-run-batch-predictions.md) | (プレビュー) バッチ &nbsp;推論 | &nbsp; | &nbsp;  | サーバーレス コンピューティングでバッチ スコアリングを実行します。 優先順位が中程度または低い VM をサポートします。 |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | (プレビュー) IoT &nbsp;モジュール |  &nbsp; | &nbsp; | IoT デバイスに ML モデルをデプロイし、サービスを提供します。 |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | IoT Edge を使用 |  &nbsp; | はい | IoT デバイスに ML モデルをデプロイし、サービスを提供します。 |

> [!NOTE]
> ローカル、Notebook VM、Azure Machine Learning コンピューティングなどのコンピューティング先はトレーニングと実験のために GPU をサポートしていますが、推論に GPU を使用することは、Azure Kubernetes Service でのみサポートされています。
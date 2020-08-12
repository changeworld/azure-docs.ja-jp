---
title: モデルをデプロイする方法と場所
titleSuffix: Azure Machine Learning
description: Azure Container Instances、Azure Kubernetes Service、Azure IoT Edge、フィールド プログラマブル ゲート アレイなど、Azure Machine Learning モデルをデプロイする方法と場所について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
zone_pivot_groups: aml-control-methods
ms.openlocfilehash: 42205f87e2025fa7f4492cb76aeb44a1fbf46eb6
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542793"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Azure Machine Learning を使用してモデルをデプロイする
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Web サービスとして Azure クラウドに、または Azure IoT Edge デバイスに機械学習モデルをデプロイする方法を説明します。

モデルをどこにデプロイするかに関係なく、ワークフローは同様です。

1. モデルを登録します。
1. 推論構成ファイルを準備します
1. エントリ スクリプトを準備します ([コードなしのデプロイ](how-to-deploy-no-code-deployment.md)を使用する場合を除く)
1. コンピューティング ターゲットにモデルをデプロイします。
1. デプロイしたモデル (Web サービスとも呼ばれる) をテストします。

デプロイ ワークフローに関連する概念の詳細については、[Azure Machine Learning でのモデルの管理、デプロイ、監視](concept-model-management-and-deployment.md)に関する記事を参照してください。


 
::: zone pivot="cli"
[!INCLUDE [CLI quickstart](../../includes/machine-learning-how-to-deploy-and-where-cli.md)]
::: zone-end

::: zone pivot="py-sdk"
[!INCLUDE [SDK quickstart](../../includes/machine-learning-how-to-deploy-and-where-sdk.md)]
::: zone-end


### <a name="understanding-service-state"></a>サービスの状態について

モデルのデプロイ中に、完全にデプロイされるまでの間に、サービスの状態が変化することがあります。

次の表で、サービスの各状態について説明します。

| Web サービスの状態 | 説明 | 最終的な状態
| ----- | ----- | ----- |
| 移行中 | サービスはデプロイ処理中です。 | いいえ |
| 異常 | サービスはデプロイされましたが、現在アクセスできません。  | いいえ |
| スケジュール不可 | リソースが不足しているため、現時点ではサービスをデプロイできません。 | いいえ |
| 失敗 | エラーまたはクラッシュが発生したため、サービスをデプロイできませんでした。 | はい |
| Healthy | サービスは正常であり、エンドポイントを使用できます。 | はい |


### <a name="batch-inference"></a><a id="azuremlcompute"></a> バッチ推論
Azure Machine Learning のコンピューティング先は、Azure Machine Learning によって作成され、管理されます。 これは Azure Machine Learning パイプラインからのバッチ予測に使用できます。

Azure Machine Learning コンピューティングを使用したバッチ推論のチュートリアルについては、[バッチ予測を実行する方法](tutorial-pipeline-batch-scoring-classification.md)に関するページを参照してください。

### <a name="iot-edge-inference"></a><a id="iotedge"></a> IoT Edge の推論
エッジにデプロイするためのサポートは現在、プレビューの段階にあります。 詳細については、[IoT Edge モジュールとしての Azure Machine Learning のデプロイ](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)に関する記事を参照してください。


## <a name="next-steps"></a>次のステップ

* [失敗したデプロイのトラブルシューティング](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service にデプロイする](how-to-deploy-azure-kubernetes-service.md)
* [Web サービスを使用するクライアント アプリケーションを作成する](how-to-consume-web-service.md)
* [Web サービスを更新する](how-to-deploy-update-web-service.md)
* [カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)
* [TLS を使用して Azure Machine Learning による Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Application Insights を使用して Azure Machine Learning のモデルを監視する](how-to-enable-app-insights.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)
* [モデル デプロイのイベント アラートおよびトリガーを作成する](how-to-use-event-grid.md)


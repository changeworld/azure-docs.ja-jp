---
title: CLI (v2) YAML スキーマの概要
titleSuffix: Azure Machine Learning
description: CLI (v2) YAML スキーマの概要とインデックス。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: 4d98ebbebbb25e2ab903e06162114f8fa026fbda
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781190"
---
# <a name="cli-v2-yaml-schemas"></a>CLI (v2) YAML スキーマ

Azure CLI の拡張機能である Azure Machine Learning CLI (v2) では、特定のスキーマを持つ YAML ファイルが使用され、必要とされることが頻繁にあります。 この記事では、YAML ファイルのリファレンス ドキュメントとソース スキーマの一覧します。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="assets"></a>アセット

リファレンス | URI
- | -
[ワークスペース](reference-yaml-workspace.md) | https://azuremlschemas.azureedge.net/latest/workspace.schema.json
[Compute](reference-yaml-compute.md) | https://azuremlschemas.azureedge.net/latest/compute.schema.json
[環境](reference-yaml-environment.md) | https://azuremlschemas.azureedge.net/latest/environment.schema.json
[データセット](reference-yaml-dataset.md) | https://azuremlschemas.azureedge.net/latest/dataset.schema.json
[Model](reference-yaml-model.md) | https://azuremlschemas.azureedge.net/latest/model.schema.json

## <a name="datastores"></a>データストア

リファレンス | URI
- | -
[Azure BLOB](reference-yaml-datastore-blob.md) | https://azuremlschemas.azureedge.net/latest/azureBlob.schema.json
[Azure Files](reference-yaml-datastore-files.md) | https://azuremlschemas.azureedge.net/latest/azureFile.schema.json
[Azure Data Lake Gen1](reference-yaml-datastore-data-lake-gen1.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen1.schema.json
[Azure Data Lake Gen2](reference-yaml-datastore-data-lake-gen2.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen2.schema.json

## <a name="jobs"></a>ジョブ

リファレンス | URI
- | -
[コマンド](reference-yaml-job-command.md) | https://azuremlschemas.azureedge.net/latest/commandJob.schema.json
[スイープ](reference-yaml-job-sweep.md) | https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json

## <a name="endpoints"></a>エンドポイント

リファレンス | URI
- | -
[マネージド オンライン (リアルタイム)](reference-yaml-endpoint-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.schema.json
[マネージド バッチ](reference-yaml-endpoint-managed-batch.md) | https://azuremlschemas.azureedge.net/latest/batchEndpoint.schema.json
[Kubernetes (k8s) オンライン (リアルタイム)](reference-yaml-endpoint-k8s-online.md) | https://azuremlschemas.azureedge.net/latest/k8sOnlineEndpoint.schema.json

## <a name="deployments"></a>デプロイ

リファレンス | URI
- | -
[マネージド オンライン (リアルタイム)](reference-yaml-deployment-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineDeployment.schema.json
[マネージド バッチ](reference-yaml-deployment-managed-batch.md) | https://azuremlschemas.azureedge.net/latest/batchDeployment.schema.json
[Kubernetes (k8s) オンライン (リアルタイム)](reference-yaml-deployment-k8s-online.md) | https://azuremlschemas.azureedge.net/latest/k8sOnelineDeployment.schema.json

## <a name="next-steps"></a>次のステップ

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)

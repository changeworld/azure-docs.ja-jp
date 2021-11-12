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
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 541cb9049be64977858a10cd486605f9572b3f30
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561349"
---
# <a name="cli-v2-yaml-schemas"></a>CLI (v2) YAML スキーマ

Azure CLI の拡張機能である Azure Machine Learning CLI (v2) では、特定のスキーマを持つ YAML ファイルが使用され、必要とされることが頻繁にあります。 この記事では、YAML ファイルのリファレンス ドキュメントとソース スキーマの一覧します。 例は個々の記事内にあります。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="workspace"></a>ワークスペース

| リファレンス | URI |
| - | - |
| [ワークスペース](reference-yaml-workspace.md) | https://azuremlschemas.azureedge.net/latest/workspace.schema.json |

## <a name="environment"></a>環境

| リファレンス | URI |
| - | - |
| [環境](reference-yaml-environment.md) | https://azuremlschemas.azureedge.net/latest/environment.schema.json |

## <a name="dataset"></a>データセット

| リファレンス | URI |
| - | - |
| [データセット](reference-yaml-dataset.md) | https://azuremlschemas.azureedge.net/latest/dataset.schema.json |

## <a name="model"></a>モデル

| リファレンス | URI |
| - | - |
| [Model](reference-yaml-model.md) | https://azuremlschemas.azureedge.net/latest/model.schema.json |

## <a name="compute"></a>Compute

| リファレンス | URI |
| - | - |
| [コンピューティング クラスター (AmlCompute)](reference-yaml-compute-aml.md) | https://azuremlschemas.azureedge.net/latest/amlCompute.schema.json |
| [コンピューティング インスタンス](reference-yaml-compute-instance.md) | https://azuremlschemas.azureedge.net/latest/computeInstance.schema.json |
| [接続された仮想マシン](reference-yaml-compute-vm.md) | https://azuremlschemas.azureedge.net/latest/vmCompute.schema.json |

## <a name="job"></a>ジョブ

| リファレンス | URI |
| - | - |
| [コマンド](reference-yaml-job-command.md) | https://azuremlschemas.azureedge.net/latest/commandJob.schema.json |
| [スイープ](reference-yaml-job-sweep.md) | https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json |
| [パイプライン](reference-yaml-job-pipeline.md) | https://azuremlschemas.azureedge.net/latest/pipelineJob.schema.json |

## <a name="datastore"></a>データストア

| リファレンス | URI |
| - | - |
| [Azure BLOB](reference-yaml-datastore-blob.md) | https://azuremlschemas.azureedge.net/latest/azureBlob.schema.json |
| [Azure Files](reference-yaml-datastore-files.md) | https://azuremlschemas.azureedge.net/latest/azureFile.schema.json |
| [Azure Data Lake Gen1](reference-yaml-datastore-data-lake-gen1.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen1.schema.json |
| [Azure Data Lake Gen2](reference-yaml-datastore-data-lake-gen2.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen2.schema.json |

## <a name="endpoint"></a>エンドポイント

| リファレンス | URI |
| - | - |
| [マネージド オンライン (リアルタイム)](reference-yaml-endpoint-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.schema.json |
| [Batch](reference-yaml-endpoint-batch.md) | https://azuremlschemas.azureedge.net/latest/batchEndpoint.schema.json |

## <a name="deployment"></a>デプロイ

| リファレンス | URI |
| - | - |
| [マネージド オンライン (リアルタイム)](reference-yaml-deployment-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineDeployment.schema.json |
| [Batch](reference-yaml-deployment-batch.md) | https://azuremlschemas.azureedge.net/latest/batchDeployment.schema.json |

## <a name="component"></a>コンポーネント

| リファレンス | URI |
| - | - |
| [コマンド](reference-yaml-component-command.md) | https://azuremlschemas.azureedge.net/latest/commandComponent.schema.json |

## <a name="next-steps"></a>次の手順

- [CLI (v2) をインストールして使用する](how-to-configure-cli.md)

---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: 03b40b1f334816f08d7e169f8ff78bdb7c06c4de
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348498"
---
`inferenceconfig.json` ドキュメント内のエントリは、[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) クラスのパラメーターにマップされます。 次の表は、JSON ドキュメントのエントリとメソッド用パラメーターの間のマッピングについてまとめたものです。

| JSON エンティティ | メソッド パラメーター | 説明 |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | イメージに対して実行するコードを含むローカル ファイルのパス。 |
| `runtime` | `runtime` | イメージに使用するランタイム。 現在サポートされているランタイムは "spark-py" と "python" です。 |
| `condaFile` | `conda_file` | 省略可能。 イメージに使用する Conda 環境定義を含むローカル ファイルのパス。 |
| `extraDockerFileSteps` | `extra_docker_file_steps` | 省略可能。 イメージを設定するときに実行する追加の Docker 手順を含むローカル ファイルのパス。 |
| `sourceDirectory` | `source_directory` | 省略可能。 イメージを作成するためのファイルをすべて含むフォルダーのパス。 |
| `enableGpu` | `enable_gpu` | 省略可能。 イメージで GPU サポートを有効にするかどうか。 GPU イメージは Microsoft Azure サービス上で使用する必要があります。 たとえば、Azure Container Instances、Azure Machine Learning コンピューティング、Azure Virtual Machines、Azure Kubernetes Service などです。 既定値は False です。 |
| `baseImage` | `base_image` | 省略可能。 基本イメージとして使用するカスタム イメージ。 基本イメージが指定されていない場合、指定されたランタイム パラメーターに基づいた基本イメージが使用されます。 |
| `baseImageRegistry` | `base_image_registry` | 省略可能。 基本イメージを含むイメージ レジストリ。 |
| `cudaVersion` | `cuda_version` | 省略可能。 GPU のサポートが必要なイメージにインストールする CUDA のバージョン。 GPU イメージは Microsoft Azure サービス上で使用する必要があります。 たとえば、Azure Container Instances、Azure Machine Learning コンピューティング、Azure Virtual Machines、Azure Kubernetes Service などです。 サポートされているバージョンは 9.0、9.1、10.0 です。 "enable_gpu" が設定されている場合、既定値は "9.1" です。 |
| `description` | `description` |  このイメージの説明。 |

次の JSON は、CLI で使用する推論構成の例です。

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```
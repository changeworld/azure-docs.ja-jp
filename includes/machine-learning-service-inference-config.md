---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 6970732f53ffa99849e20f279c8bdf4160c30a0a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845153"
---
`inferenceconfig.json` ドキュメント内のエントリは、[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) クラスのパラメーターにマップされます。 次の表は、JSON ドキュメントのエントリとメソッド用パラメーターの間のマッピングについてまとめたものです。

| JSON エンティティ | メソッド パラメーター | [説明] |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | イメージに対して実行するコードを含むローカル ファイルのパス。 |
| `runtime` | `runtime` | 省略可能。 イメージに使用するランタイム。 サポートされているランタイムは `spark-py` と `python` です。 `environment` が設定されている場合、このエントリは無視されます。 |
| `condaFile` | `conda_file` | 省略可能。 イメージに使用する Conda 環境定義を含むローカル ファイルのパス。  `environment` が設定されている場合、このエントリは無視されます。 |
| `extraDockerFileSteps` | `extra_docker_file_steps` | 省略可能。 イメージを設定するときに実行する追加の Docker 手順を含むローカル ファイルのパス。  `environment` が設定されている場合、このエントリは無視されます。|
| `sourceDirectory` | `source_directory` | 省略可能。 イメージを作成するためのすべてのファイルを含むフォルダーへのパス。パスにより、このフォルダーまたはサブ フォルダー内の任意のファイルへのアクセスが簡単になります。 ローカル コンピューターから Webservice の依存関係として、フォルダー全体をアップロードできます。 注: entry_script、conda_file、および extra_docker_file_steps パスは、source_directory パスへの相対パスです。 |
| `enableGpu` | `enable_gpu` | 省略可能。 イメージで GPU サポートを有効にするかどうか。 GPU イメージは、Azure Container Instances などの Azure サービスで使用する必要があります。 たとえば、Azure Machine Learning コンピューティング、Azure Virtual Machines、Azure Kubernetes Service などです。 既定値は False です。 `environment` が設定されている場合、このエントリは無視されます。|
| `baseImage` | `base_image` | 省略可能。 基本イメージとして使用するカスタム イメージ。 基本イメージが指定されていない場合、イメージは指定されたランタイム パラメーターに基づきます。 `environment` が設定されている場合、このエントリは無視されます。 |
| `baseImageRegistry` | `base_image_registry` | 省略可能。 基本イメージを含むイメージ レジストリ。 `environment` が設定されている場合、このエントリは無視されます。|
| `cudaVersion` | `cuda_version` | 省略可能。 GPU のサポートが必要なイメージにインストールする CUDA のバージョン。 GPU イメージは、Azure サービスで使用する必要があります。 たとえば、Azure Container Instances、Azure Machine Learning コンピューティング、Azure Virtual Machines、Azure Kubernetes Service などです。 サポートされているバージョンは 9.0、9.1、10.0 です。 `enable_gpu` が設定されている場合、既定値は 9.1 です。 `environment` が設定されている場合、このエントリは無視されます。 |
| `description` | `description` | イメージの説明。 `environment` が設定されている場合、このエントリは無視されます。  |
| `environment` | `environment` | 省略可能。  Azure Machine Learning [環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)。|

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

Azure Machine Learning [環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)の完全な仕様を、推論構成ファイルに含めることができます。 この環境がワークスペースに存在しない場合は、Azure Machine Learning によって作成されます。 それ以外の場合は、必要に応じて Azure Machine Learning によって環境が更新されます。 次の JSON は例です。

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

また、分離された CLI パラメーターで既存の Azure Machine Learning [環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)を使用し、推論構成ファイルから "環境" キーを削除することもできます。 環境名には -e を使用し、環境のバージョンには --ev を使用します。 --ev を指定しない場合は、最新バージョンが使用されます。 推論構成ファイルの例を次に示します。

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

次のコマンドは、前述の推論構成ファイル (myInferenceConfig.json という名前) を使用してモデルをデプロイする方法を示しています。 

また、既存の Azure Machine Learning [環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) (AzureML-Minimal という名前) の最新バージョンも使用します。

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```

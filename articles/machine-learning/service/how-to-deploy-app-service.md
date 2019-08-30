---
title: Azure App Service に ML モデルをデプロイする (プレビュー)
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service を使用して Azure App Service の Web アプリにモデルをデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/01/2019
ms.openlocfilehash: ada2a19de12c2f3f6b23fcc3d759afb0c747d37d
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897465"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Azure App Service に機械学習モデルをデプロイする (プレビュー)

Azure Machine Learning service から Web アプリとして Azure App Service にモデルをデプロイする方法について説明します。

> [!IMPORTANT]
> Azure Machine Learning service と Azure App Service の両方が一般公開されていますが、Machine Learning サービスから App Service にモデルをデプロイする機能はプレビュー段階です。

Azure Machine Learning service を使用すると、トレーニング済みの機械学習モデルから Docker イメージを作成できます。 このイメージには、データを受信してモデルに送信し、応答を返す Web サービスが含まれています。 Azure App Service を使用してこのイメージを展開でき、さらに次の機能が用意されています。

* セキュリティ強化を目的とした[高度な認証](/azure/app-service/configure-authentication-provider-aad)。 認証方法には、Azure Active Directory と多要素認証の両方が含まれます。
* 再デプロイ不要の[自動スケーリング](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)。
* クライアントとサービスの間のセキュリティで保護された通信のための [SSL サポート](/azure/app-service/app-service-web-ssl-cert-load)。

Azure App Service によって提供される機能の詳細については、[App Service の概要](/azure/app-service/overview)を参照してください。

> [!IMPORTANT]
> デプロイしたモデルで使用されるスコアリング データまたはスコアリングの結果を記録する能力が必要な場合は、代わりに Azure Kubernetes Service にデプロイする必要があります。 詳細については、[実稼働環境でのデータの収集](how-to-enable-data-collection.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 詳細については、「[ワークスペースの作成](how-to-manage-workspace.md)を参照してください。
* ワークスペースに登録されているトレーニング済みの機械学習モデル。 モデルがない場合は、[イメージ分類のチュートリアル: モデルのトレーニング](tutorial-train-models-with-aml.md)を使用して、トレーニングと登録を行います。

    > [!IMPORTANT]
    > この記事に含まれるスニペットは、次の変数が設定されていることを前提としています。
    >
    > * `ws` - Azure Machine Learning ワークスペース。
    > * `model` - デプロイされる登録済みのモデル。
    > * `inference_config` - モデルの推論構成。
    >
    > これらの変数の設定の詳細については、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

## <a name="prepare-for-deployment"></a>デプロイの準備をする

デプロイを行う前に、モデルを Web サービスとして実行するために必要なものを定義する必要があります。 次の一覧で、デプロイするために必要な基本項目について説明します。

* __エントリ スクリプト__。 このスクリプトは、要求を受け入れ、モデルを使用してその要求にスコアを付け、その結果を返します。

    > [!IMPORTANT]
    > エントリ スクリプトはモデルに固有のものです。受信要求データの形式、モデルで想定されるデータの形式、およびクライアントに返されるデータの形式を理解しておく必要があります。
    >
    > 要求データがモデルで使用できない形式になっている場合、スクリプトで受け入れ可能な形式に変換することができます。 また、応答をクライアントに返す前に変換することもできます。

    > [!IMPORTANT]
    > Azure Machine Learning SDK には、Web サービスでデータストアまたはデータ セットにアクセスする方法は用意されていません。 デプロイの外部に格納されているデータにアクセスするためにデプロイされたモデルが必要な場合 (Azure Storage アカウントの場合など)、関連する SDK を使用してカスタム コード ソリューションを開発する必要があります。 たとえば、[Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python) です。
    >
    > シナリオに適したもう 1 つの方法として[バッチ予測](how-to-run-batch-predictions.md)があります。これにより、スコアリング時にデータストアにアクセスすることができます。

    エントリ スクリプトの詳細については、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

* **依存関係**。エントリ スクリプトまたはモデルを実行するために必要なヘルパー スクリプトや Python/Conda パッケージなど。

これらのエンティティは、__推論構成__にカプセル化されます。 推論構成では、エントリ スクリプトとその他の依存関係が参照されます。

> [!IMPORTANT]
> Azure App Service で使用するための推論構成を作成する際は、[環境](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py)オブジェクトを使用する必要があります。 次の例で、環境オブジェクトを作成し、推論構成でそれを使用する方法を示します。
>
> ```python
> from azureml.core import Environment
> from azureml.core.environment import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
> ```

環境の詳細については、「[トレーニングとデプロイ用の環境を作成して管理する](how-to-use-environments.md)」を参照してください。

推論構成の詳細については、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

> [!IMPORTANT]
> Azure App Service にデプロイするときに__デプロイ構成__を作成する必要はありません。

## <a name="create-the-image"></a>イメージの作成

Azure App Service にデプロイされる Docker イメージを作成するには、[Model.package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config--generate-dockerfile-false-) を使用します。 次のコード スニペットで、モデルと推論構成から新しいイメージを作成する方法を示します。

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

`show_output=True` の場合、Docker ビルド プロセスの出力が表示されます。 プロセスが完了すると、ワークスペース用の Azure Container Registry 内にイメージが作成されます。

## <a name="deploy-image-as-a-web-app"></a>イメージを Web アプリとしてデプロイする

1. [Azure portal](https://portal.azure.com) で、Azure Machine Learning ワークスペースを選択します。 __[概要]__ セクションで、 __[レジストリ]__ リンクを使用して、ワークスペースの Azure Container Registry にアクセスします。

    [![ワークスペースの概要のスクリーンショット](media/how-to-deploy-app-service/workspace-overview.png)](media/how-to-deploy-app-service/workspace-overview-expanded.png)

2. Azure Container Registry で、 __[リポジトリ]__ を選択し、デプロイする__イメージ名__を選択します。 デプロイするバージョンについて、 __[...]__ エントリを選択し、 __[Web アプリにデプロイ]__ を選択します。

    [![ACR から Web アプリへのデプロイのスクリーンショット](media/how-to-deploy-app-service/deploy-to-web-app.png)](media/how-to-deploy-app-service/deploy-to-web-app-expanded.png)

3. Web アプリを作成するには、[サイト名]、[サブスクリプション]、[リソース グループ] を指定し、[App Service プラン/場所] を選択します。 最後に、 __[作成]__ を選択します。

    ![新しい Web アプリ ダイアログのスクリーンショット](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>Web アプリを使用する

[Azure portal](https://portal.azure.com) で、前のステップで作成した Web アプリを選択します。 __[概要]__ セクションで、 __[URL]__ をコピーします。 この値は サービスの __ベース URL__ です。

[![Web アプリの概要のスクリーンショット](media/how-to-deploy-app-service/web-app-overview.png)](media/how-to-deploy-app-service/web-app-overview-expanded.png)

モデルに要求を渡す Web サービスは、`{baseurl}/score` にあります。 たとえば、「 `https://mywebapp.azurewebsites.net/score` 」のように入力します。 次の Python コードは、URL にデータを送信し、応答を表示する方法を示しています。

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>次の手順

* Web アプリの構成の詳細については、 [App Service on Linux](/azure/app-service/containers/) のドキュメントを参照してください。
* スケーリングの詳細については、「[Azure での自動スケールの概要](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)」を参照してください。
* SSL サポートの詳細については、[Azure App Service の SSL 証明書を使用する](/azure/app-service/app-service-web-ssl-cert-load)ことに関する記事をご覧ください。
* 認証の詳細については、「[Azure Active Directory サインインを使用するために App Service アプリを構成する](/azure/app-service/configure-authentication-provider-aad)」を参照してください。
* [Web サービスとしてデプロイされた ML モデルを使用する](how-to-consume-web-service.md)
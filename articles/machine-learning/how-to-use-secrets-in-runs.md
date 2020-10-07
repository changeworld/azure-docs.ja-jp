---
title: トレーニングの認証シークレット
titleSuffix: Azure Machine Learning
description: ワークスペース キー コンテナ―を使用して、セキュリティで保護された方法でトレーニングの実行にシークレットを渡します
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 89934470dc3bf86bb2843137a2129bff13323ca0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91302079"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>Azure Machine Learning トレーニングの実行で認証資格情報シークレットを使用する


この記事では、トレーニングの実行でシークレットを安全に使用する方法について説明します。 ユーザー名やパスワードなどの認証情報はシークレットです。 たとえば、トレーニング データを問い合わせる目的で外部データベースに接続する場合、リモート実行コンテキストにユーザー名とパスワードを渡す必要があります。 このような値をクリアテキストでトレーニング スクリプトにコーディングすると、シークレットが公開されるので安全ではありません。 

代わりに、Azure Machine Learning ワークスペースには、[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) という名前のリソースが関連付けられています。 この Key Vault を使用して、Azure Machine Learning Python SDK の一連の API を介してシークレットをリモート実行に安全に渡します。

シークレットを使用するための標準的なフローは次のとおりです。
 1. ローカル コンピューターで Azure にログインし、お使いのワークスペースに接続します。
 2. ローカル コンピューターで、ワークスペース キー コンテナーにシークレットを設定します。
 3. リモート実行を送信します。
 4. リモート実行の中で、Key Vault からシークレットを取得して使用します。

## <a name="set-secrets"></a>シークレットを設定する

Azure Machine Learning では、[Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true) クラスにシークレットを設定するためのメソッドが含まれています。 ローカル Python セッションで、最初にワークスペース Key Vault への参照を取得します。その後、[`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true#&preserve-view=trueset-secret-name--value-) メソッドを使用して、名前と値でシークレットを設定します。 __set_secret__ メソッドでは、名前が既に存在する場合は、シークレット値が更新されます。

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

シークレット値をクリアテキストとしてファイルに格納するのは安全ではないため、その値を Python コードには含めないでください。 代わりに、Azure DevOps のビルド シークレットなどの環境変数から、または対話型ユーザー入力からシークレット値を取得してください。

[`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true#&preserve-view=truelist-secrets--) メソッドを利用してシークレット名を一覧表示できます。バッチ バージョンの [set_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true#&preserve-view=trueset-secrets-secrets-batch-) もありますが、これを利用すると、複数シークレットを一度に設定できます。

## <a name="get-secrets"></a>シークレットの取得

ローカル コードでは、[`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-secret-name-) メソッドを使用して、名前でシークレット値を取得できます。

[`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true#&preserve-view=truesubmit-config--tags-none----kwargs-) が送信される実行の場合、[`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) クラスを使用して [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-secret-name-) メソッドを使用します。 送信される実行ではワークスペースが認識されるため、このメソッドでは、ワークスペースのインスタンス化がショートカットされ、シークレット値が直接返されます。

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

書き込みや出力によってシークレット値が公開されることがないように注意してください。

複数のシークレットに一度にアクセスするための [get_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-secrets-secrets-) というバッチ バージョンもあります。

## <a name="next-steps"></a>次のステップ

 * [ノートブックの例を表示する](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Azure Machine Learning のエンタープライズ セキュリティを確認する](concept-enterprise-security.md)

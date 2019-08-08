---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: fef6225812980900ad55944644310433a6105de2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68556870"
---
新しいモデルを作成する場合、新しいモデルを使用する各サービスを手動で更新する必要があります。 Web サービスを更新するには、`update` メソッドを使用します。 次のコードは、SDK を使用して Web サービスのモデルを更新する方法を示しています。

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name=service_name, workspace=ws)

# Update to new model(s)
service.update(models=[new_model])
print(service.state)
print(service.get_logs())
```

ML CLI を使用して Web サービスを更新することもできます。 次の例では、新しいモデルを登録し、この新しいモデルを使用するように Web サービスを更新する方法を示します。

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> この例では、JSON ドキュメントを使用して、登録コマンドから更新コマンドにモデル情報を渡します。
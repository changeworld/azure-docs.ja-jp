---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 04/21/2021
ms.author: gopalv
ms.openlocfilehash: 30237f94d5961d853509e303f50be1e8a42de9b0
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2021
ms.locfileid: "109846096"
---
エントリ スクリプトは、デプロイされた Web サービスに送信されたデータを受け取り、それをモデルに渡します。 次に、クライアントに対するモデルの応答が返されます。 *このスクリプトはこのモデルに固有のものです*。 エントリ スクリプトでは、モデルが受け入れ、返すデータを認識する必要があります。

エントリ スクリプトでは、次の 2 つの処理を実行する必要があります。

1. モデルの読み込み (`init()` という関数を使用)
1. 入力データでのモデルの実行 (`run()` という関数を使用)

最初のデプロイでは、受け取ったデータを出力するダミーのエントリ スクリプトを使用します。

<!-- ```python
import json

def init():
    print('This is init')

def run(data):
    test = json.loads(data)
    print(f'received data {test}')
    return(f'test is {test}')

``` -->

:::code language="python" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/source_dir/echo_score.py":::

このファイルを `source_dir` と いう名前のディレクトリ内に`echo_score.py` として保存します。

たとえば、ユーザーが次のようにモデルを呼び出したとします。

```bash
curl -X POST -d '{"this":"is a test"}' -H "Content-Type: application/json" http://localhost:6789/score
```

次のエラーが返されます。

```bash
"test is {'this': 'is a test'}"
```
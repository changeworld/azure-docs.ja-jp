---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2021
ms.author: larryfr
ms.openlocfilehash: 2bd402b9905772b7a45be5ea433a214b6e5f76cf
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553450"
---
この記事の情報は、[azureml-examples](https://github.com/azure/azureml-examples) リポジトリに含まれているコード サンプルを基にしています。 YAML などのファイルをコピーして貼り付けることなくコマンドをローカルで実行するには、リポジトリを複製し、ディレクトリをリポジトリ内の `cli` ディレクトリに変更します。

```azurecli
git clone https://github.com/Azure/azureml-examples --depth 1
cd azureml-examples
cd cli
```

まだ Azure CLI の既定値を設定していない場合は、既定の設定を保存する必要があります。 サブスクリプション、ワークスペース、およびリソース グループの値が複数回渡されることを防ぐには、以下のコマンドを使用します。 次のパラメーターを、実際の構成の値に置き換えます。

* `<subscription>` は、Azure サブスクリプション ID に置き換えてください。
* `<workspace>` は、ご利用の Azure Machine Learning ワークスペース名に置き換えます。
* `<resource-group>` は、ワークスペースが含まれている Azure リソース グループに置き換えます。
* `<location>` は、ワークスペースが含まれている Azure リージョンに置き換えます。

> [!TIP]
> `az configure -l` コマンドを使用すると、現在の既定値を確認できます。

```azurecli
az account set --subscription <subscription>
az configure --defaults workspace=<workspace> group=<resource-group> location=<location>
```


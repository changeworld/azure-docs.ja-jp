---
title: コンポーネントとは (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning コンポーネントを使用して、機械学習パイプラインをビルドします。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.reviewer: laobri
ms.date: 10/21/2021
ms.topic: conceptual
ms.openlocfilehash: 5846a84df6c11364fed6fa65b852b55c42cd5364
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565782"
---
# <a name="what-is-an-azure-machine-learning-component-preview"></a>Azure Machine Learning コンポーネントとは (プレビュー)

Azure Machine Learning コンポーネント (以前はモジュールと呼ばれていた) は、機械学習パイプラインで 1 つのステップを実行する自己格納型のコードです。 コンポーネントは、高度な機械学習パイプラインの構成要素です ([Azure Machine Learning CLI を使用した機械学習パイプラインの作成と実行](how-to-create-component-pipelines-cli.md)を参照してください)。 コンポーネントは、データ処理、モデル トレーニング、モデル スコアリングなどのタスクを実行できます。

コンポーネントは関数に類似しており、名前とパラメーターを持ち、入力を予想し、出力を返します。 コンポーネントの作成の詳細については、「[コンポーネントを作成する](#define-a-component-preview)」を参照してください。

## <a name="why-should-i-use-a-component-preview"></a>コンポーネントを使用する理由 (プレビュー)

コンポーネントを使用すると、パイプライン間で共通のロジックを管理および再利用できます。

- **構成可能**: コンポーネントを使用すると、開発者は単純なインターフェイスの背後にある複雑なロジックを非表示にできます。 コンポーネントのユーザーは基盤となるロジックを理解する必要はなく、パラメーターを指定するだけで済みます。

- **共有と再利用**: コンポーネントは、同じワークスペース内のユーザーと自動的に共有されます。 パイプライン、環境、ワークスペース、サブスクリプション間でコンポーネントを再利用できます。 組み込みのバージョン追跡を使用することで、変更を追跡し、結果を再現できます。

- **CLI のサポート**: コンポーネントを使用して CLI (v2) でパイプラインを作成します。


## <a name="define-a-component-preview"></a>コンポーネントを定義する (プレビュー)

Azure Machine Learning コンポーネントを定義するには、次の 2 つのファイルを指定する必要があります。

- 有効な [YAML コンポーネント仕様形式](reference-yaml-component-command.md)のコンポーネント仕様。 このファイルによって、次の情報が指定されます。
  - メタデータ: name、display_name、version、type など。
  - インターフェイス: 入力と出力
  - コマンド、コード、および環境: コンポーネントの実行に使用されるコマンド、コード、および環境
- 実際の実行ロジックを指定するスクリプト。

### <a name="component-specification"></a>コンポーネントの仕様

コンポーネント仕様ファイルによって、コンポーネントのメタデータと実行パラメーターが定義されます。 コンポーネント仕様によって、指定した Python スクリプトの実行方法が Azure Machine Learning に指示されます。

トレーニング コンポーネントのコンポーネント仕様の例を以下に示します。

```yaml
name: Example_Train
display_name: Example Train
version: 20
type: command
description: Example of a torchvision training component
tags: {category: Component Tutorial, contact: user@contoso.com}
inputs:
  training_data: 
    type: path
    description: Training data organized in torchvision structure
  max_epochs:
    type: integer
    description: Maximum epochs for training
  learning_rate: 
    type: number
    description: Learning rate, default is 0.01
    default: 0.01
  learning_rate_schedule: 
    type: string
    default: time-based 
outputs:
  model_output:
    type: path
code:
  local_path: ./train_src
environment: azureml:AzureML-Minimal:1
command: >-
  python train.py 
  --training_data ${{inputs.training_data}} 
  --max_epochs ${{inputs.max_epochs}}   
  --learning_rate ${{inputs.learning_rate}} 
  --learning_rate_schedule ${{inputs.learning_rate_schedule}} 
  --model_output ${{outputs.model_output}}
```

次の表で、この例のフィールドについて説明します。 使用可能なフィールドの完全な一覧については、[YAML コンポーネント仕様のリファレンス ページ](reference-yaml-component-command.md)を参照してください。

| 名前                | 種類                                                     | 必須 | 説明                                                  |
| ------------------- | -------------------------------------------------------- | -------- | ------------------------------------------------------------ |
| name                | string                                                   | はい      | コンポーネント名。 コンポーネントの一意の識別子である必要があります。 数字または文字で始め、文字、数字、`_`、および `-` のみを使用する必要があります。 最大文字数は 255 文字です。|
| version             | string                                                   | はい      | コンポーネントのバージョン。 文字列である必要があります。 |
| display_name        | string                                                   | いいえ       | コンポーネントの表示名。 既定値は `name` と同じです。 |
| 型                | string                                                   | いいえ       | コンポーネントの種類です。 現在、この値は `command` である必要があります。|
| description         | string                                                   | いいえ       | コンポーネントの詳細な説明。 |
| tags                | Dictionary&lt;string&gt;                                       | いいえ | コンポーネントのさまざまなパースペクティブを記述するキーと値のペアの一覧。 各タグのキーと値は、1 つの単語または短い語句 (`Product:Office`、`Domain:NLP`、`Scenario:Image Classification` など) である必要があります。 |
| is_deterministic    | boolean                                                  | いいえ       | 同じ入力データが指定された場合に、コンポーネントが常に同じ結果を生成するかどうか。 既定では、 `True`です。 外部リソースからデータを読み込むコンポーネントの場合は、`False` に設定する必要があります。たとえば、データが更新される可能性があるため、特定の URL からデータをインポートする場合などです。 |
| inputs              | Dictionary&lt;string, Input&gt; | いいえ       | コンポーネントの入力ポートとパラメーターを定義します。 文字列キーは入力の名前であり、有効な Python 変数名である必要があります。 |
| outputs             | Dictionary&lt;string, Output&gt;                    | いいえ       | コンポーネントの出力ポートを定義します。 文字列キーは出力の名前であり、有効な Python 変数名である必要があります。 |
| code                | string                                                   | いいえ       | ソース コードへのパス |
| 環境         | 環境                              | いいえ       | コンポーネントを実行するランタイム環境。 |
| command             | string                                             | いいえ    | コンポーネント コードを実行するコマンド。         |

### <a name="python-script"></a>Python スクリプト

Python スクリプトには、コンポーネントの実行可能ロジックが含まれています。 このスクリプトによって、コンポーネントで実行する操作が Azure Machine Learning に指示されます。

実行するには、Python スクリプトの引数と、YAML 仕様に定義した引数を一致させる必要があります。 前のセクションの YAML 仕様と一致する Python トレーニング スクリプトの例を以下に示します。

```python
## Required imports 
import argparse
import os
## Import other dependencies your script needs
from pathlib import Path
from uuid import uuid4
from datetime import datetime

## Define an argument parser that matches the arguments from the components specification file
parser = argparse.ArgumentParser("train")
parser.add_argument("--training_data", type=str, help="Path to training data")
parser.add_argument("--max_epochs", type=int, help="Max # of epochs for the training")
parser.add_argument("--learning_rate", type=float, help="Learning rate")
parser.add_argument("--learning_rate_schedule", type=str, help="Learning rate schedule")
parser.add_argument("--model_output", type=str, help="Path of output model")

args = parser.parse_args()

## Implement your custom logic (in this case a training script)
print ("hello training world...")

lines = [
    f'Training data path: {args.training_data}',
    f'Max epochs: {args.max_epochs}',
    f'Learning rate: {args.learning_rate}',
    f'Learning rate: {args.learning_rate_schedule}',
    f'Model output path: {args.model_output}',
]

for line in lines:
    print(line)

print("mounted_path files: ")
arr = os.listdir(args.training_data)
print(arr)

for filename in arr:
    print ("reading file: %s ..." % filename)
    with open(os.path.join(args.training_data, filename), 'r') as handle:
        print (handle.read())

## Do the train and save the trained model as a file into the output folder.
## Here only output a dummy data for example.
curtime = datetime.now().strftime("%b-%d-%Y %H:%M:%S")
model = f"This is a dummy model with id: {str(uuid4())} generated at: {curtime}\n"
(Path(args.model_output) / 'model.txt').write_text(model)

```

:::image type="content" source="media/concept-component/component-introduction.png" lightbox="media/concept-component/component-introduction.png" alt-text="ソース コード要素とコンポーネント UI 間のマッピングを示す概念ドキュメント。" :::

## <a name="create-a-component"></a>コンポーネントを作成する

### <a name="create-a-component-using-cli-v2"></a>CLI (v2) を使用してコンポーネントを作成する

コンポーネント仕様と Python スクリプト ファイルを定義し、[CLI (v2) を正常にインストール](how-to-configure-cli.md)したら、次を使用してワークスペースにコンポーネントを作成できます。

```azurecli
az ml component create --file my_component.yml --version 1 --resource-group my-resource-group --workspace-name my-workspace
```

`create` コマンドの詳細については、`az ml component create --help` を使用してください。

### <a name="create-a-component-in-the-studio-ui"></a>スタジオ UI でコンポーネントを作成する

スタジオ UI の **[コンポーネント]** ページでコンポーネントを作成できます。

1. [コンポーネント] ページで **[新しいコンポーネント]** をクリックします。

    :::image type="content" source="./media/concept-component/ui-create-component.png" lightbox="./media/concept-component/ui-create-component.png" alt-text="新しいコンポーネント ボタンを示すスクリーンショット。":::

1. ウィザードに従って、作成プロセスを完了します。

## <a name="use-components-to-build-ml-pipelines"></a>コンポーネントを使用して ML パイプラインをビルドする

Azure CLI (v2) を使用して、パイプライン ジョブを作成できます。 [ML パイプラインを作成して実行する (CLI)](how-to-create-component-pipelines-cli.md) を参照してください。

## <a name="manage-components"></a>コンポーネントを管理する

CLI (v2) を使用して、コンポーネントの詳細を確認し、コンポーネントを管理できます。 コンポーネント コマンドの詳細な説明を取得するには、`az ml component -h` を使用します。

### <a name="list-components"></a>コンポーネントを一覧表示する

`az ml component list` を使用して、ワークスペースにすべてのコンポーネントを一覧表示できます。

作成したすべてのコンポーネントを、スタジオ UI の **[コンポーネント]** ページのワークスペースに表示できます。

### <a name="show-details-for-a-component"></a>コンポーネントの詳細を表示する

`az ml component show --name <COMPONENT_NAME>` を使用して、コンポーネントの詳細を表示できます。

また、Studio UI の **[コンポーネント]** ページでコンポーネントの詳細を確認することもできます。


### <a name="upgrade-a-component"></a>コンポーネントをアップグレードする

`az ml component create --file <NEW_VERSION.yaml>` を使用して、コンポーネントをアップグレードできます。

[コンポーネントの詳細] ページで **[アップグレード]** をクリックし、コンポーネントの新しいバージョンにアップグレードすることもできます。

:::image type="content" source="./media/concept-component/upgrade-component.png" lightbox="./media/concept-component/upgrade-component.png" alt-text="[アップグレード] ボタンを示すスクリーンショット。":::

### <a name="delete-a-component"></a>コンポーネントの削除

`az ml component delete --name <COMPONENT_NAME>` を使用して、コンポーネントを削除できます。 

コンポーネントを選択して、アーカイブすることもできます。

:::image type="content" source="./media/concept-component/archive-component.png" alt-text="コンポーネントのアーカイブ オプションを示すスクリーンショット。":::

## <a name="next-steps"></a>次の手順

- [コンポーネント YAML リファレンス](reference-yaml-component-command.md)
- [ML パイプラインを作成して実行する (CLI)](how-to-create-component-pipelines-cli.md)
- [デザイナーで機械学習パイプラインを構築する](tutorial-designer-automobile-price-train-score.md)

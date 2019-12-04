---
title: Azure Functions で Python と TensorFlow を使用して機械学習の推論を行う
description: このチュートリアルでは、Azure Functions での TensorFlow 機械学習モデルの適用方法について説明します
author: anthonychu
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: e7c4e1bbd23d43d5e11ab8b26c3d4e1215b4946b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230501"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>チュートリアル:Python および TensorFlow を使用して Azure Functions で機械学習モデルを適用する

この記事では、Azure Functions で、Python と TensorFlow を機械学習モデルと共に使用して、その内容に基づいて画像を分類する方法について説明します。

このチュートリアルで学習する内容は次のとおりです。 

> [!div class="checklist"]
> * Python で Azure Functions を開発するためにローカル環境を初期化する
> * カスタムの TensorFlow 機械学習モデルを関数アプリにインポートする
> * 写真に犬または猫が含まれているかどうかを予測するサーバーレス HTTP API をビルドする
> * Web アプリケーションから API を使用する

![完成したプロジェクトのスクリーンショット](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件 

Python で Azure Functions を作成するには、いくつかのツールをインストールする必要があります。

- [Python 3.6](https://www.python.org/downloads/release/python-360/)
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- コード エディター ([Visual Studio Code](https://code.visualstudio.com/) など)

## <a name="clone-the-tutorial-repository"></a>チュートリアル リポジトリをクローンする

開始するには、ターミナルを開き、Git を使用して次のリポジトリをクローンします。

```console
git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
cd functions-python-tensorflow-tutorial
```

リポジトリには、いくつかのフォルダーが含まれています。

- *start*:これはチュートリアル用の作業フォルダーです
- *end*:これは参照の最終結果と完全な実装です
- *resources*: 機械学習モデルとヘルパー ライブラリが含まれています
- *frontend*:関数アプリを呼び出す Web サイト

## <a name="create-and-activate-a-python-virtual-environment"></a>Python 仮想環境を作成してアクティブ化する

Azure Functions には Python 3.6. x が必要です。 必要な Python バージョンを確実に使用するように、仮想環境を作成します。

現在の作業ディレクトリを *start* フォルダーに変更します。 次に、 *.venv* という名前の仮想環境を作成してアクティブ化します。 Python のインストールによっては、Python 3.6 仮想環境を作成するためのコマンドが、次の手順と異なる場合があります。

#### <a name="linux-and-macos"></a>Linux および macOS:

```bash
cd start
python3.6 -m venv .venv
source .venv/bin/activate
```

#### <a name="windows"></a>Windows:

```powershell
cd start
py -3.6 -m venv .venv
.venv\scripts\activate
```

ターミナル プロンプトの先頭に、仮想環境が正常にアクティブ化されたことを示す `(.venv)` が付加されるようになりました。 仮想環境で `python` が実際に Python 3.6. x であることを確認します。

```console
python --version
```

> [!NOTE]
> このチュートリアルの残りの部分では、仮想環境でコマンドを実行します。 ターミナルで仮想環境を再アクティブ化する必要がある場合は、お使いのオペレーティング システムに対して適切なアクティブ化コマンドを実行します。

## <a name="create-an-azure-functions-project"></a>Azure Functions プロジェクトを作成する

*start* フォルダーで、Azure Functions Core Tools を使用して Python 関数アプリを初期化します。

```console
func init --worker-runtime python
```

関数アプリには、1 つ以上の Azure Functions を含めることができます。 エディターで *start* フォルダーを開き、内容を確認します。

- [*local.settings.json*](functions-run-local.md#local-settings-file):ローカル開発に使用されるアプリケーション設定が含まれています
- [*host.json*](functions-host-json.md):Azure Functions のホストと拡張機能の設定が含まれています
- [*requirements.txt*](functions-reference-python.md#package-management):このアプリケーションに必要な Python パッケージが含まれています

## <a name="create-an-http-function"></a>HTTP 関数を作成する

アプリケーションには、画像 URL を入力として受け取り、画像に犬または猫が含まれているかどうかの予測を返す、単一の HTTP API エンドポイントが必要です。

ターミナルで、Azure Functions Core Tools を使用して、*classify* という名前の新しい HTTP 関数をスキャフォールディングします。

```console
func new --language python --template HttpTrigger --name classify
```

*classify* という名前の新しいフォルダーが作成され、2 つのファイルが含まれます。

- *\_\_init\_\_.py*:main 関数のファイル
- *function.json*:関数のトリガーとその入力バインドと出力バインドを記述するファイル

### <a name="run-the-function"></a>関数の実行

Python 仮想環境がアクティブになっているターミナルで、関数アプリを起動します。

```console
func start
```

ブラウザーを開き、次の URL に移動します。 関数が実行されて *Hello Azure!* を返す必要があります

```
http://localhost:7071/api/classify?name=Azure
```

関数アプリを停止するには、`Ctrl-C` を使用します。

## <a name="import-the-tensorflow-model"></a>TensorFlow モデルをインポートする

Azure Custom Vision Service でトレーニングされ、エクスポートされた、事前構築済みの TensorFlow モデルを使用します。

> [!NOTE]
> Custom Vision Service の Free レベルを使用して独自のビルドを作成する場合は、[サンプル プロジェクト リポジトリ](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md)の指示に従ってください。

このモデルは、 *<REPOSITORY_ROOT>/resources/model* フォルダー内の 2 つのファイルで構成されています: *model.pb* および *labels.txt*。 これらを *classify* 関数のフォルダーにコピーします。

#### <a name="linux-and-macos"></a>Linux および macOS:

```bash
cp ../resources/model/* classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\model\* classify
```

上記のコマンドには、必ず \* を含めてください。 *classify* に *model.pb* と *labels.txt* という名前のファイルが含まれていることを確認します。

## <a name="add-the-helper-functions-and-dependencies"></a>ヘルパー関数と依存関係を追加する

入力画像を準備し、TensorFlow を使用して予測を行うためのいくつかのヘルパー関数が、*resources* フォルダー内の *predict.py* という名前のファイルに含まれています。 このファイルを *classify* 関数のフォルダーにコピーします。

#### <a name="linux-and-macos"></a>Linux および macOS:

```bash
cp ../resources/predict.py classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\predict.py classify
```

*classify* に *predict.py* という名前のファイルが含まれていることを確認します。

### <a name="install-dependencies"></a>依存関係をインストールする

ヘルパー ライブラリには、インストールされる必要があるいくつかの依存関係があります。 エディターで *start/requirements.txt* を開き、次の依存関係をファイルに追加します。

```txt
tensorflow==1.14
Pillow
requests
```

ファイルを保存します。

仮想環境がアクティブになっているターミナルで、*start* フォルダーにある次のコマンドを実行して、依存関係をインストールします。 一部のインストール手順は、完了までに数分かかる場合があります。

```console
pip install --no-cache-dir -r requirements.txt
```

### <a name="caching-the-model-in-global-variables"></a>グローバル変数でモデルをキャッシュする

エディターで *predict.py* を開き、ファイルの先頭付近にある `_initialize` 関数を確認します。 TensorFlow モデルは、最初に関数が実行され、グローバル変数に保存されるときに、ディスクから読み込まれることに注意してください。 `_initialize` 関数の後続の実行では、ディスクからの読み込みはスキップされます。 この手法でモデルをメモリにキャッシュすると、その後の予測が高速化されます。

グローバル変数の詳細については、[Azure Functions の Python 開発者向けガイド](functions-reference-python.md#global-variables)を参照してください。

## <a name="update-function-to-run-predictions"></a>予測を実行するように関数を更新する

エディターで *classify/\_\_init\_\_.py* を開きます。 追加した *predict* ライブラリを前と同じフォルダーにインポートします。 ファイルに既に存在する他のインポートの下に、次の `import` ステートメントを追加します。

```python
import json
from .predict import predict_image_from_url
```

関数テンプレート コードを以下の内容に置き換えます。

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    image_url = req.params.get('img')
    results = predict_image_from_url(image_url)

    headers = {
        "Content-type": "application/json",
        "Access-Control-Allow-Origin": "*"
    }
    return func.HttpResponse(json.dumps(results), headers = headers)
```

必ず変更内容を保存してください。

この関数は、`img` という名前のクエリ文字列パラメーターで画像 URL を受け取ります。 これは画像をダウンロードするヘルパー ライブラリから `predict_image_from_url` を呼び出し、TensorFlow モデルを使用して予測を返します。 次に、この関数は、結果と共に HTTP 応答を返します。

HTTP エンドポイントは、別のドメインでホストされている Web ページによって呼び出されるため、HTTP 応答には、ブラウザーのクロス オリジン リソース共有 (CORS) 要件を満たすための `Access-Control-Allow-Origin` ヘッダーが含まれます。

> [!NOTE]
> 実稼働アプリケーションでは、セキュリティを強化するために、`*` を Web ページの特定のオリジンに変更します。

### <a name="run-the-function-app"></a>関数アプリを実行する

Python 仮想環境がまだアクティブになっていることを確認し、次のコマンドを使用して関数アプリを起動します。

```console
func start
```

ブラウザーで、猫の写真の URL を使用して関数を呼び出す、この URL を開きます。 有効な予測結果が返されることを確認します。

```
http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
```

関数アプリを実行したままにします。

### <a name="run-the-web-app"></a>Web アプリの実行

*frontend* フォルダーには、関数アプリで HTTP API を使用する単純な Web アプリがあります。

*別の*ターミナルを開き、*frontend* フォルダーに変更します。 Python 3.6 で HTTP サーバーを起動します。

#### <a name="linux-and-macos"></a>Linux および macOS:

```bash
cd <FRONT_END_FOLDER>
python3.6 -m http.server
```

#### <a name="windows"></a>Windows:

```powershell
cd <FRONT_END_FOLDER>
py -3.6  -m http.server
```

ブラウザーで、ターミナルに表示されている HTTP サーバーの URL に移動します。 Web アプリが表示されます。 次のいずれかの写真 URL をテキストボックスに入力します。 パブリックにアクセス可能な猫または犬の写真の URL を使用することもできます。

- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`

[送信] をクリックすると、関数アプリが呼び出され、結果がページに表示されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ
このチュートリアルの全体は、お使いのコンピューター上でローカルに実行されるため、クリーンアップする Azure リソースやサービスはありません。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Functions を使用して HTTP API をビルドしてカスタマイズし、TensorFlow モデルを使用して予測を行う方法について学習しました。 また、Web アプリケーションから API を呼び出す方法についても学習しました。

このチュートリアルの手法を使用することで、Azure Functions のサーバーレス コンピューティング モデルで API を実行しながら、複雑な API もビルドできます。

関数アプリを Azure にデプロイするには、[Azure Functions Core Tools](./functions-run-local.md#publish) または [Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions) を使用します。

> [!div class="nextstepaction"]
> [Azure Functions の Python 開発者向けガイド](./functions-reference-python.md)

---
title: Azure Machine Learning 推論 HTTP サーバー
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 推論 HTTP サーバーを使用して、ローカル開発を有効にする方法について説明します。
author: shivanissambare
ms.author: ssambare
ms.reviewer: larryfr
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: inference server, local development, local debugging, devplatv2
ms.date: 05/14/2021
ms.openlocfilehash: 48dda26415113bff4ff20305ee8779804859be33
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123428310"
---
# <a name="azure-machine-learning-inference-http-server-preview"></a>Azure Machine Learning 推論 HTTP サーバー (プレビュー)

Azure Machine Learning 推論 HTTP サーバー [(プレビュー)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) は、ローカルの開発環境で入力スクリプト (`score.py`) を簡単に検証できる Python のパッケージです。 スコアリング スクリプトに問題がある場合、サーバーからエラーが返されます。 また、エラーが発生した場所も返されます。

サーバーは、継続的インテグレーションとデプロイ パイプラインで検証ゲートを作成するときにも使用できます。 たとえば、候補のスクリプトでサーバーを起動し、ローカル エンドポイントに対してテスト スイートを実行します。

## <a name="prerequisites"></a>前提条件

- 必要: Python >= 3.7

## <a name="installation"></a>インストール

> [!NOTE]
> パッケージの競合を回避するには、仮想環境にサーバーをインストールします。

`azureml-inference-server-http package` をインストールするには、cmd/terminal で次のコマンドを実行します。

```bash
python -m pip install azureml-inference-server-http
```

## <a name="use-the-server"></a>サーバーを使用する

1. ファイルを保持するディレクトリを作成します。

    ```bash
    mkdir server_quickstart
    cd server_quickstart
    ```

1. パッケージの競合を回避するには、仮想環境を作成してアクティブにします。

    ```bash
    virtualenv myenv
    source myenv/bin/activate
    ```

1. [Pypi](https://pypi.org/project/azureml-inference-server-http/) フィードから `azureml-inference-server-http` パッケージをインストールします。

    ```bash
    python -m pip install azureml-inference-server-http
    ```

1. エントリ スクリプトを作成します (`score.py`)。 次の例では、基本的なエントリ スクリプトを作成します。

    ```bash
    echo '
    import time

    def init():
        time.sleep(1)

    def run(input_data):
        return {"message":"Hello, World!"}
    ' > score.py
    ```

1. サーバーを起動し、エントリ スクリプトとして `score.py` を設定します。

    ```bash
    azmlinfsrv --entry_script score.py
    ```

    > [!NOTE]
    > サーバーは 0.0.0.0 でホストされます。つまり、ホスト コンピューターのすべての IP アドレスをリッスンします。

1. `curl` を使用して、スコアリング要求をサーバーに送信します。

    ```bash
    curl -p 127.0.0.1:5001/score
    ```

    サーバーでは、このように応答する必要があります。

    ```bash
    {"message": "Hello, World!"}
    ```

これで、サーバーを再度実行して、スコアリング スクリプトを変更し、変更をテストできます。

## <a name="server-routes"></a>サーバー ルート

サーバーは、これらのルートでポート 5001 でリッスンしています。

| Name | ルート|
| --- | --- |
| Liveness Probe | 127.0.0.1:5001/|
| Score | 127.0.0.1:5001/score|

## <a name="server-parameters"></a>サーバー パラメーター

次の表に、サーバーで受け入れられるパラメーターを示します。

| パラメーター | 必須 | Default | 説明 |
| ---- | --- | ---- | ----|
| entry_script | True | 該当なし | スコアリング スクリプトへの相対または絶対パス。|
| model_dir | × | 該当なし | 推論に使用されるモデルを保持するディレクトリへの相対または絶対パス。
| port | False | 5001 | サーバーのサービス ポート。|
| worker_count | False | 1 | 同時要求を処理するワーカー スレッドの数。 |
| appinsights_instrumentation_key | × | 該当なし | ログが発行されるアプリケーション分析情報へのインストルメンテーション キー。 |

## <a name="request-flow"></a>要求フロー

次の手順では、Azure Machine Learning 推論 HTTP サーバーで受信要求を処理する方法について説明します。

1. Python CLI ラッパーは、サーバーのネットワーク スタックの周囲に置かれ、サーバーを起動するために使用されます。
1. クライアントからサーバーに要求が送信されます。
1. 要求は受信されると、[WSGI](https://www.fullstackpython.com/wsgi-servers.html) サーバーを経由し、いずれかのワーカーにディスパッチされます。
    - [Gunicorn](https://docs.gunicorn.org/) は __Linux__ で使用されます。
    - [Waitress](https://docs.pylonsproject.org/projects/waitress/) は __Windows__ で使用されます。
1. 次に、要求は [Flask](https://flask.palletsprojects.com/) アプリによって処理されます。これにより、エントリ スクリプトとすべての依存関係を読み込みます。
1. 最後に、要求は入力スクリプトに送信されます。 次に、エントリ スクリプトは、読み込まれたモデルへの推論の呼び出しを行い、応答を返します。

:::image type="content" source="./media/how-to-inference-server-http/inference-server-architecture.png" alt-text="HTTP サーバー プロセスの図":::

## <a name="how-to-integrate-with-visual-studio-code"></a>Visual Studio Code と統合する方法

Visual Studio Code (VSCode) と [Python Extension](https://marketplace.visualstudio.com/items?itemName=ms-python.python) を使用し、[azureml-inference-server-http](https://pypi.org/project/azureml-inference-server-http/) パッケージを使用してデバッグするには 2 つの方法があります。 

1. コマンドラインで AzureML Inference Server を起動し、VSCode + Python Extension を使用してプロセスにアタッチします。
1. VSCode で `launch.json` を設定し、VSCode 内で AzureML Inference Server を起動します。

どちらの方法でも、ブレークポイントを設定し、ステップごとにデバッグすることができます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="do-i-need-to-reload-the-server-when-changing-the-score-script"></a>スコア スクリプトを変更するときに、サーバーをリロードする必要がありますか。

スコアリング スクリプト (`score.py`) を変更した後、`ctrl + c` でサーバーを停止します。 次に、`azmlinfsrv --entry_script score.py` を使用して再起動します。

### <a name="which-os-is-supported"></a>どの OS をサポートしていますか。

Azure Machine Learning 推論サーバーは、Windows と Linux をベースとするオペレーティングシステム上で動作します。

## <a name="next-steps"></a>次のステップ

* 入力スクリプトの作成とモデルのデプロイの詳細については、「[Azure Machine Learning を使用してモデルをデプロイする方法](how-to-deploy-and-where.md)」に関するページを参照してください。
* [推論用の事前構築済み Docker イメージ](concept-prebuilt-docker-images-inference.md)について説明します
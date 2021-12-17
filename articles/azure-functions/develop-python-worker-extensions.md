---
title: Azure Functions 用の Python worker 拡張機能の開発
description: Azure で実行されている Python 関数にミドルウェアの動作を挿入できるようにする worker 拡張機能を作成して公開する方法について説明します。
ms.topic: how-to
ms.date: 6/1/2021
ms.custom: devx-track-python
ms.openlocfilehash: c5ee4ec3a8bd6fd6994962ab35453b34cb7fda8b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124764282"
---
# <a name="develop-python-worker-extensions-for-azure-functions"></a>Azure Functions 用の Python worker 拡張機能の開発

Azure Functions を使用すると、Python 関数の実行の一部としてカスタム動作を統合できます。 この機能により、お客様に各自の関数アプリで簡単に使用してもらえるビジネス ロジックを作成することができます。 詳細については、[Python 開発者向けリファレンス](functions-reference-python.md#python-worker-extensions)を参照してください。

このチュートリアルで学習する内容は次のとおりです。 
> [!div class="checklist"]
> * Azure Functions 用のアプリケーションレベルの Python worker 拡張機能を作成します。 
> * お客様と同じようにアプリで拡張機能を使用します。 
> * 拡張機能を使用できるようにパッケージ化して公開します。 

## <a name="prerequisites"></a>前提条件

開始する前に、次の要件を満たす必要があります。

* [Python 3.6.x 以上](https://www.python.org/downloads/release/python-374/)。 Azure Functions でサポートされている Python バージョンの完全な一覧を確認するには、[Python 開発者ガイド](functions-reference-python.md#python-version)を参照してください。

* [Azure Functions Core Tools](functions-run-local.md#v2) バージョン 3.0.3568 以降。

* [サポートされているプラットフォーム](https://code.visualstudio.com/docs/supporting/requirements#_platforms)のいずれかにインストールされた [Visual Studio Code](https://code.visualstudio.com/)。

## <a name="create-the-python-worker-extension"></a>Python worker 拡張機能を作成する

作成する拡張機能は、HTTP トリガー呼び出しの経過時間をコンソール ログと HTTP 応答本文で報告するものです。

### <a name="folder-structure"></a>フォルダー構造

この拡張機能プロジェクトのフォルダー構造は、次のようになります。

```
<python_worker_extension_root>/
 | - .venv/
 | - python_worker_extension_timer/
 | | - __init__.py
 | - setup.py
 | - readme.md
```

| フォルダー/ファイル | 説明 |
| --- | --- |
| **.venv/** | (省略可能) ローカル開発に使用される Python 仮想環境が含まれます。 |
| **python_worker_extension/** | Python worker 拡張機能のソース コードが含まれます。 このフォルダーには、PyPI に公開されるメインの Python モジュールが含まれます。 |
| **setup.py** | Python worker 拡張機能パッケージのメタデータが含まれます。 |
| **readme.md** | (省略可能) 拡張機能の説明と使用法が含まれます。 このコンテンツは、PyPI プロジェクトのホーム ページに説明として表示されます。 |

### <a name="configure-project-metadata"></a>プロジェクト メタデータを構成する

まず `setup.py` を作成します。これはパッケージに関する重要な情報を提供するものです。 拡張機能が適切に配布されてお客様の関数アプリに統合されるようにするために、`'azure-functions >= 1.7.0, < 2.0.0'` が `install_requires` セクションに含まれていることを確認してください。

次のテンプレートでは、必要に応じて `author`、`author_email`、`install_requires`、`license`、`packages`、`url` の各フィールドを変更する必要があります。

:::code language="python" source="~/azure-functions-python-worker-extension/setup.py":::

次に、アプリケーションレベルのスコープに拡張機能コードを実装します。

### <a name="implement-the-timer-extension"></a>タイマー拡張機能を実装する

アプリケーションレベルの拡張機能を実装するために、`python_worker_extension_timer/__init__.py` に次のコードを追加します。

:::code language="python" source="~/azure-functions-python-worker-extension/python_worker_extension_timer/__init__.py":::

このコードは [AppExtensionBase](https://github.com/Azure/azure-functions-python-library/blob/dev/azure/functions/extension/app_extension_base.py) から継承され、アプリ内のすべての関数に拡張機能が適用されるようになります。 また、[FuncExtensionBase](https://github.com/Azure/azure-functions-python-library/blob/dev/azure/functions/extension/func_extension_base.py) から継承することによって、関数レベルのスコープに拡張機能を実装することもできます。

`init` メソッドは、拡張クラスがインポートされるときに worker によって呼び出されるクラス メソッドです。 ここで拡張機能の初期化操作を行うことができます。 この場合、各関数の呼び出し開始時刻を記録するために、ハッシュ マップが初期化されます。

`configure` メソッドはお客様向けです。 readme ファイルで、どのような場合に `Extension.configure()` を呼び出す必要があるかをお客様に知らせることができます。 また、拡張機能でできること、可能な構成、拡張機能の使用法についても readme で説明します。 この例では、`HttpResponse` で経過時間を報告するかどうかをお客様が選択できます。

`pre_invocation_app_level` メソッドは、関数が実行される前に Python worker によって呼び出されます。 それにより、関数のコンテキストや引数など、関数からの情報が提供されます。 この例では、拡張機能によってメッセージがログに記録され、その invocation_id に基づいて呼び出しの開始時刻が記録されます。

同様に、`post_invocation_app_level` が関数の実行後に呼び出されます。 この例では、開始時刻と現在の時刻に基づいて経過時間を計算します。 また、HTTP 応答の戻り値を上書きします。

## <a name="consume-your-extension-locally"></a>拡張機能をローカルで使用する

拡張機能を作成したので、アプリ プロジェクトでそれを使用して、意図したとおりに動作することを確認できます。 

### <a name="create-an-http-trigger-function"></a>HTTP トリガー関数の作成

1. アプリ プロジェクト用の新しいフォルダーを作成し、そこに移動します。 

1. Bash などの適切なシェルから、次のコマンドを実行してプロジェクトを初期化します。

    ```bash
    func init --python
    ```

1. 次のコマンドを使用して、匿名アクセスができる新しい HTTP トリガー関数を作成します。

    ```bash
    func new -t HttpTrigger -n HttpTrigger -a anonymous
    ```

### <a name="activate-a-virtual-environment"></a>仮想環境をアクティブ化する

1. OS に応じて次のように Python 仮想環境を作成します。

    # <a name="linux"></a>[Linux](#tab/linux)
    ```bash
    python3 -m venv .venv
    ```
    # <a name="windows"></a>[Windows](#tab/windows)
    ```console
    py -m venv .venv
    ``` 
    ---

1. OS に応じて次のように Python 仮想環境をアクティブ化します。
    # <a name="linux"></a>[Linux](#tab/linux)
    ```bash
    source .venv/bin/activate
    ```
    # <a name="windows"></a>[Windows](#tab/windows)
    ```console
    .venv\Scripts\Activate.ps1
    ``` 
    ---

### <a name="configure-the-extension"></a>拡張機能を構成する

1. 次のコマンドを使用して、関数アプリ プロジェクトのリモート パッケージをインストールします。 
    
    ```bash
    pip install -r requirements.txt
    ```

1. 次のように、編集可能モードでローカル ファイル パスから拡張機能をインストールします。

    ```bash
    pip install -e <PYTHON_WORKER_EXTENSION_ROOT>
    ```

    この例では、`<PYTHON_WORKER_EXTENSION_ROOT>` を拡張機能プロジェクトのファイルの場所に置き換えます。   
    お客様がこの拡張機能を使用するときは、そうではなく、次の例のように拡張機能パッケージの場所を requirements.txt ファイルに追加します。

    # <a name="pypi"></a>[PyPI](#tab/pypi)
    ```python
    # requirements.txt
    python_worker_extension_timer==1.0.0
    ``` 
    # <a name="github"></a>[GitHub](#tab/github)
    
    ```python
    # requirements.txt
    git+https://github.com/Azure-Samples/python-worker-extension-timer@main
    ```
    ---

1. プロジェクト ファイルの local.settings.json を開き、次のフィールドを `Values` に追加します。

    ```json
    "PYTHON_ENABLE_WORKER_EXTENSIONS": "1" 
    ```

    Azure で実行するときは、そうではなく、`PYTHON_ENABLE_WORKER_EXTENSIONS=1` を[関数アプリのアプリ設定](functions-how-to-use-azure-function-app-settings.md#settings)に追加します。

1. \_\_init.py\_\_ の `main` 関数の前に、次の 2 行を追加します。

    ```python
    from python_worker_extension_timer import TimerExtension
    TimerExtension.configure(append_to_http_response=True)
    ```

    このコードで `TimerExtension` モジュールをインポートし、`append_to_http_response` 構成の値を設定します。

### <a name="verify-the-extension"></a>拡張機能を確認する

1. アプリ プロジェクトのルート フォルダーから、`func host start --verbose` を使用して関数ホストを起動します。 出力に関数のローカル エンドポイントが `https://localhost:7071/api/HttpTrigger` として表示されます。

1. ブラウザーで、GET 要求を `https://localhost:7071/api/HttpTrigger` に送信します。 次のような応答が表示され、要求の **TimeElapsed** データが追加されています。 

    <pre>
    This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response. (TimeElapsed: 0.0009996891021728516 sec)
    </pre>

## <a name="publish-your-extension"></a>拡張機能の公開

拡張機能を作成して確認した後、引き続き残りの公開タスクを完了する必要があります。

> [!div class="checklist"]
> + ライセンスを選択します。
> + readme.md などのその他のドキュメントを作成します。 
> + 拡張ライブラリを Python パッケージ レジストリまたはバージョン管理システム (VCS) に公開します。

# <a name="pypi"></a>[PyPI](#tab/pypi)

拡張機能を PyPI に公開するには:

1. 次のコマンドを実行して `twine` と `wheel` を既定の Python 環境または仮想環境にインストールします。

    ```bash
    pip install twine wheel
    ```

1. 拡張機能リポジトリから古い `dist/` フォルダーを削除します。

1. 次のコマンドを実行して、`dist/` 内に新しいパッケージを生成します。

    ```bash
    python setup.py sdist bdist_wheel
    ```

1. 次のコマンドを実行して、パッケージを PyPI にアップロードします。

    ```bash
    twine upload dist/*
    ```

    アップロード時に、PyPI アカウントの資格情報の入力が必要になる場合があります。

これらの手順の後、お客様は requirements.txt にパッケージ名を含めることによって拡張機能を使用できます。

詳細については、[Python のパッケージ化に関する公式チュートリアル](https://packaging.python.org/tutorials/packaging-projects/)を参照してください。

# <a name="github"></a>[GitHub](#tab/github)

[このサンプル リポジトリ](https://github.com/Azure-Samples/python-worker-extension-timer)に示されているように、拡張機能のソース コードを setup.py ファイルと共に GitHub リポジトリに公開することもできます。 

pip での VCS サポートの詳細については、[pip の VCS サポートに関する公式ドキュメント](https://pip.pypa.io/en/stable/cli/pip_install/#vcs-support)を参照してください。

---

## <a name="examples"></a>例

+ この記事の完成したサンプル拡張機能プロジェクトは、[python_worker_extension_timer](https://github.com/Azure-Samples/python-worker-extension-timer) サンプル リポジトリで確認できます。 

+ OpenCensus 統合は、拡張インターフェイスを使用して Azure Functions Python アプリにテレメトリ トレースを統合するオープンソース プロジェクトです。 この Python worker 拡張機能の実装を確認するには、[opencensus-python-extensions-azure](https://github.com/census-ecosystem/opencensus-python-extensions-azure/tree/main/extensions/functions) リポジトリを参照してください。

## <a name="next-steps"></a>次のステップ

Python による Azure Functions 開発の詳細については、次のリソースを参照してください。

* [Azure Functions の Python 開発者向けガイド](functions-reference-python.md)
* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions 開発者向けリファレンス](functions-reference.md)

---
title: Python と Visual Studio Code で Azure Functions を作成し、デプロイする
description: Azure Functions 用の Visual Studio Code 拡張機能を使用し、Python でサーバーレス関数を作成し、Azure にデプロイする方法。
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: glenga
ms.openlocfilehash: f5591a3e0ca73649b1ffc51c75aa95e86e286768
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639092"
---
# <a name="deploy-python-to-azure-functions-with-visual-studio-code"></a>Visual Studio Code を使用して Azure Functions に Python をデプロイする

このチュートリアルでは、Visual Studio Code と Azure Functions 拡張を使用し、Python でサーバーレス HTTP エンドポイントを作成し、さらに接続 (または "バインド") をストレージに追加します。 Azure Functions では、サーバーレス環境でコードが実行されますが、仮想マシンをプロビジョニングしたり、Web アプリを公開したりする必要がありません。 Visual Studio Code 用の Azure Functions 拡張により、さまざまな構成問題が自動的に処理され、Functions の使用プロセスが大幅に簡素化されます。

このチュートリアルのいずれかの手順で問題が発生した場合は、詳細をお知らせください。 詳しいフィードバックを送信するには、各セクションの終わりにある **[問題が発生しました]** ボタンを使用します。

## <a name="prerequisites"></a>前提条件

- [Azure サブスクリプション](#azure-subscription)。
- [Visual Studio Code、Python、Azure Functions](#visual-studio-code-python-and-the-azure-functions-extension) 拡張機能。
- [Azure Functions Core Tools](#azure-functions-core-tools)。

### <a name="azure-subscription"></a>Azure サブスクリプション

Azure サブスクリプションをお持ちでない場合は、30 日間の無料アカウントに[今すぐご登録](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)いただけます。Azure クレジットの 200 ドルを使用してさまざまな組み合わせのサービスをお試しください。

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code、Python、Azure Functions 拡張機能

次のソフトウェアをインストールします。

- Azure Functions で必要な Python 3.6.x。 [Python 3.6.8](https://www.python.org/downloads/release/python-368/) は最新の 3.6.x バージョンです。
- [Visual Studio Code](https://code.visualstudio.com/)。
- [Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。[Visual Studio Code Python チュートリアルの前提条件](https://code.visualstudio.com/docs/python/python-tutorial)に説明があります。
- [Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。 全般的な情報については、[vscode-azurefunctions GitHub リポジトリ](https://github.com/Microsoft/vscode-azurefunctions)をご覧ください。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

ご利用のオペレーティング システムに対する指示は、「[Azure Functions Core Tools のインストール](functions-run-local.md#v2)」に従います。 ツール自体は .NET Core で記述されています。Core Tools パッケージは Node.js パッケージ マネージャー (npm) を使用してインストールすることをお勧めします。そのため、Python であっても、現在のところ、.NET Core と Node.js をインストールする必要があります。 ただし、"拡張バンドル" を利用して .NET Core 要件を回避できます。説明は前述のドキュメントにあります。 いずれにせよ、以上のコンポーネントは 1 回だけインストールする必要があります。その後、更新プログラムがあれば、Visual Studio Code からインストールを自動的に求められます。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

Functions 拡張機能がインストールされたら、Azure アカウントにサインインします。具体的には、 **[Azure:Functions]** エクスプローラーに進み、 **[Azure にサインイン]** を選択し、プロンプトに従います。

![Visual Studio Code で Azure にサインインする](media/tutorial-vs-code-serverless-python/azure-sign-in.png)

サインイン後、Azure サブスクリプションのメール アカウントがステータス バーに表示されることを確認します。

![Visual Studio Code で Azure アカウントが表示されているステータス バー](media/tutorial-vs-code-serverless-python/azure-account-status-bar.png)

サブスクリプションに割り当てた名前も **[Azure:Functions]** エクスプローラーに表示されます (下の画像では "プライマリ" です)。

![Visual Studio Code のサブスクリプションが表示されている Azure App Service エクスプローラー](media/tutorial-vs-code-serverless-python/azure-subscription-view.png)

> [!NOTE]
> **"サブスクリプション名 [subscription ID] が見つかりません"** というエラーが表示された場合、原因としては、プロキシの後ろにいるため、Azure API に届かないということが考えられます。 ご利用のターミナルのプロキシ情報に環境変数の `HTTP_PROXY` と `HTTPS_PROXY` を構成してください。
>
> ```bash
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```ps
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```

### <a name="verify-prerequisites"></a>前提条件を確認する

すべての Azure Functions ツールがインストールされていることを確認するには、Visual Studio Code コマンド パレット (F1) を開き、**Terminal:Create New Integrated Terminal** コマンドを選択し、ターミナルが開いたら、コマンド `func` を実行します。

![Azure Functions Core Tools の前提条件を確認する](media/tutorial-vs-code-serverless-python/check-prereqs.png)

Azure Functions ロゴで始まる出力は (出力を上方向にスクロールする必要があります)、Azure Functions Core Tools が存在することを示します。

`func` コマンドが認識されない場合は、Azure Functions Core Tools をインストールしたフォルダーが PATH 環境変数に含まれることを確認します。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=01-verify-prerequisites)

## <a name="create-the-function"></a>関数を作成する

1. Azure Functions のコードは、コードの作成前に最初に作成する Functions "_プロジェクト_" 内で管理されます。 **[Azure:Functions]** エクスプローラーで (左側の Azure アイコンを使用して開きます)、 **[新しいプロジェクト]** コマンド アイコンを選択するか、コマンド パレット (F1) を開き、 **[Azure Functions:新しいプロジェクトの作成]** を選択します。

    ![Functions エクスプローラーの [新しいプロジェクトの作成] ボタン](media/tutorial-vs-code-serverless-python/project-create-new.png)

1. 後続のプロンプトの内容:

    | Prompt | 値 | 説明 | 
    | --- | --- | --- |
    | Specify a folder for the project (プロジェクトのフォルダーを指定してください) | 現在開いているフォルダー | プロジェクトを作成するフォルダー。 サブフォルダーにプロジェクトを作成することもできます。 |
    | Select a language for your function app project (関数アプリ プロジェクトの言語を選択してください) | **Python** | 関数に使用する言語。これによりコードに使用するテンプレートが決定されます。 |
    | Select a template for your project's first function (プロジェクトの最初の関数のテンプレートを選択してください) | **HTTP トリガー** | HTTP トリガーを使用する関数は、関数のエンドポイントに HTTP 要求が行われるたびに実行されます。 (Azure Functions には他にもさまざまなトリガーがあります。 詳細は、「[Functions でできること](functions-overview.md#what-can-i-do-with-functions)」を参照してください。) |
    | Provide a function name (関数名を指定してください) | HttpExample | この名前は、構成データと共に関数のコードが含まれるサブフォルダーに使用されます。また、HTTP エンドポイントの名前がこの名前で定義されます。 関数自体とトリガーを区別する目的で、既定の "HTTPTrigger" をそのまま使用せず、"HttpExample" を使用します。 |
    | 承認レベル | **匿名** | 匿名認証の場合、関数は公開され、誰でもアクセスできます。 |
    | Select how you would like to open your project (プロジェクトを開く方法を選択してください) | **現在のウィンドウで開く** | 現在の Visual Studio Code ウィンドウでプロジェクトを開きます。 |

1. 短い時間の経過後、新しいプロジェクトが作成されたことを示すメッセージが表示されます。 **エクスプローラー**には、関数のために作成されたサブフォルダーがあります。Visual Studio Code によって *\_\_init\_\_.py* ファイルが開きますが、このファイルには既定の関数コードが含まれます。

    [![新しい Python 関数プロジェクトを作成した結果](media/tutorial-vs-code-serverless-python/project-create-results.png)](media/tutorial-vs-code-serverless-python/project-create-results.png)

    > [!NOTE]
    > " *\_\_init\_\_.py*" を開いたとき、Python インタープリターが選択されていないと Visual Studio Code に表示されたら、コマンド パレット (F1) を開き、**Python:Select Interpreter** コマンドを選択します。次に、(プロジェクトの一部として作成された) ローカルの `.env` フォルダーで仮想環境を選択します。 「[前提条件](#prerequisites)」で述べたように、この環境は厳密に Python 3.6x をベースにする必要があります。
    >
    > ![プロジェクトで作成された仮想環境を選択する](media/tutorial-vs-code-serverless-python/select-venv-interpreter.png)

> [!TIP]
> 同じプロジェクトで別の関数を作成するときは必ず、**Create Function** コマンドを **[Azure:Functions]** エクスプローラーで使用するか、コマンド パレット (F1) を開き、**Azure Functions:Create Function** コマンドを探して選択します。 いずれのコマンドでも、関数名 (エンドポイントの名前) が求められ、既定のファイルと共にサブフォルダーが作成されます。
>
> ![[Azure:Functions] エクスプローラー内の新しい関数を作成するコマンド](media/tutorial-vs-code-serverless-python/function-create-new.png)

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=02-create-function)

## <a name="examine-the-code-files"></a>コード ファイルを調べる

新しく作成された関数のサブフォルダーにファイルが 3 つあります。" *\_\_init\_\_.py*" には関数のコードが含まれます。"*function.json*" は、Azure Functions の関数を説明するものです。"*sample.dat*" はサンプル データ ファイルです。 "*sample.dat*" は、他のファイルをサブフォルダーに追加できることを示すためだけに存在します。必要に応じて削除できます。

まず、"*function.json*" を確認し、次に " *\_\_init\_\_.py*" のコードを確認しましょう。

### <a name="functionjson"></a>function.json

function.json ファイルには、Azure Functions エンドポイントに必要な構成情報があります。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

`scriptFile` プロパティで、コードのスタートアップ ファイルを特定します。そのコードには、`main` という名前の Python 関数が含まれている必要があります。 ここに指定されているファイルに `main` 関数が含まれている限り、コードを複数のファイルに分割できます。

`bindings` 要素には、2 つのオブジェクトが含まれています。1 つは受信要求を説明するものであり、もう 1 つは HTTP 応答を説明するものです。 受信要求 (`"direction": "in"`) の場合、関数は HTTP GET または POST 要求に応答し、認証を必要としません。 応答 (`"direction": "out"`) は、`main` Python 関数から返される値が何であれ、それを返す HTTP 応答です。

### <a name="__initpy__"></a>\_\_init.py\_\_

新しい関数を作成すると、Azure Functions によって " *\_\_init\_\_.py*" に既定の Python コードが与えられます。

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

コードの重要な部分は次のとおりです。

- `azure.functions` から `func` をインポートする必要があります。ログ記録モジュールのインポートは任意ですが、お勧めします。
- 必須の `main` Python 関数は、`req` という名前の `func.request` オブジェクトを受け取り、`func.HttpResponse` 型の値を返します。 これらのオブジェクトの機能については、[func.HttpRequest](/python/api/azure-functions/azure.functions.httprequest?view=azure-python) と [func.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse?view=azure-python) のリファレンスで詳細を確認できます。
- 次に、`main` の本文で要求が処理され、応答が生成されます。 今回のコードでは、URL の `name` パラメーターを探します。 見つからなかった場合、要求本文に JSON が含まれるかどうかが確認され (`func.HttpRequest.get_json` を使用)、また、JSON に `name` 値が含まれることが確認されます (`get_json` によって返される JSON オブジェクトの `get` メソッドを使用)。
- コードにより、名前が見つかった場合は、文字列 "Hello" の後ろに名前が追加されて返され、見つからなかった場合は、エラー メッセージが返されます。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=03-examine-code-files)

## <a name="debug-locally"></a>ローカル デバッグ

1. Functions プロジェクトを作成すると、Visual Studio Code 拡張機能により、**Attach to Python Functions** という名前の構成が 1 つ含まれる起動構成も`.vscode/launch.json` に作成されます。 この構成では、F5 を押すか、デバッグ エクスプローラーを使用し、プロジェクトを開始できます。

    ![Functions 起動構成が表示されたデバッグ エクスプローラー](media/tutorial-vs-code-serverless-python/launch-configuration.png)

1. デバッガーを起動すると、ターミナルが開き、利用できるエンドポイントの概要など、Azure Functions からの出力が表示されます。 "HttpExample" 以外の名前を使用した場合は、URL が異なることがあります。

    ```output
    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. Visual Studio Code の **[出力]** ウィンドウの URL で **Ctrl + クリック**または **Cmd + クリック**を使用し、ブラウザーを開いてそのアドレスを表示するか、ブラウザーを起動して同じ URL を貼り付けます。 いずれの場合でもエンドポイントは `api/<function_name>` であり、今回は `api/HttpExample` となります。 ただし、その URL には name パラメーターが含まれないため、ブラウザー ウィンドウには、コードのそのパスに応じて、"Please pass a name on the query string or in the request body" (クエリ文字列または要求本文で名前を渡してください) とだけ表示されます。

1. それでは、`http://localhost:7071/api/HttpExample?name=VS%20Code` のように、使用する name パラメーターを追加してみましょう。ブラウザー ウィンドウに "Hello Visual Studio Code!" というメッセージが表示され、そのコード パスを実行したことがわかります。

1. JSON 要求本文で名前値を渡すには、JSON インラインで curl のようなツールを使用できます。

    ```bash
    # Mac OS/Linux: modify the URL if you're using a different function name
    curl --header "Content-Type: application/json" --request POST \
        --data {"name":"Visual Studio Code"} http://localhost:7071/api/HttpExample
    ```

    ```ps
    # Windows (escaping on the quotes is necessary; also modify the URL
    # if you're using a different function name)
    curl --header "Content-Type: application/json" --request POST \
        --data {"""name""":"""Visual Studio Code"""} http://localhost:7071/api/HttpExample
    ```

    あるいは、`{"name":"Visual Studio Code"}` を含む "*data.json*" のようなファイルを作成し、コマンド `curl --header "Content-Type: application/json" --request POST --data @data.json http://localhost:7071/api/HttpExample` を使用します。

1. 関数のデバッグをテストするには、`name = req.params.get('name')` と記されている行にブレークポイントを設定し、URL に再度要求を行います。 Visual Studio Code デバッガーはその行で停止するはずです。変数を調べ、コードをステップ実行できます (基本的なデバッグの簡単なチュートリアルは、[Visual Studio Code チュートリアルのデバッガーの構成と実行](https://code.visualstudio.com/docs/python/python-tutorial.md#configure-and-run-the-debugger)に関するセクションにあります)。

1. 関数をローカルで徹底的にテストできたら、デバッガーを停止します ( **[デバッグ]**  >  **[デバッグの停止]** メニュー コマンドを使用するか、デバッグ ツールバーで **Disconnect** コマンドを使用します)。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=04-test-debug)

## <a name="deploy-to-azure-functions"></a>Azure Functions へのデプロイ

以下の手順では、Functions 拡張機能を使用し、Azure で関数アプリとその他の必要な Azure リソースを作成します。 Function App を使用すると、リソースの管理、デプロイ、および共有を容易にするためのロジック ユニットとして関数をグループ化できます。 また、データと[ホスティング プラン](functions-scale.md#hosting-plan-support)に Azure Storage アカウントが必要になります。 これらのリソースはすべて、1 つのリソース グループ内で整理されます。

1. **[Azure: Functions]** エクスプローラーで、**Deploy to Function App** コマンドを選択するか、コマンド パレット (F1) を開き、**Azure Functions:Deploy to Function App** コマンドを選択します。 繰り返しになりますが、関数アプリは Azure で Python プロジェクトが実行される場所です。

    ![Deploy to Function App コマンド](media/tutorial-vs-code-serverless-python/deploy-command.png)

1. プロンプトが表示されたら、 **[Create New Function App in Azure\(Azure で新しい関数アプリを作成する\)]** を選択し、Azure 全体で一意となる名前を指定します (通常、個人の名前か社名を他の一意の識別子と共に使用します。文字、数字、ハイフンを使用できます)。 前に Function App を作成した場合、その名前はこのオプション一覧に表示されます。

1. 拡張機能により次のアクションが実行されますが、それらは Visual Studio Code のポップアップ メッセージと **[出力]** ウィンドウで確認できます (処理には数分かかります)。

    - 与えた名前でリソース グループを作成します (ハイフンを除きます)。
    - そのリソース グループで、ストレージ アカウント、ホスティング プラン、関数アプリを作成します。 既定では、[従量課金プラン](functions-scale.md#consumption-plan)が作成されます。 専用プランで関数を実行するには、[拡張作成オプションを使用して発行を有効にする](functions-develop-vs-code.md)必要があります。
    - 関数アプリにコードをデプロイします。

    **[Azure:Functions]** エクスプローラーには進捗状況も表示されます。

    ![[Azure:Functions] エクスプローラー内の新しい関数を作成するコマンド](media/tutorial-vs-code-serverless-python/deploy-progress.png)

1. デプロイが完了すると、Azure Functions 拡張機能に 3 つの追加アクションのためのボタンを含むメッセージが表示されます。

    ![デプロイの成功と追加のアクションを示すメッセージ](media/tutorial-vs-code-serverless-python/deployment-popup.png)

    **[Stream logs]\(ログをストリーミング\)** と **[Upload settings]\(設定をアップロード\)** については、次のセクションを参照してください。 **[View output]\(出力を表示\)** については、後続の手順 5 をご覧ください。

1. デプロイ後、 **[出力]** ウィンドウには、Azure の公開エンドポイントも表示されます。

    ```output
    HTTP Trigger Urls:
      HttpExample: https://vscode-azure-functions.azurewebsites.net/api/HttpExample
    ```

    このエンドポイントを使用し、ローカルで実行したものと同じテストを実行します。要求本文には、URL パラメーターと JSON データを含む要求の一方または両方を使用します。 公開エンドポイントの結果は、前にテストしたローカル エンドポイントの結果に一致するはずです。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=05-deploy)

### <a name="stream-logs"></a>ログのストリーミング

ログのストリーミングのサポートは現在開発中です。詳細は、Azure Functions 拡張機能の[問題 589](https://github.com/microsoft/vscode-azurefunctions/issues/589) にあります。 デプロイ メッセージ ポップアップの **[Stream logs]\(ログをストリーミング\)** ボタンで最終的に Azure のログ出力と Visual Studio Code がつながります。 Functions プロジェクトを右クリックし、 **[ストリーミングの開始]** または **[ストリーミングの停止]** を選択することで、**Azure Functions** エクスプローラーでログ ストリームを開始したり、停止したりできるようになります。

ただし、現在のところ、これらのコマンドは動作しません。 ログ ストリーミングは代わりに、次のコマンドを実行することでブラウザーで実行できます。`<app_name>` は、Azure の Functions アプリの名前に変更してください。

```bash
# Replace <app_name> with the name of your Functions app on Azure
func azure functionapp logstream <app_name> --browser
```

### <a name="sync-local-settings-to-azure"></a>Azure にローカル設定を同期する

デプロイ メッセージ ポップアップの **[Upload settings]\(設定をアップロード\)** ボタンをクリックすると、"*local.settings.json*" ファイルに行った変更が Azure に適用されます。 このコマンドは、Functions プロジェクト ノードを展開して、 **[アプリケーション設定]** を右クリックし、 **[Upload local settings]\(ローカル設定のアップロード\)** を選択して、**Azure Functions** エクスプローラーで呼び出すこともできます。コマンド パレットを使用し、 **[Azure Functions:ローカル設定のアップロード]** コマンドを選択することもできます。

設定をアップロードすると、既存の設定が更新され、"*local.settings.json*" で定義されている新しい設定が追加されます。 アップロードしても、ローカル ファイルに記載されていない設定は Azure から削除されません。 そのような設定を削除するには、**Azure Functions** エクスプローラーで **[アプリケーション設定]** ノードを展開し、設定を右クリックし、 **[設定の削除]** を選択します。Azure portal で直接設定を編集することもできます。

行った変更をポータルか **Azure Explorer** 経由で "*local.settings.json*" ファイルに適用するには、 **[アプリケーション設定]** ノードを右クリックし、 **[リモート設定のダウンロード]** コマンドを選択します。 コマンド パレットを使用し、 **[Azure Functions:リモート設定のダウンロード]** コマンドを選択することもできます。

## <a name="add-a-second-function"></a>2 つ目の関数を追加する

最初のデプロイ後、関数を追加するなど、コードを変更し、同じ Functions App に再デプロイできます。

1. **[Azure: Functions]** エクスプローラーで **[関数の作成]** コマンドを選択するか、コマンド パレットの **[Azure Functions:関数の作成]** を使用します。 関数には次の詳細を指定します。

    - テンプレート:HTTP トリガー
    - 名前:"DigitsOfPi"
    - 承認レベル:Anonymous

1. Visual Studio Code では、ファイル エクスプローラーは関数名を持つサブフォルダーになりますが、ここにも " *\_\_init\_\_.py*"、"*function.json*"、"*sample.dat*" という名前のファイルが含まれています。

1. " *\_\_init\_\_.py*" の内容を次のコードに一致するように置換します。次のコードでは、URL に指定されている桁数まで PI の値を含む文字列が生成されます (このコードでは、URL パラメーターのみが使用されます)。

    ```python
    import logging

    import azure.functions as func

    """ Adapted from the second, shorter solution at http://www.codecodex.com/wiki/Calculate_digits_of_pi#Python
    """

    def pi_digits_Python(digits):
        scale = 10000
        maxarr = int((digits / 4) * 14)
        arrinit = 2000
        carry = 0
        arr = [arrinit] * (maxarr + 1)
        output = ""

        for i in range(maxarr, 1, -14):
            total = 0
            for j in range(i, 0, -1):
                total = (total * j) + (scale * arr[j])
                arr[j] = total % ((j * 2) - 1)
                total = total / ((j * 2) - 1)

            output += "%04d" % (carry + (total / scale))
            carry = total % scale

        return output;

    def main(req: func.HttpRequest) -> func.HttpResponse:
        logging.info('DigitsOfPi HTTP trigger function processed a request.')

        digits_param = req.params.get('digits')

        if digits_param is not None:
            try:
                digits = int(digits_param)
            except ValueError:
                digits = 10   # A default

            if digits > 0:
                digit_string = pi_digits_Python(digits)

                # Insert a decimal point in the return value
                return func.HttpResponse(digit_string[:1] + '.' + digit_string[1:])

        return func.HttpResponse(
             "Please pass the URL parameter ?digits= to specify a positive number of digits.",
             status_code=400
        )
    ```

1. このコードでは HTTP GET のみがサポートされるため、`"methods"` コレクションに `"get"` だけが含まれるように "*function.json*" を変更します (つまり、`"post"` を削除します)。 ファイル全体は次のようになります。

    ```json
    {
      "scriptFile": "__init__.py",
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ]
        },
        {
          "type": "http",
          "direction": "out",
          "name": "$return"
        }
      ]
    }
    ```

1. F5 を押すか、 **[デバッグ]**  >  **[デバッグの開始]** メニュー コマンドを選択し、デバッガーを開始します。 **[出力]** ウィンドウには、プロジェクトの両方のエンドポイントが表示されるはずです。

    ```output
    Http Functions:

            DigitsOfPi: [GET] http://localhost:7071/api/DigitsOfPi

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. ブラウザーで、あるいは curl から、`http://localhost:7071/api/DigitsOfPi?digits=125` に要求を行い、出力を確認します (コード アルゴリズムは完全には正しくないことに気付かれるかもしれませんが、改善は皆様におまかせします!)完了したらデバッガーを停止します。

1. **[Deploy to Function App]\(Function App にデプロイする\)** を **[Azure:Functions]** エクスプローラーで使用し、コードを再デプロイします。 プロンプトが表示されたら、前に作成した Function App を選択します。

1. デプロイが完了したら (数分かかります!)、 **[出力]** ウィンドウに、テストを繰り返すことができる公開エンドポイントが表示されます。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=06-second-function)

## <a name="add-a-binding-to-write-messages-to-azure-storage"></a>Azure Storage にメッセージを書き込む目的でバインドを追加する

"_バインド_" を追加すると、データ アクセス コードを記述しなくても、Azure Storage などのリソースに関数コードを接続できます。 バインドは "*function.json*" ファイルに定義され、入力と出力の両方を表すことができます。 関数では複数の入力と出力のバインドを使用できますが、トリガーは 1 つだけです。 詳細については、「[Azure Functions でのトリガーとバインドの概念](functions-triggers-bindings.md)」を参照してください。

このセクションでは、このチュートリアルで前に作成した HttpExample 関数にストレージ バインドを追加します。 関数でこのバインドを使用し、要求ごとにストレージにメッセージを書き込みます。 ストレージでは、関数アプリで使用されているものと同じ既定のストレージ アカウントが使用されます。 ただし、ストレージを頻繁に利用する予定がある場合は、別アカウントの作成を検討してください。

1. Azure Functions プロジェクトのリモート設定を "*local.settings.json*" ファイルに同期します。その際、コマンド パレットを開き、 **[Azure Functions:リモート設定のダウンロード]** を選択します。 "*local.settings.json*" を開き、`AzureWebJobsStorage` の値が含まれていることを確認します。 その値はストレージ アカウントの接続文字列です。

1. `HttpExample` フォルダーで、"*function.json*" を右クリックし、 **[バインドの追加]** を選択します。

    ![Visual Studio Code エクスプローラーの [バインドの追加] コマンド](media/tutorial-vs-code-serverless-python/add-binding-command.png)

1. Visual Studio Code の後続のプロンプトで、次の値を選択するか、指定します。

    | Prompt | 指定する値 |
    | --- | --- |
    | Set binding direction (バインド方向を設定してください) | out |
    | Select binding with direction out (外方向のバインドを選択してください) | Azure Queue Storage |
    | The name used to identify this binding in your code (コードでこのバインドの識別に使用する名前) | msg |
    | The queue to which the message will be sent (メッセージの送信先のキュー) | outqueue |
    | Select setting from "*local.settings.json*" (asking for the storage connection) ("local.settings.json" から設定を選択してください (ストレージ接続を要求)) | AzureWebJobsStorage |

1. 以上の選択を行ったら、次のバインドが "*function.json*" ファイルに追加されていることを確認します。

    ```json
        {
          "type": "queue",
          "direction": "out",
          "name": "msg",
          "queueName": "outqueue",
          "connection": "AzureWebJobsStorage"
        }
    ```

1. これでバインドが構成されました。バインドを関数コードで使用できます。 繰り返しになりますが、新しく定義されたバインドは、" *\_\_init\_\_.py*" の `main` 関数の引数としてコードに表示されます。 たとえば、次のコードに一致するように、HttpExample で " *\_\_init\_\_.py*" ファイルを変更できます。次のコードでは、`msg` 引数を使用することで、要求に使用されている名前でタイムスタンプ付きのメッセージが書かれます。 コメントからは、特定の変更について説明されます。

    ```python
    import logging
    import datetime  # MODIFICATION: added import
    import azure.functions as func

    # MODIFICATION: the added binding appears as an argument; func.Out[func.QueueMessage]
    # is the appropriate type for an output binding with "type": "queue" (in function.json).
    def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> func.HttpResponse:
        logging.info('Python HTTP trigger function processed a request.')

        name = req.params.get('name')
        if not name:
            try:
                req_body = req.get_json()
            except ValueError:
                pass
            else:
                name = req_body.get('name')

        if name:
            # MODIFICATION: write the a message to the message queue, using msg.set
            msg.set(f"Request made for {name} at {datetime.datetime.now()}")

            return func.HttpResponse(f"Hello {name}!")
        else:
            return func.HttpResponse(
                 "Please pass a name on the query string or in the request body",
                 status_code=400
            )
    ```

1. このような変更をローカルでテストするには、F5 を押すか、 **[デバッグ]**  >  **[デバッグの開始]** メニュー コマンドを選択し、Visual Studio Code で再度、デバッガーを起動します。 前と同じように、 **[出力]** ウィンドウには、プロンプトのエンドポイントが表示されるはずです。

1. ブラウザーで、URL `http://localhost:7071/api/HttpExample?name=VS%20Code` にアクセスし、HttpExample エンドポイントへの要求を作成します。これにより、メッセージのキューへの書き込みも行われるはずです。

1. メッセージが "outqueue" キュー (バインドで名前を設定) に書き込まれたことを確認するには、3 つのメソッドのいずれかを使用できます。

    1. [Azure portal](https://portal.azure.com) にサインインし、関数プロジェクトが含まれるリソース グループに移動します。 そのリソース グループ内で、プロジェクトのストレージ アカウントを見つけて移動し、 **[キュー]** に進みます。 そのページで "outqueue" に進みます。ログに記録されているメッセージがすべて表示されるはずです。

    1. Azure Storage Explorer でキューに移動し、キューを調べます。Azure Storage Explorer は Visual Studio と統合されますが、詳細は「[Visual Studio Code を使用して関数を Azure Storage に接続する](functions-add-output-binding-storage-queue-vs-code.md)」にあります。特に「[出力キューを確認する](functions-add-output-binding-storage-queue-vs-code.md#examine-the-output-queue)」セクションをご覧ください。

    1. Azure CLI を使用し、ストレージ キューにクエリを実行します。詳細は「[ストレージ キューに対するクエリを実行する](functions-add-output-binding-storage-queue-python.md#query-the-storage-queue)」にあります。
    
1. クラウドでテストするには、 **[Deploy to Function App]\(Function App にデプロイする\)** を **[Azure:Functions]** エクスプローラーで使用し、コードを再デプロイします。 プロンプトが表示されたら、前に作成した Function App を選択します。 デプロイが完了したら (数分かかります!)、繰り返しになりますが、 **[出力]** ウィンドウにテストを繰り返すことができる公開エンドポイントが表示されます。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=07-storage-binding)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

作成した Function App には、最小限のコストが発生する可能性があるリソースが含まれます ([Functions の価格](https://azure.microsoft.com/pricing/details/functions/)ページを参照してください)。 リソースをクリーンアップするには、**Azure:Functions** エクスプローラーで Function App を右クリックし、 **[Delete Function App]\(関数アプリの削除\)** を選択します。 [Azure portal](https://portal.azure.com) にアクセスし、左側にあるナビゲーション ウィンドウから **[リソース グループ]** を選択し、このチュートリアルで作成したリソース グループを選択し、 **[リソース グループの削除]** コマンドを使用することもできます。

## <a name="next-steps"></a>次の手順

このチュートリアルを完了し、Azure Functions に Python コードをデプロイしました。おめでとうございます! これで、よりたくさんのサーバーレス関数を作成できる準備が整いました。

前述のように、Functions 拡張機能の詳細は GitHub リポジトリの [vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions) で参照できます。 問題提起や投稿も歓迎いたします。

「[Azure Functions の概要](functions-overview.md)」では、使用できるさまざまなトリガーについて説明されています。

データ ストレージのほか、AI や機械学習のサービスなど、Python から使用できる Azure サービスの詳細については、[Azure Python デベロッパー センター](/azure/python/?view=azure-python)にアクセスしてください。

Visual Studio Code 用の Azure 拡張機能は他にもあります。役に立つ機能が見つかるかもしれません。 拡張機能のエクスプローラーで、"Azure" で検索してください。

![Visual Studio Code 用の Azure 拡張機能](media/tutorial-vs-code-serverless-python/azure-extensions.png)

人気の拡張機能の一部:

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)
- [Azure CLI Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Azure リソース マネージャー ツール](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

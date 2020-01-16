---
title: VS Code を使用して Kusto クエリ言語インライン Python をデバッグする - Azure Data Explorer
description: VS Code を使用して Kusto クエリ言語 (KQL) インライン Python をデバッグする方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 96bd66f96b04bd7032d976ba9ebbbeb60c8415e7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444466"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>VS Code を使用して Kusto クエリ言語インライン Python をデバッグする

Azure Data Explorer は、[python() プラグイン](/azure/kusto/query/pythonplugin)を使用して、Kusto クエリ言語に埋め込まれた Python コードの実行をサポートします。 プラグイン ランタイムは、分離された安全な Python 環境であるサンドボックスでホストされます。 Python() プラグイン機能は、OSS Python パッケージの大規模なアーカイブで Kusto クエリ言語のネイティブ機能を拡張します。 この拡張機能を使用すると、機械学習、人工知能、統計、時系列などの高度なアルゴリズムをクエリの一部として実行できます。

Kusto クエリ言語ツールは、Python アルゴリズムの開発とデバッグには便利ではありません。 そのため、Jupyter、PyCharm、VS、VS Code など、使い慣れた Python 統合開発環境でアルゴリズムを開発してください。 アルゴリズムが完了したら、KQL にコピーして貼り付けます。 このワークフローを改善し、効率化するために、Azure Data Explorer では、KQL のインライン Python コードを作成してデバッグするために、Kusto エクスプローラーまたは Web UI クライアントと VS Code 間の統合をサポートしています。 

> [!NOTE]
> このワークフローは、比較的小さな入力テーブル (最大数 MB) のデバッグにのみ使用できます。 そのため、デバッグのために入力を制限する必要がある場合があります。  大きなテーブルを処理する必要がある場合は、`| take`、`| sample`、または `where rand() < 0.x` を使用してデバッグできるように制限します。

## <a name="prerequisites"></a>前提条件

1. Python [Anaconda Distribution](https://www.anaconda.com/distribution/#download-section) をインストールします。 **[詳細オプション]** で、 **[Add Anaconda to my PATH environment variable]\(PATH 環境変数に Anaconda を追加する\)** を選択します。
2. [Visual Studio Code](https://code.visualstudio.com/Download) をインストールします
3. [Visual Studio Code 用の Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-python.python)をインストールします。

## <a name="run-your-query-in-your-client-application"></a>クライアント アプリケーションでクエリを実行する

1. クライアント アプリケーションで、インライン Python を含むクエリにプレフィックス `set query_python_debug;` を付けます。
1. クエリを実行します。
    * Kusto エクスプローラー:VS Code は、*debug_python.py* スクリプトで自動的に起動されます。
    * Kusto Web UI: 
        1. *debug_python.py*、*df.txt*、および *kargs.txt* をダウンロードして保存します。 ウィンドウで、 **[許可]** を選択します。 選択したディレクトリにファイルを **[保存]** します。 

            ![Web UI がインライン python ファイルをダウンロードします](media/debug-inline-python/webui-inline-python.png)

        1. *debug_python.py* を右クリックし、VS コードで開きます。 
        *debug_python.py* スクリプトには、KQL クエリからのインライン Python コードが含まれ、データフレームを *df.txt* から初期化し、パラメーターの辞書を *kargs.txt* から初期化するためにプリフィックスとしてテンプレート コードが付けられています。    
            
1. VS Code で、VS Code デバッガーを起動します。 **[デバッグ]**  >  **[デバッグの開始] (F5)** の順に選択し、 **[Python]** 構成を選択します。 デバッガーが起動し、インライン コードをデバッグするブレークポイントが自動的に設定されます。

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>VS Code のインライン Python デバッグはどのように動作しますか。

1. クエリは、必要な `| evaluate python()` 句に到達するまで、サーバーで解析および実行されます。
1. Python サンドボックスは呼び出されますが、コードを実行する代わりに、入力テーブル、パラメーターのディクショナリ、およびコードをシリアル化して、クライアントに送り返します。
1. これら 3 つのオブジェクトは、選択したディレクトリ (Web UI) またはクライアントの %TEMP% ディレクトリ (Kusto Explorer) の 3 つのファイル *df.txt*、*kargs.txt*、および *debug_python.py* に保存されます。
1. VS Code が起動され、*debug_python.py* ファイルがプリロードされます。このファイルには、それぞれのファイルから df と kargs を初期化するプレフィックス コードと、KQL クエリに埋め込まれた Python スクリプトが含まれています。

## <a name="query-example"></a>クエリの例

1. クライアント アプリケーションで次の KQL クエリを実行します。

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    結果のテーブルを参照してください。

    | x  | x4  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. `set query_python_debug;` を使用して、クライアント アプリケーションで同じ KQL クエリを実行します。

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. VS Code が起動されます。

    ![VS Code を起動する](media/debug-inline-python/launch-vs-code.png)

1. VS Code は、次のようにデバッグ コンソールで "結果" データフレームをデバッグして出力します。

    ![VS Code のデバッグ](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Python サンドボックス イメージとローカル インストールに違いがある場合があります。 [プラグインに対してクエリを実行し、特定のパッケージのサンドボックスイメージを確認します。](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl)

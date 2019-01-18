---
title: クイック スタート:Speech Service SDK を使用して Python で音声を認識する
titleSuffix: Azure Cognitive Services
description: Speech Service SDK を使用して Python で音声を認識する方法について説明します
services: cognitive-services
author: chlandsi
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: chlandsi
ms.openlocfilehash: 7610b12b351b2652df7ade603711d4d92e587292
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53723911"
---
# <a name="quickstart-using-the-speech-service-from-python"></a>クイック スタート:Python から Speech Service を使用する

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、Python 用 Speech SDK を通じて Speech Service を使用する方法について説明します。 マイクの入力から音声を認識する方法を解説します。

## <a name="prerequisites"></a>前提条件

開始する前の前提条件の一覧を次に示します。

* Speech Service の Azure サブスクリプション キー。 [無料で 1 つ取得します](get-started.md)。
* [Python 3.5 (64 ビット)](https://www.python.org/downloads/) 以降。
* Python 用 Speech SDK パッケージは、Windows (x64)、Mac (macOS X バージョン 10.12 以降)、Linux (Ubuntu 16.04 または 18.04、x64) で使用可能です。
* Ubuntu では、必要なパッケージをインストールするために次のコマンドを実行します。

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

* Windows では、お使いのプラットフォームに対応した [Microsoft Visual Studio 2017 の Visual C++ 再頒布可能パッケージ](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) も必要になります。

## <a name="get-the-speech-sdk-python-package"></a>Speech SDK Python パッケージを取得する

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Cognitive Services Speech SDK Python パッケージは、コマンドラインで次のコマンドを使用することで、[PyPI](https://pypi.org/) からインストールできます。

```sh
pip install azure-cognitiveservices-speech
```

Cognitive Services Speech SDK の現在のバージョンは `1.2.0` です。

## <a name="support-and-updates"></a>サポートと更新プログラム

Speech SDK Python パッケージの更新プログラムは、PyPI を通じて配布され、「[リリース ノート](./releasenotes.md)」ページで発表されます。
新しいバージョンが利用可能な場合は、コマンド `pip install --upgrade azure-cognitiveservices-speech` を使用してそれに更新できます。
`azure.cognitiveservices.speech.__version__` 変数を調べることで、現在インストールされているバージョンを確認することができます。

問題があるか、機能が欠落している場合は、[サポート ページ](./support.md)を参照してください。

## <a name="create-a-python-application-using-the-speech-sdk"></a>Speech SDK を使用して Python アプリケーションを作成する

### <a name="running-the-sample-in-a-terminal"></a>ターミナルでサンプルを実行する

このクイック スタートからソース ファイル `quickstart.py` に[コード](#quickstart-code)をコピーしてお使いの IDE またはコンソールで実行することができます。

```sh
python quickstart.py
```

また、このクイック スタート チュートリアルを [Cognitive Services Speech のサンプル リポジトリ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/)から [Jupyter](https://jupyter.org) ノートブックとしてダウンロードして、ノートブックとして実行することもできます。

### <a name="quickstart-code"></a>クイック スタート コード

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="installing-the-speech-sdk-python-package-and-running-the-sample-in-visual-studio-code"></a>Speech SDK Python パッケージをインストールして、Visual Studio Code でサンプルを実行する

1. Python の 64 ビット バージョン (3.5 以降) をお使いのコンピューターに[ダウンロード](https://www.python.org/downloads/)して、インストールします。
1. Visual Studio Code を[ダウンロード](https://code.visualstudio.com/Download)して、インストールします。
1. Visual Studio Code を開き、Python 拡張機能をインストールします。そのためには、メニューから **[ファイル]** > **[基本設定]** > **[Extensions]\(拡張機能\)** の順に選択して、"Python" を検索します。
   ![Python 拡張機能をインストールする](media/sdk/qs-python-vscode-python-extension.png)
1. Windows エクスプ ローラーなどを使用して、プロジェクトを格納するフォルダーを作成します。
1. Visual Studio Code で**ファイル** アイコンをクリックして、作成したフォルダーを開きます。
   ![フォルダーを開く](media/sdk/qs-python-vscode-python-open-folder.png)
1. 新しいファイル アイコンをクリックして、新しい Python ソース ファイル `speechsdk.py` を作成します。
   ![ファイルを作成する](media/sdk/qs-python-vscode-python-newfile.png)
1. 新しく作成されたファイルに [Python コード](#quickstart-code)をコピーして貼り付け、保存します。
1. お使いの Speech Service のサブスクリプション情報を挿入します。
1. Python インタープリターが既に選択されている場合は、ウィンドウの下部にあるステータス バーの左側に表示されます。
   それ以外の場合は、**コマンド パレット**を開いて (`Ctrl+Shift+P`) 「**Python:Select Interpreter**」と入力することで、使用可能な Python インタープリターの一覧を表示し、適切なものを選択できます。
1. 選択した Python インタープリター用の Speech SDK Python パッケージをまだインストールしていない場合は、これを Visual Studio Code から簡単に行うことができます。
   Speech SDK パッケージをインストールするには、もう一度コマンド パレットを開き (`Ctrl+Shift+P`)、「**Terminal:Create New Integrated Terminal**」と入力してターミナルを開きます。
   開いたターミナルに、コマンド `python -m pip install azure-cognitiveservices-speech` か、システムに応じた適切なコマンドを入力します。
1. サンプル コードを実行するには、エディター内のどこかを右クリックし、**[Run Python File in Terminal]\(ターミナル内の Python ファイルを実行する\)** を選択します。
   求められたら、いくつかの単語を発話してください。すぐに、文字起こしされたテキストが表示されます。
   ![サンプルを実行する](media/sdk/qs-python-vscode-python-run.png)

これらの手順に従ううえで問題が発生した場合は、より詳しい [Visual Studio Code Python チュートリアル](https://code.visualstudio.com/docs/python/python-tutorial)を参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で Python のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

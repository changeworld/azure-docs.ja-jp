---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: eur
ms.openlocfilehash: 6a33020c686686d40529b8188ccfcf7fcb412331
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252438"
---
このガイドでは、Python 用 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) をインストールする方法について説明します。 このパッケージ名の使用を自分で開始する場合は、`pip install azure-cognitiveservices-speech` を実行します。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

- 以下のオペレーティング システム用の Python Speech SDK パッケージを入手できます。
  - Windows: x64 および x86
  - Mac: macOS X バージョン10.14 以降 (macOS の最小 Python バージョンは 3.7)
  - Linux。[サポートされている Linux ディストリビューションとターゲット アーキテクチャ](~/articles/cognitive-services/speech-service/speech-sdk.md)の一覧を参照してください。

## <a name="prerequisites"></a>前提条件

- Windows では、お使いのプラットフォームに対応した [Microsoft Visual Studio 2019 の Visual C++ 再頒布可能パッケージ](https://support.microsoft.com/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0)が必要です。 これを初めてインストールする場合、再起動が必要になる場合があります。

- Linux については、[システム要件とセットアップ手順](~/articles/cognitive-services/speech-service/speech-sdk.md#get-the-speech-sdk)に関する記事を参照してください。

- 最後に、[Python 3.6 から 3.9](https://www.python.org/downloads/) が必要になります。 インストールを確認するには、コマンド プロンプトを開き、コマンド `python --version` を入力して、結果を確認します。 適切にインストールされた場合、"Python 3.8.8" のような応答が返されます。

## <a name="install-the-speech-sdk-from-pypi"></a>PyPI から Speech SDK をインストールする

独自の環境またはビルド ツールを使用している場合は、次のコマンドを実行して [PyPI](https://pypi.org/) から Speech SDK をインストールします。 Visual Studio Code のユーザーの場合は、ガイド付きのインストールに関する次のサブセクションにスキップしてください。

```sh
pip install azure-cognitiveservices-speech
```

macOS を使用している場合は、次のコマンドを実行して、上記の `pip` コマンドを取得する必要があります。

```sh
python3 -m pip install --upgrade pip
```

`pip` を正常に使用して `azure-cognitiveservices-speech` をインストールしたら、その名前空間を Python プロジェクトにインポートすることで、Speech SDK を使用できます。

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Visual Studio Code を使用して Speech SDK をインストールする

1. お使いのプラットフォームでサポートされている最新バージョンの [Python](https://www.python.org/downloads/) (3.6 から 3.9) をダウンロードしてインストールします。
   - Windows ユーザーは、インストールプロセス中に [Add Python to your PATH]\(Python をパスに追加する\) を選択していることを確認してください。
1. [Visual Studio Code](https://code.visualstudio.com/Download) をダウンロードして、インストールします。
1. Visual Studio Code を開いて、Python 拡張機能をインストールします。 メニューで **[ファイル]**  >  **[基本設定]**  >  **[拡張機能]** の順に選択します。 **Python** を検索して、 **[インストール]** をクリックします。

   ![Python 拡張機能のインストール](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. または、Visual Studio Code 内から、統合コマンド ラインで Speech SDK Python パッケージをインストールします。
   1. ターミナル (ドロップダウン メニューから、 **[ターミナル]**  >  **[新しいターミナル]** ) を開きます
   1. ターミナルが開いたら、コマンド `python -m pip install azure-cognitiveservices-speech` を入力します

Visual Studio Code を初めて使用する場合は、詳細な [Visual Studio Code のドキュメント](https://code.visualstudio.com/docs) を参照してください。 Visual Studio Code および Python の詳細については、[Visual Studio Code Python のチュートリアル](https://code.visualstudio.com/docs/python/python-tutorial)に関するページをご覧ください。

## <a name="support-and-updates"></a>サポートと更新プログラム

Speech SDK Python パッケージの更新プログラムは、PyPI を通じて配布され、[リリース ノート](~/articles/cognitive-services/speech-service/releasenotes.md)で発表されます。
新しいバージョンが利用可能な場合は、コマンド `pip install --upgrade azure-cognitiveservices-speech` を使用してそれに更新できます。
`azure.cognitiveservices.speech.__version__` 変数を調べて、現在インストールされているバージョンを確認します。

問題があるか、機能が欠落している場合は、「[サポート オプションとヘルプ オプション](../../../../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)」を参照してください。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [windows](../quickstart-list.md)]

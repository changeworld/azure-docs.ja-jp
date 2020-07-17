---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 3613d190ef079d0e477d42b426a224d8e4dda7e6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400681"
---
## <a name="prerequisites"></a>前提条件

開始する前に、以下の操作を行います。

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech リソースを作成する <span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [開発環境を設定し、空のプロジェクトを作成する](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * オーディオ キャプチャ用のマイクにアクセスできることを確認する

## <a name="source-code"></a>ソース コード

*quickstart.py* という名前のファイルを作成し、次の Python コードを貼り付けます。

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>コードの説明

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>アプリをビルドして実行する

これで、アプリをテストし、音声サービスを使用して音声認識機能を確認する準備ができました。

1. **アプリを開始する** - コマンド ラインで、次のように入力します:
    ```bash
    python quickstart.py
    ```
2. **認識を開始する** - 英語で語句を読み上げるよう求めるメッセージが表示されます。 音声が Speech Service に送信され、テキストとして文字起こしされて、コンソールに表示されます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]


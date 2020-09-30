---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: b68025c1b33a94f03e2d84693a7d6407a18abd88
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332468"
---
このクイックスタートでは、Speech SDK を使用してテキスト読み上げ合成を行うための一般的な設計パターンについて説明します。 まずは基本的な構成と合成を行った後、次のようなより高度なカスタム アプリケーション開発の例に進みます。

* インメモリ ストリームとして応答を取得する
* 出力のサンプル レートとビット レートをカスタマイズする
* SSML (音声合成マークアップ言語) を使用して合成要求を送信する
* ニューラル音声を使用する

## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../overview.md#try-the-speech-service-for-free)。

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="synthesize-speech-to-a-speaker"></a>スピーカーに音声を合成する

Speech CLI を実行して、音声をテキストに合成する準備ができました。 コマンド ラインから、Speech CLI バイナリ ファイルが含まれるディレクトリに変更します。 次に、次のコマンドを実行します。

```bash
spx synthesize --text "The speech synthesizer greets you!"
```

Speech CLI では、コンピューターのスピーカーを通じて、英語の自然言語を生成します。

## <a name="synthesize-speech-to-a-file"></a>音声をファイルに合成する

次のコマンドを実行して、スピーカーからの出力を `.wav` ファイルに変更します。

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Speech CLI では、`greetings.wav` オーディオ ファイルに英語の自然言語を生成します。
Windows では、`start greetings.wav` と入力すると、オーディオファイルを再生できます。
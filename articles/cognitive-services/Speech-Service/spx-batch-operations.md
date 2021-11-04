---
title: Speech CLI のバッチ操作 - 音声サービス
titleSuffix: Azure Cognitive Services
description: Speech CLI を使用して、バッチ音声テキスト変換 (音声認識)、バッチ テキスト読み上げ (音声合成) を行う方法について説明します。
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: eur
ms.openlocfilehash: 1049720604ba4ba3a0be1874e0364e2e92c321e9
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505215"
---
# <a name="speech-cli-batch-operations"></a>Speech CLI のバッチ操作

Azure Speech Services を使用するときの一般的なタスクは、バッチ操作です。 この記事では、Speech CLI を使用して、バッチ音声テキスト変換 (音声認識)、バッチ操作テキスト読み上げ (音声合成) を行う方法について説明します。 具体的には、次の操作を行う方法を学習します。

* オーディオ ファイルのディレクトリでバッチ音声認識を実行する
* `.tsv` ファイルを反復処理してバッチ音声合成を実行する

## <a name="batch-speech-to-text-speech-recognition"></a>バッチ音声テキスト変換 (音声認識)

音声サービスは、多くの場合、オーディオ ファイルから音声を認識するために使用されます。 この例では、Speech CLI を使用してディレクトリを反復処理し、各 `.wav` ファイルの認識出力をキャプチャする方法について説明します。 `--files` フラグを使用してオーディオ ファイルが格納されているディレクトリを指定し、ワイルドカード `*.wav` を使用して拡張子が `.wav` であるすべてのファイルに対して認識を実行するように Speech CLI に指示します。 各認識ファイルの出力は、タブ区切り値として `speech_output.tsv` に書き込まれます。

> [!NOTE]
> `--threads` 引数は、次のセクションの `spx synthesize` コマンドでも使用できますが、使用可能なスレッドは CPU とその現在の負荷の割合によって変化します。

```console
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

出力ファイルの構造の例を次に示します。

```output
audio.input.id  recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-speech-synthesis"></a>バッチ テキスト読み上げ (音声合成)

バッチ テキスト読み上げを実行する最も簡単な方法は、新しい `.tsv` (タブ区切り値) ファイルを作成し、Speech CLI で `--foreach` コマンドを使用することです。 任意のテキスト エディターを使用して `.tsv` ファイルを作成することができます。この例では `text_synthesis.tsv` という名前にしましょう。

>[!IMPORTANT]
> このテキスト ファイルの内容をコピーするときは、ファイル内のファイルの場所とテキストの間が空白ではなく **タブ** になっていることを確認してください。 場合によっては、この例の内容をコピーするとタブがスペースに変換され、それが原因で `spx` コマンドの実行に失敗します。

```Input
audio.output    text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

次に、`text_synthesis.tsv` を指すコマンドを実行し、各 `text` フィールドで合成を実行し、対応する `audio.output` パスに `.wav` ファイルとして結果を書き込みます。

```console
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

このコマンドは、`.tsv` ファイルの **各** レコードに対して `spx synthesize --text "Sample text to synthesize" --audio output C:\batch_wav_output\wav_1.wav` を実行するのと同じです。

注意すべき点がいくつかあります。

* 列ヘッダーの `audio.output` と `text` は、それぞれコマンドライン引数の `--audio output` と `--text` に対応します。 `--audio output` のようなマルチパートのコマンドライン引数は、スペースなし、先頭ダッシュなし、文字列区切りのピリオド (例: `audio.output`) を使用してファイル内で書式設定する必要があります。 その他の既存のコマンドライン引数は、このパターンを使用して追加列としてファイルに追加できます。
* ファイルがこのように書式設定されている場合は、追加の引数を `--foreach` に渡す必要はありません。
* 必ず、`.tsv` 内の各値を **タブ** で区切ってください。

ただし、次の例のように、列ヘッダーがコマンドライン引数と **一致しない** `.tsv` ファイルがあるとします。

```Input
wav_path    str_text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

次の構文を使用して、`--foreach` 呼び出しでこれらのフィールド名を正しい引数にオーバーライドできます。 これは上記と同じ呼び出しです。

```console
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>次のステップ

* [Speech CLI の概要](./spx-overview.md)
* [Speech CLI のクイックスタート](./spx-basics.md)

---
title: Speech CLI の基本
titleSuffix: Azure Cognitive Services
description: Speech CLI コマンド ツールを使用して、コードを記述せずに最小限のセットアップで音声サービスを操作する方法について説明します。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: bead348e64fcee4cc5b790f975c9da5200ee796b
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422401"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>Speech CLI の基本を学ぶ

この記事では、Speech CLI の基本的な使用パターンについて説明します。これは、コードを記述せずに音声サービスを使用できるコマンド ライン ツールです。 開発環境を作成したり、コードを記述したりせずに、音声サービスの主な機能をすばやくテストして、ユースケースが適切に合致しているかどうかを確認できます。 また、Speech CLI は実稼働準備が整っており、`.bat` またはシェル スクリプトを使用して、音声サービスの単純なワークフローを自動化するために使用できます。

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>基本的な使用方法

このセクションでは、初めてのテストと実験に役立つことが多いいくつかの基本的な SPX コマンドについて説明します。 まずは、次のコマンドを実行して、ツールに組み込まれているヘルプを参照してください。

```shell
spx
```

コマンド パラメーターの右側に一覧表示されている **see: (参照)** ヘルプ トピックに注目してください。 これらのコマンドを入力することで、サブコマンドについての詳しいヘルプを表示できます。

キーワードでヘルプ トピックを検索することができます。 たとえば、次のコマンドを入力すると、Speech CLI の使用例が一覧表示されます。

```shell
spx help find --topics "examples"
```

recognize コマンドのオプションを表示するには、次のコマンドを入力します。

```shell
spx help recognize
```

次に、次のコマンドを実行して、Speech サービスを使用して既定のマイクによる音声認識を実行します。

```shell
spx recognize --microphone
```

コマンドを入力すると、SPX により、現在のアクティブな入力デバイスでオーディオのリッスンが開始され、`ENTER` キーを押すと停止します。 録音した音声が認識され、コンソール出力でテキストに変換されます。 Speech CLI を使用すると、テキスト読み上げ合成も簡単に実行できます。 

次のコマンドを実行すると、入力したテキストが入力として取得され、合成された音声が、現在のアクティブな出力デバイスに出力されます。

```shell
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Speech CLI を使用すると、音声認識および合成に加えて、音声翻訳を行うこともできます。 上記の音声認識コマンドと同様に、次のコマンドを実行して、既定のマイクからオーディオをキャプチャし、ターゲット言語のテキストに翻訳します。

```shell
spx translate --microphone --source en-US --target ru-RU --output file C:\some\file\path\russian_translation.txt
```

このコマンドでは、ソース (翻訳 **元** 言語) と、ターゲット (翻訳 **先** 言語) の両方を指定します。 `--microphone` 引数を使用して、現在のアクティブな入力デバイスでオーディオをリッスンし、`ENTER` キーを押すと停止します。 出力はターゲット言語へのテキスト翻訳で、テキスト ファイルに書き込まれます。

> [!NOTE]
> サポートされているすべての言語と対応するロケール コードの一覧については、「[音声サービスの言語と音声のサポート](language-support.md)」を参照してください。

### <a name="configuration-files-in-the-datastore"></a>データストア内の構成ファイル

Speech CLI の動作は、構成ファイルの設定に依存する場合があります。これは、@ 記号を使用して Speech CLI 呼び出し内で参照できます。
Speech CLI では、新しい設定は、現在の作業ディレクトリに作成される新しい `./spx/data` サブディレクトリに保存されます。
構成値をシークする際は、Speech CLI によって現在の作業ディレクトリ、次に `./spx/data` のデータストア、その次に `spx` バイナリの最後の読み取り専用データストアを含むその他のデータストアが検索されます。
以前は、データストアを使用して `@key` 値と `@region` 値を保存していたため、各コマンド ライン呼び出しでそれらを指定する必要はありませんでした。
さらに、構成ファイルを使用して独自の構成設定を保存したり、これらを使用して実行時に生成された URL やその他の動的コンテンツを渡したりすることもできます。

このセクションでは、ローカル データストア内の構成ファイルを使用して、`spx config` を使用してコマンド設定を格納およびフェッチし、`--output` オプションを使用して Speech CLI からの出力を格納する方法を示します。

次の例では、`@my.defaults` 構成ファイルをクリアし、ファイル内の **key** と **region** のキーと値のペアを追加し、`spx recognize` の呼び出しでこの構成を使用します。

```shell
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

構成ファイルに動的コンテンツを書き込むこともできます。 たとえば、次のコマンドでは、カスタム音声モデルを作成し、新しいモデルの URL を構成ファイルに格納しています。 次のコマンドでは、その URL のモデルが使用できる状態になるまで待機してから制御を返します。

```shell
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

次の例では、2 つの URL を `@my.datasets.txt` 構成ファイルに書き込みます。
このシナリオでは、`--output` にオプションの **add** キーワードを含めることによって、構成ファイルを作成したり、既存の構成ファイルに追加したりできます。


```shell
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

既定の構成ファイル (コマンドに固有の既定の設定の場合は `@spx.default`、`@default.config`、および `@*.default.config`) の使用法など、データストア ファイルの詳細については、次のコマンドを入力してください。

```shell
spx help advanced setup
```

## <a name="batch-operations"></a>バッチ操作

前のセクションのコマンドは、音声サービスがどのように機能するかをすばやく確認するのに最適です。 ただし、ユースケースが合致しているかどうかを評価する際、さまざまなシナリオがこのサービスによってどのように処理されるかを確認するために、既存の入力範囲に対してバッチ操作の実行が必要になる場合があります。 このセクションでは、次の方法について説明します。

* オーディオ ファイルのディレクトリでバッチ音声認識を実行する
* `.tsv` ファイルを反復処理し、バッチ テキスト読み上げ合成を実行する

## <a name="batch-speech-recognition"></a>バッチ音声認識

オーディオ ファイルのディレクトリがある場合は、Speech CLI を使用して簡単にバッチ音声認識を実行できます。 次のコマンドを実行して、`--files` を使ってディレクトリを指定します。 この例では、ディレクトリに `\*.wav` を追加して、そのディレクトリに存在するすべての `.wav` ファイルを認識します。 さらに、`--threads` 引数を指定して、認識を 10 個の並列スレッドで実行します。

> [!NOTE]
> `--threads` 引数は、次のセクションの `spx synthesize` コマンドでも使用できますが、使用可能なスレッドは CPU とその現在の負荷の割合によって変化します。

```shell
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

認識された音声出力は、`--output file` 引数を使用して `speech_output.tsv` に書き込まれます。 出力ファイルの構造の例を次に示します。

```output
audio.input.id    recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="synthesize-speech-to-a-file"></a>音声をファイルに合成する

次のコマンドを実行して、スピーカーからの出力を `.wav` ファイルに変更します。

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Speech CLI では、`greetings.wav` オーディオ ファイルに英語の自然言語を生成します。
Windows では、`start greetings.wav` と入力すると、オーディオファイルを再生できます。


## <a name="batch-text-to-speech-synthesis"></a>バッチ テキスト読み上げ合成

バッチ テキスト読み上げを実行する最も簡単な方法は、新しい `.tsv` (タブ区切り値) ファイルを作成し、Speech CLI で `--foreach` コマンドを利用することです。 次のファイル `text_synthesis.tsv` を確認してください。

```output
audio.output    text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

 次に、`text_synthesis.tsv` を指すコマンドを実行し、各 `text` フィールドで合成を実行し、対応する `audio.output` パスに `.wav` ファイルとして結果を書き込みます。 

```shell
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

このコマンドは、`.tsv` ファイルの **各** レコードに対して `spx synthesize --text Sample text to synthesize --audio output C:\batch_wav_output\wav_1.wav` を実行するのと同じです。 注意すべき点がいくつかあります。

* 列ヘッダーの `audio.output` と `text` はそれぞれ、コマンド ライン引数の `--audio output` と `--text` に対応します。 `--audio output` のようなマルチパートのコマンド ライン引数は、スペースなし、先頭ダッシュなし、文字列区切りのピリオド (例: `audio.output`) を使用してファイル内で書式設定する必要があります。 その他の既存のコマンド ライン引数は、このパターンを使用して追加列としてファイルに追加できます。
* ファイルがこのように書式設定されている場合は、追加の引数を `--foreach` に渡す必要はありません。
* 必ず、`.tsv` 内の各値を **タブ** で区切ってください。

ただし、次の例のような `.tsv` ファイルがあり、列ヘッダーがコマンド ライン引数と **一致しない** 場合は、次のようになります。

```output
wav_path    str_text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

次の構文を使用して、`--foreach` 呼び出しでこれらのフィールド名を正しい引数にオーバーライドできます。 これは上記と同じ呼び出しです。

```shell
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>次のステップ

* Speech CLI を使用して[音声認識](get-started-speech-to-text.md?pivots=programmer-tool-spx)または[音声合成](get-started-text-to-speech.md?pivots=programmer-tool-spx)のクイックスタートを完了します。

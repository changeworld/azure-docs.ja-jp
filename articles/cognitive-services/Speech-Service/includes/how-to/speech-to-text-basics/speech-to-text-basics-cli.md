---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/08/2020
ms.author: trbye
ms.openlocfilehash: 9c2c46040dd741253e9a68855c4dca89c1dc9a9a
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89570581"
---
[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="speech-to-text-from-microphone"></a>マイクからの音声テキスト変換

PC マイクを電源に接続してオンにして、マイクを使用する可能性のあるすべてのアプリをオフにします。 コンピューターによっては、マイクが内蔵されているものや、Bluetooth デバイスの構成が必要なものもあります。

Speech CLI を実行して、マイクからの音声を認識する準備ができました。 コマンド ラインから、Speech CLI バイナリ ファイルが含まれるディレクトリに変更して、次のコマンドを実行します。

```bash
spx recognize --microphone
```

> [!NOTE]
> Speech CLI の既定値は英語です。 [「音声テキスト変換」表から](../../../../language-support.md)別の言語を選択できます。
> たとえば、ドイツ語の音声を認識するには、`--source de-DE` を追加します。

マイクに向かって話すと、自分が発した言葉がテキストに文字起こしされ、リアルタイムで表示されます。 Speech CLI は、無音の状態が一定の時間続いた後、または Ctrl + C キーを押したときに停止します。

## <a name="speech-to-text-from-audio-file"></a>オーディオ ファイルからの音声テキスト変換

Speech CLI では、多くのファイル形式と自然言語で音声認識を行うことができます。 この例では、英語の音声が含まれる WAV ファイル (16 kHz、8 kHz、16 ビット、mono PCM) を使用できます。 または、クイック サンプルが必要な場合、<a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span></a> ファイルをダウンロードし、それを Speech CLI バイナリ ファイルと同じディレクトリにコピーします。

これで Speech CLI を実行する準備ができました。次のコマンドを実行すると、音声ファイルで検出された音声が認識されます。

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> Speech CLI の既定値は英語です。 [「音声テキスト変換」表から](../../../../language-support.md)別の言語を選択できます。
> たとえば、ドイツ語の音声を認識するには、`--source de-DE` を追加します。

Speech CLI では、音声がテキストに文字起こしされて、画面に表示されます。
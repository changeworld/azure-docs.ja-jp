---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 09/08/2020
ms.author: eur
ms.openlocfilehash: ae12aaf1fe15554246c9b05f9e4839305e1419b7
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501889"
---
Speech Service の中核となる機能の 1 つは、人間の音声を認識して文字起こしをする機能です (多くの場合、音声テキスト変換と呼ばれます)。 このクイックスタートでは、アプリや製品で Speech CLI を使用して、高品質の音声テキスト変換を実行する方法について説明します。

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

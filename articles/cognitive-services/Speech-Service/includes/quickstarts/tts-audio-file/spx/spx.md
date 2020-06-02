---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: bd48618a520f547c72bfba7ff04ae6a249d0a673
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806465"
---
## <a name="run-the-speech-cli"></a>Speech CLI を実行する

Speech CLI を実行して、音声をテキストから新しいオーディオ ファイルに合成する準備ができました。

コマンド ラインから、Speech CLI バイナリ ファイルが含まれるディレクトリに変更して、次のように入力します。

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Speech CLI では、`greetings.wav` オーディオ ファイルに英語の自然言語を生成します。
Windows では、`start greetings.wav` と入力すると、オーディオファイルを再生できます。

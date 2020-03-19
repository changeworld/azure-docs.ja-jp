---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 788903d802ca47c763734e7cf6ddbbf3b0032203
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943840"
---
圧縮オーディオ形式で Speech Services にストリーミングするには、`SPXPullAudioInputStream` または `SPXPushAudioInputStream` を作成します。

以下のスニペットは、ストリームの圧縮形式として MP3 を指定して、`SPXPushAudioInputStream` のインスタンスから `SPXAudioConfiguration` を作成する方法を示しています。

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

次のスニペットは、圧縮オーディオ データをファイルから読み取り、`SPXPushAudioInputStream` に追加する方法を示しています。

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]

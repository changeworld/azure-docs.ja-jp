---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: f0baac79bfbfc06b24d692caef87a20013c4654c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422283"
---
圧縮オーディオ形式で Speech Services にストリーミングするには、`SPXPullAudioInputStream` または `SPXPushAudioInputStream` を作成します。

以下のスニペットは、ストリームの圧縮形式として MP3 を指定して、`SPXPushAudioInputStream` のインスタンスから `SPXAudioConfiguration` を作成する方法を示しています。

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

次のスニペットは、圧縮オーディオ データをファイルから読み取り、`SPXPushAudioInputStream` に追加する方法を示しています。

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]

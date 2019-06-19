---
title: 音声合成マークアップ言語 (SSML) - Speech Services
titleSuffix: Azure Cognitive Services
description: 音声合成マークアップ言語を使用して、テキスト読み上げの発音と韻律を制御します。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: a748250a25c483f61489351943e8ef29a5d5edbe
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165066"
---
# <a name="speech-synthesis-markup-language-ssml"></a>音声合成マークアップ言語 (SSML)

音声合成マークアップ言語 (SSML) は、XML ベースのマークアップ言語で、テキスト読み上げの発音および "*韻律*" を制御する方法を提供します。 韻律とは、読み上げのリズムとピッチを指し、言うなれば音楽です。 単語の発音を指定したり、数字を解釈するためのヒントを提供したりできます。また、休止の挿入や、ピッチ、音量、速度などの制御を行うことができます。 詳細については、「[Speech Synthesis Markup Language (SSML) Version 1.0 (音声合成マークアップ言語 (SSML) バージョン 1.0)](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/)」を参照してください。 

サポートされている言語、ロケール、および音声 (ニューラルと標準) の完全な一覧については、[言語のサポート](language-support.md#text-to-speech)に関するページを参照してください。

以降のセクションでは、一般的な音声合成タスクのサンプルを示します。

## <a name="adjust-speaking-style-for-neural-voices"></a>ニューラル音声の話し方を調整する

いずれかのニューラル音声を使用するときに、SSML を使用して話し方を調整できます。

既定では、テキスト読み上げサービスはニュートラル スタイルでテキストを合成します。 ニューラル音声は、さまざまな話し方でテキストを合成音声に変換する `<mstts:express-as>` 要素で SSML を拡張します。 現在、スタイル タグは次の音声でのみサポートされています。

* `en-US-JessaNeural` 
* `zh-CN-XiaoxiaoNeural`

話し方の変更は、文章レベルで適用できます。 スタイルは、音声によって異なります。 スタイルの種類がサポートされていない場合、サービスは合成音声を既定のニュートラル スタイルとして返します。

| 音声 | Style | 説明 | 
|-----------|-----------------|----------|
| `en-US-JessaNeural` | type=`cheerful` | ポジティブで幸せな感情を示します |
| | type=`empathy` | 思いやりと理解を示します |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | ニュース放送に似たフォーマルなトーンを示します |
| | type=`sentiment` | 感動的なメッセージやストーリーを伝えます |

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
<voice name='en-US-JessaNeural'>
<mstts:express-as type="cheerful"> 
    That'd be just amazing! 
</mstts:express-as></voice></speak>
```

## <a name="add-a-break"></a>中断を追加する
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>読み上げ速度を変更する

読み上げ速度は、標準の音声に対して単語または文章レベルで適用できます。 ただし、ニューラル音声の場合、読み上げ速度は文章レベルでのみ適用できます。

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Guy24kRUS'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>発音
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>音量を変更する

音量変更は、標準の音声に対して単語または文章レベルで適用できます。 ただし、ニューラル音声の場合、音量変更は文章レベルでのみ適用できます。

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>ピッチを変更する

ピッチ変更は、標準の音声に対して単語または文章レベルで適用できます。 ただし、ニューラル音声の場合、ピッチ変更は文章レベルでのみ適用できます。

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='en-US-Guy24kRUS'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>ピッチ曲線を変更する

> [!IMPORTANT]
> ピッチ曲線の変更は、ニューラル音声についてはサポートされていません。

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>複数の音声の使用
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    Good morning!
</voice>
<voice  name='en-US-Guy24kRUS'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>次の手順

* [言語サポート: 音声、ロケール、言語](language-support.md)

---
title: 音声合成マークアップ言語 - Speech Services
titleSuffix: Azure Cognitive Services
description: 音声合成マークアップ言語を使用して、テキスト読み上げの発音と韻律を制御します。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: f6a2d6a35200bc4dec169aae72415c1c2904c465
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53341135"
---
# <a name="speech-synthesis-markup-language"></a>音声合成マークアップ言語

音声合成マークアップ言語 (SSML) は、XML ベースのマークアップ言語で、テキスト読み上げの発音および "*韻律*" を制御する方法を提供します。 韻律とは、読み上げのリズムとピッチを指し、言うなれば音楽です。 単語の発音を指定したり、数字を解釈するためのヒントを提供したりできます。また、休止の挿入や、ピッチ、音量、速度などの制御を行うことができます。 詳細については、「[Speech Synthesis Markup Language (SSML) Version 1.0 (音声合成マークアップ言語 (SSML) バージョン 1.0)](http://www.w3.org/TR/2009/REC-speech-synthesis-20090303/)」を参照してください。

サポートされている言語、ロケール、および音声 (ニューラルと標準) の完全な一覧については、[言語のサポート](language-support.md#text-to-speech)に関するページを参照してください。

以降のセクションでは、一般的な音声合成タスクのサンプルを示します。

>[!IMPORTANT]
> 現時点では、韻律のタグ付けは標準音声でのみ利用できます。

## <a name="add-a-break"></a>中断を追加する
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>読み上げ速度を変更する
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>発音
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>音量を変更する
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>ピッチを変更する
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>ピッチ曲線を変更する
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>複数の音声の使用
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Good morning!
</voice>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>次の手順

* [言語サポート: 音声、ロケール、言語](language-support.md)

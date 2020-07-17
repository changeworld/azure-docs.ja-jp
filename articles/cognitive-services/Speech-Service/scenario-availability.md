---
title: シナリオの可用性 - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech SDK には、さまざまなプログラミング言語と環境にわたる多数のシナリオがあります。 まだ、すべてのシナリオがすべてのプログラミング言語またはすべての環境で利用できるわけではありません。 各シナリオの利用可否は以下のとおりです。
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cbasoglu
ms.openlocfilehash: a72e5055a3b0f858a732ec28eeab511b09dd450c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400142"
---
# <a name="scenario-availability"></a>シナリオの利用可否

Speech SDK には、さまざまなプログラミング言語と環境にわたる多数のシナリオがあります。 まだ、すべてのシナリオがすべてのプログラミング言語またはすべての環境で利用できるわけではありません。 各シナリオの利用可否は以下のとおりです。

- **音声認識 (SR)、フレーズ リスト、意図、翻訳、オンプレミス コンテナー**
  - C++/Windows、Linux、および macOS
  - C# (Framework および .NET Core)/Windows、UWP、Unity、Xamarin、Linux、および macOS
  - Java (Jre および Android)
  - JavaScript (ブラウザーと NodeJS)
  - Python
  - Swift
  - Objective-C  
- **テキスト読み上げ (TTS)**
  - C++/Windows および Linux
  - C#/Windows および UWP および Unity
  - Java (Jre および Android)
  - Python
  - Swift
  - Objective-C
  - TTS REST API は他のすべての状況で使用できます。
- **キーワード認識 (KWS)**
  - C++/Windows および Linux
  - C#/Windows および Linux
  - Python/Windows および Linux
  - Java/Windows および Linux および Android (Speech Devices SDK)
  - キーワード認識 (KWS) 機能は任意の種類のマイクでも動作する可能性がありますが、公式の KWS サポートは、現時点では Azure Kinect DK ハードウェアまたは Speech Devices SDK 内のマイク アレイに限定されています
- **音声アシスタント**
  - C++/Windows、Linux、および macOS
  - C#/Windows
  - Java/Windows、Linux、macOS、および Android (Speech Devices SDK)
- **会話の文字起こし**
  - C++/Windows および Linux
  - C# (Framework および .NET Core)/Windows および UWP および Linux
  - Java/Windows および Linux および Android (Speech Devices SDK)
- **マルチデバイスの会話**
  - C++/Windows
  - C# (Framework と .NET Core)/Windows
- **コール センターの文字起こし**
  - REST API、任意の状況で使用できます
- **コーデック圧縮オーディオ入力**
  - C++/Linux
  - C#/Linux
  - Java/Linux、Android、および iOS

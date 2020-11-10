---
title: Speech SDK を使用してコーデック圧縮オーディオをストリーミングする - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech SDK を使用して圧縮オーディオを Speech Service にストリーミングする方法について学習します。 Linux の場合は C++、C#、Java で、Android の場合は Java で、iOS の場合は Objective-C で使用できます。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-two
ms.openlocfilehash: 7d6a0388507d836050550a3a98255deabb826cde
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128341"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Speech SDK でコーデック圧縮オーディオを使用する

Speech サービス SDK の **圧縮オーディオ入力ストリーム** API では、`PullStream` または `PushStream` を使用して、圧縮オーディオを Speech Services にストリーミングする方法を提供します。

プラットフォーム | 言語 | サポートされている GStreamer のバージョン
| :--- | ---: | :---:
Windows (UWP を除く)  | C++、C#、Java、Python | [1.15.1](https://gstreamer.freedesktop.org/data/pkg/windows/1.15.1/)
Linux  | C++、C#、Java、Python | [サポートされている Linux ディストリビューションとターゲット アーキテクチャ](~/articles/cognitive-services/speech-service/speech-sdk.md)
Android  | Java | [1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/)

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>圧縮されたオーディオ入力に必要な Speech SDK バージョン
* RHEL 8 と CentOS 8 では、Speech SDK バージョン 1.10.0 以降が必要です
* Windows では、Speech SDK バージョン 1.11.0 以降が必要です。

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>前提条件

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>コーデック圧縮オーディオ入力を使用するコード例

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [音声を認識する方法](quickstarts/speech-to-text-from-microphone.md)

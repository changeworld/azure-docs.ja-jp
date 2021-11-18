---
title: Speech SDK を使用してコーデック圧縮オーディオをストリーミングする - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech SDK を使用して圧縮オーディオを Speech Service にストリーミングする方法について学習します。 Linux の場合は C++、C#、Java で、Android の場合は Java で、iOS の場合は Objective-C で使用できます。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-eight
ms.openlocfilehash: 1f04c8c525e094d5e0980c1683632d34f3372580
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551921"
---
# <a name="use-codec-compressed-audio-input"></a>コーデック圧縮音声入力の使用

Speech SDK と Speech CLI では、GStreamer を使用して圧縮されたオーディオ形式を受け入れることもできます。 GStreamer によってオーディオの圧縮が解除された後、ネットワークを介して RAW PCM として Speech サービスに送信されます。

プラットフォーム | 言語 | サポートされている GStreamer のバージョン
| :--- | ---: | :---:
Linux  | C++、C#、Java、Python、Go | [サポートされている Linux ディストリビューションとターゲット アーキテクチャ](~/articles/cognitive-services/speech-service/speech-sdk.md) 
Windows (UWP を除く) | C++、C#、Java、Python | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/windows/1.18.3/msvc/gstreamer-1.0-msvc-x86_64-1.18.3.msi) 
Android  | Java | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/android/1.18.3/) 

## <a name="installing-gstreamer-on-linux"></a>Linux への GStreamer のインストール

詳細については、[Linux へのインストール手順](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c)に関するページを参照してください。  

```sh
sudo apt install libgstreamer1.0-0 \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```
## <a name="installing-gstreamer-on-windows"></a>Windows への GStreamer のインストール

詳細については、[Windows へのインストール手順](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)に関するページを参照してください。 

* c:\gstreamer フォルダーを作成します。
* [インストーラー](https://gstreamer.freedesktop.org/data/pkg/windows/1.18.3/msvc/gstreamer-1.0-msvc-x86_64-1.18.3.msi)をダウンロードします。 
* インストーラーを c:\gstreamer にコピーします。
* PowerShell を管理者として開きます。
* PowerShell で次のコマンドを実行します。

```powershell
cd c:\gstreamer
msiexec /passive INSTALLLEVEL=1000 INSTALLDIR=C:\gstreamer /i gstreamer-1.0-msvc-x86_64-1.18.3.msi
```
* システム変数 GST_PLUGIN_PATH を追加し、値を C:\gstreamer\1.0\msvc_x86_64\lib\gstreamer-1.0 に設定します。
* システム変数 GSTREAMER_ROOT_X86_64 を追加し、値を C:\gstreamer\1.0\msvc_x86_64 に設定します。
* パス変数に別のエントリを追加し、C:\gstreamer\1.0\msvc_x86_64\bin に設定します。
* コンピューターを再起動します。

## <a name="using-gstreamer-in-android"></a>Android での GStreamer の使用
libgstreamer_android.so のビルドの詳細については、上記の [Java] タブを参照してください。 

詳細については、[Android へのインストール手順](https://gstreamer.freedesktop.org/documentation/installing/for-android-development.html?gi-language=c)に関するページを参照してください。 

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>圧縮されたオーディオ入力に必要な Speech SDK バージョン
* RHEL 8 と CentOS 8 では、Speech SDK バージョン 1.10.0 以降が必要です
* Windows では、Speech SDK バージョン 1.11.0 以降が必要です。
* Windows と Android の最新の GStreamer には、Speech SDK バージョン 1.16.0 以降。

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="gstreamer-required-to-handle-compressed-audio"></a>圧縮されたオーディオを処理するために必要な GStreamer

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

::: zone pivot="programming-language-go"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/go/prerequisites.md)]
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

::: zone pivot="programming-language-go"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/go/examples.md)]
::: zone-end

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [音声を認識する方法](./get-started-speech-to-text.md)

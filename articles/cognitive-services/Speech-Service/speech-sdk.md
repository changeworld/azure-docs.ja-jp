---
title: Speech SDK について - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech ソフトウェア開発キット (SDK) は、Speech サービスの機能に対するネイティブ アクセス権をアプリケーションに提供します。これによりソフトウェアの開発が容易になります。 この記事では、Windows、Linux、Android 向けの SDK について、さらに詳しく取り上げます。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: wolfma
ms.openlocfilehash: ea879cbfc3b9b1d1a627add52f26a473aca53cdf
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759877"
---
# <a name="about-the-speech-sdk"></a>Speech SDK について

Speech ソフトウェア開発キット (SDK) は、音声認識対応のソフトウェア開発を支援します。アプリケーションから SDK を利用することで、Speech サービスの各種機能を利用することができます。 現在、これらの SDK は**音声テキスト変換**、**テキスト音声変換**、**音声翻訳**、**意図認識**、および **Bot Framework の Direct Line Speech チャネル**へのアクセスを提供しています。

Speech SDK を使用することにより、マイクからの音声のキャプチャや、ストリームからの読み取り、ストレージからの音声ファイルへのアクセスを簡単に行うことができます。 Speech SDK では、音声認識用として、WAV/PCM 16 ビット、16 kHz/8 kHz、単一チャネル オーディオがサポートされています。 [音声変換 REST エンドポイント](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) や [バッチ文字起こしサービス](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats)を使用したオーディオ形式もサポートされています。

機能やサポートされているプラットフォームについての概要は、[エントリ ページ](https://aka.ms/csspeech) ドキュメントで確認できます。

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>SDK の取得

### <a name="windows"></a>Windows

> [!WARNING]
> Speech SDK では、Windows 10 以降のバージョンがサポートされます。 これより前の Windows バージョンは**サポートされていません**。

Windows の場合、次の言語がサポートされています。

* C# (UWP と .NET)、C++: Speech SDK NuGet パッケージの最新バージョンを参照および使用することができます。 パッケージには、32 ビットおよび 64 ビットのクライアント ライブラリとマネージ (.NET) ライブラリが含まれています。 SDK は、NuGet の [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) を使用して Visual Studio にインストールできます。

* Java:Speech SDK Maven パッケージの最新バージョンを参照および使用することができます。これは Windows x64 のみをサポートします。 Maven プロジェクトでは、追加のリポジトリとして `https://csspeechstorage.blob.core.windows.net/maven/` を追加し、依存関係として `com.microsoft.cognitiveservices.speech:client-sdk:1.8.0` を参照します。

### <a name="linux"></a>Linux

> [!NOTE]
> 現在、次のターゲット アーキテクチャでは Ubuntu 16.04、Ubuntu 18.04、Debian 9 のみがサポートされています。
> - C++ 開発用の x86、x64、ARM64
> - Java 用の x64、ARM64
> - .NET Core および Python 用の x64

次のシェル コマンドを実行して、必須のライブラリがインストールされていることを確認します。

Ubuntu の場合:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Debian 9 の場合:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

* C#:Speech SDK NuGet パッケージの最新バージョンを参照および使用することができます。 SDK を参照するには、プロジェクトに次のパッケージ参照を追加します。

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.8.0" />
  ```

* Java:Speech SDK Maven パッケージの最新バージョンを参照および使用することができます。 Maven プロジェクトでは、追加のリポジトリとして `https://csspeechstorage.blob.core.windows.net/maven/` を追加し、依存関係として `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` を参照します。

* C++: [.tar パッケージ](https://aka.ms/csspeech/linuxbinary)として SDK をダウンロードし、ファイルを任意のディレクトリにアンパックします。 SDK のフォルダー構造を次の表に示します。

  |Path|[説明]|
  |-|-|
  |`license.md`|ライセンス|
  |`ThirdPartyNotices.md`|サード パーティに関する通知|
  |`include`|C および C++ 用のヘッダー ファイル|
  |`lib/x64`|アプリケーションとのリンクのためのネイティブ x64 ライブラリ|
  |`lib/x86`|アプリケーションとのリンクのためのネイティブ x86 ライブラリ|

  アプリケーションを作成するには、必須のバイナリ (およびライブラリ) を開発環境にコピーまたは移動し、 必要に応じてそれらをビルド プロセスに含めます。

### <a name="android"></a>Android

Android 用 Java SDK は、必要なライブラリと必要な Android アクセス許可を含む [AAR (Android ライブラリ)](https://developer.android.com/studio/projects/android-library) としてパッケージ化されます。 これは、`https://csspeechstorage.blob.core.windows.net/maven/` にある Maven リポジトリでパッケージ `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` としてホストされます。

このパッケージを Android Studio プロジェクトから使用するには、次の変更を行います。

* プロジェクト レベルの build.gradle ファイルで、`repository` セクションに次を追加します。

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* モジュール レベルの build.gradle ファイルで、`dependencies` セクションに次を追加します。

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

Java SDK は [Speech Devices SDK](speech-devices-sdk.md) の一部でもあります。

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>次のステップ

* [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
* [C# で音声を認識する方法を確認する](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)

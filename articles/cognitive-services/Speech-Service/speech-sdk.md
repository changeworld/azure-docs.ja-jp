---
title: Cognitive Services Speech SDK について
description: Speech サービスで使用できる SDK の概要。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/17/2018
ms.author: v-jerkin
ms.openlocfilehash: c7eaa2aa37b05bd0e125e1841357979af4f6763a
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39326061"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>Cognitive Services Speech SDK について

Cognitive Services Speech ソフトウェア開発キット (SDK) は、Speech サービスの機能に対するネイティブ アクセス権をアプリケーションに提供します。これによりソフトウェアの開発が容易になります。 現時点で、SDK からは、**音声テキスト変換**、**音声翻訳**、および**意図認識**にアクセスできます。

[!include[Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>SDK の取得

### <a name="get-the-windows-sdk"></a>Windows SDK を取得する

Speech SDK の Windows バージョンには、32 ビットおよび 64 ビットの C/C++ クライアント ライブラリと、C# で使用するためのマネージド (.NET) ライブラリが含まれています。 この SDK は NuGet を使用して Visual Studio でインストールできます。単純に `Microsoft.CognitiveServices.Speech` を検索します。

### <a name="get-the-linux-sdk"></a>Linux SDK を取得する

次のシェル コマンドを実行して、必須のコンパイラおよびライブラリがあることを確認します。

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

> [!NOTE]
> 以降の手順では、PC (x86 または x64) で Ubuntu 16.04 が実行されていることを前提としています。 異なる Ubuntu バージョン、または異なる Linux ディストリビューションでは、これらの手順を環境に合わせて調整します。

次に、[SDK をダウンロードし](https://aka.ms/csspeech/linuxbinary)、ファイルを任意のディレクトリにアンパックします。 SDK のフォルダー構造を下表に示します。

|Path|説明|
|-|-|
|`license.md`|ライセンス|
|`third-party-notices.md`|サード パーティに関する通知|
|`include`|C および C++ 用のヘッダー ファイル|
|`lib/x64`|アプリケーションとのリンクのためのネイティブ x64 ライブラリ|
|`lib/x86`|アプリケーションとのリンクのためのネイティブ x86 ライブラリ|

アプリケーションを作成するには、必須のバイナリ (およびライブラリ) を開発環境にコピーまたは移動し、必要に応じてそれらをビルド プロセスに含めます。

### <a name="get-the-java-sdk"></a>Java SDK を取得する

Android 用の Java SDK は、[AAR (Android ライブラリ)](https://developer.android.com/studio/projects/android-library) としてパッケージ化されています。これには、必要なライブラリや、それを使用するために必要な Android アクセス許可が含まれています。
これは、`https://csspeechstorage.blob.core.windows.net/maven/` にある Maven リポジトリでパッケージ `com.microsoft.cognitiveservices.speech:client-sdk:0.5.0` としてホストされます。
このパッケージを Android Studio プロジェクトから使用するには、次の変更を行います。

* プロジェクト レベルの `build.gradle` ファイルで、`repository` セクションに次を追加します。

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* モジュール レベルの `build.gradle` ファイルで、`dependencies` セクションに次を追加します。

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:0.5.0'
  ```

Java SDK は [Speech Devices SDK](speech-devices-sdk.md) の一部でもあります。

[!include[Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>次の手順

* [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
* [C# で音声を認識する方法を確認する](quickstart-csharp-dotnet-windows.md)

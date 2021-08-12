---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 18d363ba216c43e01e1238ea1200db99703939d0
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110059197"
---
:::row:::
    :::column span="3":::
        Android用Java SDKは、必要なライブラリとAndroid許可を含む <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR　(Android ライブラリ） </a>としてパッケージ化されています。 これは、`https://csspeechstorage.blob.core.windows.net/maven/` にある Maven リポジトリでパッケージ `com.microsoft.cognitiveservices.speech:client-sdk:1.17.0` としてホストされます。 ([GitHub リポジトリを検索](https://github.com/Azure-Samples/cognitive-services-speech-sdk/search?q=com.microsoft.cognitiveservices.speech%3Aclient-sdk)して、1.17.0 が最新バージョンであることを確認します)。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

このパッケージを Android Studio プロジェクトから使用するには、次の変更を行います。

1. プロジェクトレベルでの *build.grandle* ファイルで、`repositories` セクションに次のものを追加します：
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. モジュールレベルでの *build.grandle* ファイルで、`dependencies` セクションに次のものを追加します：
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.17.0'
  ```

Java SDK は [Speech Devices SDK](../speech-devices-sdk.md) の一部でもあります。

#### <a name="additional-resources"></a>その他のリソース

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Android 固有の Java クイックスタートソースコード</a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Java Runtime (JRE) のクイックスタートソースコード</a>

---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 59835890a5baaf050868951ea683fd97b795ce3e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656499"
---
:::row:::
    :::column span="3":::
        Android用Java SDKは、必要なライブラリとAndroid許可を含む <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR　(Android ライブラリ） <span class="docon docon-navigate-external x-hidden-focus"></span></a>としてパッケージ化されています。 これは、`https://csspeechstorage.blob.core.windows.net/maven/` にある Maven リポジトリでパッケージ `com.microsoft.cognitiveservices.speech:client-sdk:1.11.0` としてホストされます。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

このパッケージを Android Studio プロジェクトから使用するには、次の変更を行います。

1. プロジェクトレベルでの*build.grandle*ファイルで、`repository` セクションに次のものを追加します：
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. モジュールレベルでの*build.grandle*ファイルで、`dependencies` セクションに次のものを追加します：
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.11.0'
  ```

Java SDK は [Speech Devices SDK](../speech-devices-sdk.md) の一部でもあります。

#### <a name="additional-resources"></a>その他のリソース

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Android 固有の Java クイックスタートソースコード<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Java Runtime (JRE) のクイックスタートソースコード<span class="docon docon-navigate-external x-hidden-focus"></span></a>
---
title: クイック スタート:Java (Windows、Linux、macOS) 用 Speech SDK のプラットフォームの設定 - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech サービス SDK を使用して Java (Windows、Linux、macOS) 用にプラットフォームを設定するには、このガイドを使用します。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.custom: devx-track-java
ms.author: eur
ms.openlocfilehash: 9f0323d92a32938fd8850a06d0f86b034a59dd4b
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252278"
---
このガイドでは、64 ビット Java 8 JRE 用 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) をインストールする方法について説明します。 このパッケージ名の使用を自分で開始する場合、Java SDK は Maven Central Repository で使用できません。 Gradle または `pom.xml` 依存関係ファイルを使用しているかどうかに関係なく、`https://csspeechstorage.blob.core.windows.net/maven/` を指すカスタム リポジトリを追加する必要があります (パッケージ名については以下を参照してください)。

> [!NOTE]
> Speech Devices SDK および Roobo デバイスについては、[Speech Devices SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md) を参照してください。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

- 以下のオペレーティング システム用の Java Speech SDK パッケージを入手できます。
  - Windows: 64 ビットのみ
  - Mac: macOS X version 10.14　以降
  - Linux。[サポートされている Linux ディストリビューションとターゲット アーキテクチャ](~/articles/cognitive-services/speech-service/speech-sdk.md)の一覧を参照してください。

## <a name="prerequisites"></a>前提条件

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) または [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/) (Java が既にインストールされている必要があります)

- Windows では、お使いのプラットフォームに対応した [Microsoft Visual Studio 2019 の Visual C++ 再頒布可能パッケージ](https://support.microsoft.com/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0)が必要です。 これを初めてインストールする場合、再起動が必要になる場合があります。

- Linux については、[システム要件とセットアップ手順](~/articles/cognitive-services/speech-service/speech-sdk.md#get-the-speech-sdk)に関する記事を参照してください。

## <a name="gradle-config"></a>Gradle の構成

Gradle の構成には、カスタム リポジトリと、依存関係拡張子 `.jar` への明示的な参照の両方が必要です。

```groovy
// build.gradle

repositories {
    maven {
        url "https://csspeechstorage.blob.core.windows.net/maven/"
    }
}

dependencies {
    implementation group: 'com.microsoft.cognitiveservices.speech', name: 'client-sdk', version: "1.18.0", ext: "jar"
}
```

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Eclipse プロジェクトを作成して Speech SDK をインストールする

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>次のステップ

[!INCLUDE [windows](../quickstart-list.md)]

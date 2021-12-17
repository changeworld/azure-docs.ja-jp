---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: eur
ms.openlocfilehash: 642fa02cf8f40d7cc6a39a1951499cb7921ed100
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252299"
---
macOS 用に開発する場合は、次の音声 SDK を使用できます。

- Objective-C/Swift Speech SDK は、Mac x64 および ARM ベースのシリコン用の CocoaPod パッケージとしてネイティブに利用できます。 Objective-C Speech SDK と Swift の使用の詳細については、「<a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Objective-C を Swift にインポートする</a>」を参照してください。
- .NET Speech SDK は、NuGet パッケージを介して使用でき、**Xamarin.Mac** および **Unity** のアプリケーション フレームワークで利用できます。
- Python Speech SDK は、Python バージョン 3.7 以降用の PyPI モジュールとして提供されています。
- Java Speech SDK は、Maven リポジトリを介して JAR パッケージとして使用できます。

### <a name="system-requirements"></a>システム要件

- macOS version 10.14以降

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        macOS Cocoアポストロフィ d パッケージは、 <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (またはそれ以降) </a>統合開発環境 (IDE) と共にダウンロードして使用することができます。 <a href="https://aka.ms/csspeech/macosbinary" target="_blank">まず、バイナリ Cocoアポストロフィ </a>をダウンロードします。 使用目的に合わせて同じディレクトリ内のポッドを抽出し、*Podfile* を作成して、`pod`を `target`として一覧表示します。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :osx, 10.14
use_frameworks!

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.15.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin. Mac は、.NET 開発者向けの完全な macOS SDK を公開しC#を使用してネイティブ Mac アプリケーションを構築します。 詳細については、<a href="/xamarin/mac/" target="_blank">Xamarin.Mac</a>を参照してください。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>その他のリソース

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS音声 SDK クイックスタートのObjective-Cソースコード</a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">macOS音声SDK クイックスタート Swift ソースコード</a>

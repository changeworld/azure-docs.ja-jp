---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 7ce193c2c2f5e10a27550da68a4c2d2fdcd1db7f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399918"
---
:::row:::
    :::column span="3":::
        iOS 向けの開発時には、2つの Speech SDK を利用できます。 Objective-C Speech SDK は、iOS CocoaPod パッケージとしてネイティブに使用できます。 また、.NET Speech SDK は .NET Standard 2.0 を実装しているため、Xamarin で使用することもできます。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Objective-C Speech SDK と Swift <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">の使用の詳細については、「Objective-C をSwiftにインポートする 」を参照してください<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

### <a name="system-requirements"></a>システム要件

- macOS version 10.3以降
- 対象 iOS 9.3以降

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        iOS CocoaPod パッケージは、<a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (またはそれ以降) <span class="docon docon-navigate-external x-hidden-focus"></span></a>統合開発環境 (IDE) と共にダウンロードして使用することができます。 まず、<a href="https://aka.ms/csspeech/iosbinary" target="_blank">バイナリ CocoaPod<span class="docon docon-navigate-external x-hidden-focus"></span></a> をダウンロードします。 使用目的に合わせて同じディレクトリ内のポッドを抽出し、*Podfile*を作成して、`pod`を `target`として一覧表示します。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'AppName' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.10.0'
end
```

# <a name="xamarinios"></a>[Xamarin.iOS](#tab/ios-xamarin)

:::row:::
    :::column span="3":::
        Xamarin iOS は、.NET 開発者向けの完全な iOS SDK を公開しています。 Visual Studio でC#またはF#を使用して、完全にネイティブな iOS アプリをビルドします。 詳細は、<a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin.iOS <span class="docon docon-navigate-external x-hidden-focus"></span></a>を参照してください。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp; ❤️ &nbsp;         <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>その他のリソース

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">iOS Speech SDK クイックスタートのObjective-C ソースコード <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">iOS Speech SDK クイックスタート Swift ソースコード<span class="docon docon-navigate-external x-hidden-focus"></span></a>
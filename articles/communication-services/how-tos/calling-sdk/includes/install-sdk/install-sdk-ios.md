---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 0ca40e760e34be8ef4299e042949f7a36b54f36f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837815"
---
## <a name="set-up-your-system"></a>システムを設定する

### <a name="create-the-xcode-project"></a>Xcode プロジェクトを作成する

Xcode で、新しい iOS プロジェクトを作成し、 **[単一ビュー アプリ]** テンプレートを選択します。 このクイックスタートでは [SwiftUI フレームワーク](https://developer.apple.com/xcode/swiftui/)を使用します。そのため、 **[言語]** を **[Swift]** に、 **[ユーザー インターフェイス]** を **[SwiftUI]** に設定する必要があります。 

このクイックスタートでは、単体テストと UI テストは作成しません。 **[Include Unit Tests]\(単体テストを含める\)** と **[Include UI Tests]\(UI テストを含める\)** のテキスト ボックスは自由にオフにしてかまいません。

:::image type="content" source="../../../../quickstarts/voice-video-calling/media/ios/xcode-new-ios-project.png" alt-text="Xcode 内にプロジェクトを作成するためのウィンドウを示すスクリーンショット。":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>CocoaPods でパッケージと依存関係をインストールする

1. 次のようにして、アプリケーションのポッドファイルを作成します。

    ```
    platform :ios, '13.0'
    use_frameworks!
    target 'AzureCommunicationCallingSample' do
        pod 'AzureCommunicationCalling', '~> 1.0.0'
    end
    ```
2. `pod install` を実行します。
3. Xcode で `.xcworkspace` を開きます。

### <a name="request-access-to-the-microphone"></a>マイクへのアクセスを要求する

デバイスのマイクにアクセスするには、アプリの情報プロパティ リストを `NSMicrophoneUsageDescription` で更新する必要があります。 関連付けられた値を `string` に設定します。これは、ユーザーからのアクセスを求めるためにシステムによって使用されるダイアログに含められます。

プロジェクト ツリーの `Info.plist` のエントリを右クリックし、 **[Open As]\(形式を指定して開く\)**  >  **[Source Code]\(ソース コード\)** の順に選択します。 最上位の `<dict>` セクションに以下の行を追加してから、ファイルを保存します。

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクトの *ContentView.swift* ファイルを開き、そのファイルの先頭に `import` 宣言を追加して `AzureCommunicationCalling` ライブラリをインポートします。 さらに、`AVFoundation` をインポートします。 これは、コード内のオーディオ アクセス許可要求に必要になります。

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="initialize-callagent"></a>CallAgent を初期化する

`CallClient` から `CallAgent` インスタンスを作成するには、初期化された後に `CallAgent` オブジェクトを非同期に返す `callClient.createCallAgent` メソッドを使用する必要があります。

通話クライアントを作成するには、`CommunicationTokenCredential` オブジェクトを渡す必要があります。

```swift
import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationTokenCredential?
do {
    let options = CommunicationTokenRefreshOptions(initialToken: token, refreshProactively: true, tokenRefresher: self.fetchTokenSync)
    userCredential = try CommunicationTokenCredential(withOptions: options)
} catch {
    updates("Couldn't created Credential object", false)
    initializationDispatchGroup!.leave()
    return
}

// tokenProvider needs to be implemented by Contoso, which fetches a new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

`CallClient` に作成した `CommunicationTokenCredential` オブジェクトを渡し、表示名を設定します。

```swift
self.callClient = CallClient()
let callAgentOptions = CallAgentOptions()
options.displayName = " iOS ACS User"

self.callClient!.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions) { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})
```
---
title: クイック スタート - Azure Communication Services を使用して iOS アプリに通話を追加する
description: このクイックスタートでは、iOS 用の Azure Communication Services Calling SDK を使用する方法について説明します。
author: chpalm
ms.author: rifox
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: be6dbd899f91df5a19878d0491f14b3eb636f8eb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671672"
---
このクイックスタートでは、iOS 用の Azure Communication Services Calling SDK を使用して、通話を開始する方法について説明します。

## <a name="sample-code"></a>サンプル コード

サンプル アプリは [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Voice%20Calling) からダウンロードできます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) を実行しており、有効な開発者証明書がキーチェーンにインストールされている Mac。
- デプロイ済みの Communication Services リソース。 [Communication Services のリソースを作成する](../../../create-communication-resource.md)。
- Azure Communication Service の[ユーザー アクセス トークン](../../../access-tokens.md)。

## <a name="setting-up"></a>設定

### <a name="creating-the-xcode-project"></a>Xcode プロジェクトを作成する

Xcode で、新しい iOS プロジェクトを作成し、 **[単一ビュー アプリ]** テンプレートを選択します。 このチュートリアルでは [SwiftUI フレームワーク](https://developer.apple.com/xcode/swiftui/)を使用します。そのため、 **[言語]** を **[Swift]** に設定し、 **[ユーザー インターフェイス]** を **[SwiftUI]** に設定する必要があります。 このクイック スタートでは、テストは作成しません。 **[Include Tests]\(テストを含める\)** チェック ボックスはオフにしてかまいません。

:::image type="content" source="../../media/ios/xcode-new-ios-project.png" alt-text="Xcode 内で新しいプロジェクトを作成するウィンドウのスクリーンショット。":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>CocoaPods でパッケージと依存関係をインストールする

1. アプリケーションのポッドファイルを作成するために、ターミナルを開いてプロジェクト フォルダーに移動し、次を実行します。

   `pod init`

1. ポッドファイルに次のコードを追加して保存します ("target" がプロジェクトの名前と一致していることを確認してください)。

   ```
   platform :ios, '13.0'
   use_frameworks!

   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0'
   end
   ```

1. `pod install` を実行します。

1. Xcode を使用して `.xcworkspace` を開きます。

### <a name="request-access-to-the-microphone"></a>マイクへのアクセスを要求する

デバイスのマイクにアクセスするには、アプリの情報プロパティ リストを `NSMicrophoneUsageDescription` によって更新する必要があります。 関連付けられた値を `string` に設定します。これは、ユーザーからのアクセスの要求を求めるためにシステムによって使用されるダイアログに含められます。

プロジェクト ツリーの `Info.plist` のエントリを右クリックし、 **[Open As]\(形式を指定して開く\)**  >  **[Source Code]\(ソース コード\)** の順に選択します。 最上位の `<dict>` セクションに以下の行を追加してから、ファイルを保存します。

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクトの **ContentView. swift** ファイルを開き、ファイルの先頭に `import` 宣言を追加して `AzureCommunicationCalling library` をインポートします。 さらに、`AVFoundation` をインポートします。これは、コード内のオーディオ アクセス許可要求に必要になります。

```swift
import AzureCommunicationCalling
import AVFoundation
```

`ContentView` 構造体の実装を、ユーザーが通話を開始して終了できるようにする、いくつかの単純な UI コントロールに置き換えます。 このクイック スタートでは、これらのコントロールにビジネス ロジックをアタッチします。

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                // Add start call logic
            }
        }
    }

    func endCall() {
        // Add end call logic
    }
}
```

## <a name="object-model"></a>オブジェクト モデル

Azure Communication Services Calling SDK の主な機能のいくつかは、次のクラスとインターフェイスによって処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient は、Calling SDK へのメイン エントリ ポイントです。|
| CallAgent | CallAgent は、通話を開始および管理するために使用します。 |
| CommunicationTokenCredential | CommunicationTokenCredential は、CallAgent をインスタンス化するためのトークン資格情報として使用されます。| 
| CommunicationUserIdentifier | CommunicationUserIdentifier はユーザーの ID を表すために使用され、次のいずれかになります: CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication。 |

## <a name="authenticate-the-client"></a>クライアントを認証する

ユーザー アクセス トークンを使用して `CallAgent` インスタンスを初期化します。これにより、電話をかけたり受けたりすることができるようになります。 

次のコードでは、`<USER ACCESS TOKEN>` をリソース用の有効なユーザー アクセス トークンに置き換える必要があります。 まだトークンを入手していない場合は、[ユーザー アクセス トークン](../../../access-tokens.md)に関するドキュメントを参照してください。

**ContentView.swift** で `onAppear` コールバックに次のコードを追加します。

```swift
var userCredential: CommunicationTokenCredential?
do {
    userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = CallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential: userCredential!) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
        return
    }
    else {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```



## <a name="start-a-call"></a>通話を開始する

`startCall` メソッドは、 *[Start Call]\(通話の開始\)* ボタンがタップされたときに実行されるアクションとして設定されます。 `ASACallAgent` を使用して通話を開始するように実装を更新します。

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUserIdentifier(self.callee)]
            self.callAgent?.startCall(participants: callees, options: StartCallOptions()) { (call, error) in
                if (error == nil) {
                    self.call = call
                } else {
                    print("Failed to get call object")
                }
            }
        }
    }
}
```

`StartCallOptions` のプロパティを使用して、通話の初期オプションを設定することもできます (つまり、マイクをミュートした状態で通話を開始できます)。

## <a name="end-a-call"></a>通話を終了する

`endCall` メソッドを実装して、 *[通話の終了]* ボタンがタップされたときに現在の通話を終了します。

```swift
func endCall()
{    
    self.call!.hangUp(options: HangUpOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>コードの実行

iOS シミュレーターでアプリをビルドして実行するには、 **[製品]**  >  **[実行]** の順に選択するか、(&#8984;-R) キーボード ショートカットを使用します。

:::image type="content" source="../../media/ios/quick-start-make-call.png" alt-text="クイック スタート アプリの最終的な外観":::

発信 VOIP 通話を行うには、テキスト フィールドにユーザー ID を指定し、 **[Start Call]\(通話の開始\)** ボタンをタップします。 `8:echo123` を呼び出すとエコー ボットに接続されます。これは、オーディオ デバイスを起動し、デバイスが機能していることを確認する場合に役立ちます。 

> [!NOTE]
> 初めて通話を行うときに、マイクへのアクセスを求めるメッセージが表示されます。 運用環境のアプリケーションでは、`AVAudioSession` API を使用して[アクセス許可の状態を確認し](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources)、アクセス許可が付与されていない場合はアプリケーションの動作を適切に更新する必要があります。

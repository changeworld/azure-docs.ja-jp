---
title: クイックスタート - Azure Communication Services を使用して Microsoft Teams 会議への参加を iOS アプリに追加する
description: このクイックスタートでは、iOS 用の Azure Communication Services Teams Embed ライブラリを使用する方法について説明します。
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 222ae284f77950c729a6a790e2ad29453a9ce34a
ms.sourcegitcommit: b4032c9266effb0bf7eb87379f011c36d7340c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2021
ms.locfileid: "107903169"
---
このクイックスタートでは、iOS 用の Azure Communication Services Teams Embed ライブラリを使用して Microsoft Teams 会議に参加する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、前提となる次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) を実行しており、有効な開発者証明書がキーチェーンにインストールされている Mac。
- デプロイ済みの Communication Services リソース。 [Communication Services のリソースを作成する](../../create-communication-resource.md)。
- Azure Communication Service の[ユーザー アクセス トークン](../../access-tokens.md)。
- ビルド環境にインストールされた [CocoaPods](https://cocoapods.org/)。

## <a name="setting-up"></a>設定

### <a name="creating-the-xcode-project"></a>Xcode プロジェクトを作成する

Xcode で、新しい iOS プロジェクトを作成し、 **[App]\(アプリ\)** テンプレートを選択します。 ここでは、UIKit のストーリーボードを使用します。 このクイックスタートでは、テストは作成しません。 **[Include Tests]\(テストを含める\)** チェック ボックスはオフにしてかまいません。

:::image type="content" source="../media/ios/xcode-new-project-template-select.png" alt-text="Xcode 内で新しいプロジェクトのテンプレートを選択するところを示すスクリーンショット。":::

プロジェクトに `TeamsEmbedGettingStarted` という名前を付けます。

:::image type="content" source="../media/ios/xcode-new-project-details.png" alt-text="Xcode 内での新しいプロジェクトの詳細を示すスクリーンショット。":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>CocoaPods でパッケージと依存関係をインストールする

1. アプリケーションのポッドファイルを作成します。

```
platform :ios, '12.0'
use_frameworks!

target 'TeamsEmbedGettingStarted' do
    pod 'AzureCommunication', '~> 1.0.0-beta.11'
end

azure_libs = [
'AzureCommunication',
'AzureCore']

post_install do |installer|
    installer.pods_project.targets.each do |target|
    if azure_libs.include?(target.name)
        puts "Adding BUILD_LIBRARY_FOR_DISTRIBUTION to #{target.name}"
        target.build_configurations.each do |config|
        xcconfig_path = config.base_configuration_reference.real_path
        File.open(xcconfig_path, "a") {|file| file.puts "BUILD_LIBRARY_FOR_DISTRIBUTION = YES"}
        end
    end
    end
end
```

2. `pod install` を実行します。
3. 生成された `.xcworkspace` を Xcode で開きます。

### <a name="request-access-to-the-microphone-camera-etc"></a>マイク、カメラなどへのアクセスを要求する

デバイスのハードウェアにアクセスするには、アプリの情報プロパティ リストを更新します。 関連付けられた値を `string` に設定します。これは、ユーザーに対してアクセスを求めるためにシステムによって使用されるダイアログに追加されます。

プロジェクト ツリーの `Info.plist` のエントリを右クリックし、 **[Open As]\(形式を指定して開く\)**  >  **[Source Code]\(ソース コード\)** の順に選択します。 最上位の `<dict>` セクションに以下の行を追加してから、ファイルを保存します。

```xml
<key>NSCameraUsageDescription</key>
<string></string>
<key>NSContactsUsageDescription</key>
<string></string>
<key>NSMicrophoneUsageDescription</key>
<string></string>
```

### <a name="add-the-teams-embed-framework"></a>Teams Embed フレームワークを追加する

1. [`MicrosoftTeamsSDK` iOS パッケージ](https://github.com/Azure/communication-teams-embed/releases)をダウンロードします。
2. プロジェクト ルートに `Frameworks` フォルダーを作成します。 例: `\TeamsEmbedGettingStarted\Frameworks\`
3. ダウンロードした `TeamsAppSDK.framework`、`MeetingUIClient.framework`、およびリリース バンドルで提供されているその他のフレームワークを、このフォルダーにコピーします。
4. フレームワークを全般タブのプロジェクト ターゲットに追加します。[`Add Other`]\(その他のものを追加\) -> [`Add Files...`]\(ファイルを追加\) を使用してフレームワーク ファイルに移動し、それらを追加します。

:::image type="content" source="../media/ios/xcode-add-frameworks.png" alt-text="Xcode で追加されたフレームワークを示すスクリーンショット。":::

5. プロジェクトのターゲット ビルド設定タブの [`Framework Search Paths`]\(フレームワーク検索パス\) に、`$(PROJECT_DIR)/Frameworks` を追加します (まだ追加していない場合)。この設定を見つけるには、フィルターを [`basic`]\(基本\) から [`all`]\(すべて\) に変更します。右側の検索バーを使用してもかまいません。

:::image type="content" source="../media/ios/xcode-add-framework-search-path.png" alt-text="Xcode でのフレームワーク検索パスを示すスクリーンショット。":::

### <a name="turn-off-bitcode"></a>ビットコードをオフにする

プロジェクトの [`Build Settings`]\(ビルド設定\) で [`Enable Bitcode`]\(ビットコードの有効化\) オプションを [`No`]\(いいえ\) に設定します。 この設定を見つけるには、フィルターを [`Basic`]\(基本\) から [`All`]\(すべて\) に変更します。右側の検索バーを使用してもかまいません。

:::image type="content" source="../media/ios/xcode-bitcode-option.png" alt-text="Xcode のビットコード オプションを示すスクリーンショット。":::


### <a name="turn-on-voice-over-ip-background-mode"></a>Voice over IP バックグラウンド モードを有効にする

アプリ ターゲットを選択し、[Capabilities]\(機能\) タブをクリックします。

上部の [`+ Capabilities`]\(+ 機能\) をクリックして [`Background Modes`]\(バックグラウンド モード) を有効にし、[`Background Modes`]\(バックグラウンド モード) を選択します。

`Voice over IP` のチェック ボックスをオンにします。

:::image type="content" source="../media/ios/xcode-background-voip.png" alt-text="Xcode で有効にされた VOIP を示すスクリーンショット。":::

### <a name="add-a-window-reference-to-appdelegate"></a>AppDelegate にウィンドウ参照を追加する

プロジェクトの **AppDelegate.swift** ファイルを開き、"window" の参照を追加します。

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?
```

### <a name="add-a-button-to-the-viewcontroller"></a>ViewController にボタンを追加する

**ViewController.swift** の `viewDidLoad` コールバックでボタンを作成します。

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    let button = UIButton(frame: CGRect(x: 100, y: 100, width: 200, height: 50))
    button.backgroundColor = .black
    button.setTitle("Join Meeting", for: .normal)
    button.addTarget(self, action: #selector(joinMeetingTapped), for: .touchUpInside)
    
    button.translatesAutoresizingMaskIntoConstraints = false
    self.view.addSubview(button)
    button.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    button.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
```

**ViewController.swift** でボタンのアウトレットを作成します。

```swift
@IBAction func joinMeetingTapped(_ sender: UIButton) {

}
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクトの **ViewController.swift** ファイルを開き、そのファイルの冒頭に `import` 宣言を追加して、`AzureCommunication library` と `MeetingUIClient` をインポートします。 

```swift
import UIKit
import AzureCommunication
import MeetingUIClient
```

`ViewController` クラスの実装を、ユーザーが会議に参加するためのシンプルなボタンに置き換えます。 このクイックスタートでは、このボタンにビジネス ロジックをアタッチします。

```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    
    override func viewDidLoad() {
        super.viewDidLoad()

        // Initialize meetingClient
    }

    @IBAction func joinMeetingTapped(_ sender: UIButton) {
        joinMeeting()
    }
    
    private func joinMeeting() {
        // Add join meeting logic
    }
}
```

## <a name="object-model"></a>オブジェクト モデル

Azure Communication Services Teams Embed ライブラリが備える主な機能のいくつかは、次のクラスとインターフェイスにより処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient | MeetingUIClient は、Teams Embed ライブラリへのメイン エントリ ポイントです。 |
| MeetingUIClientMeetingJoinOptions | MeetingUIClientMeetingJoinOptions は、表示名など、構成可能なオプションに使用されます。 |
| MeetingUIClientGroupCallJoinOptions | MeetingUIClientMeetingJoinOptions は、表示名など、構成可能なオプションに使用されます。 |
| MeetingUIClientTeamsMeetingLinkLocator | MeetingUIClientTeamsMeetingLinkLocator は、会議に参加するための会議 URL を設定するために使用されます。 |
| MeetingUIClientGroupCallLocator | MeetingUIClientGroupCallLocator は、参加するグループ ID を設定するために使用されます。 |
| MeetingUIClientCallState | MeetingUIClientCallState は、呼び出し状態の変化をレポートする目的で使用されます。 `connecting`、`waitingInLobby`、`connected`、`ended` の各オプションがあります。 |
| MeetingUIClientDelegate | MeetingUIClientDelegate は、呼び出し状態の変化など、イベントを受け取る目的で使用されます。 |
| MeetingUIClientIdentityProviderDelegate | MeetingUIClientIdentityProviderDelegate は、ユーザーの詳細を会議内のユーザーにマップするために使用されます。 |
| MeetingUIClientUserEventDelegate | MeetingUIClientUserEventDelegate は、UI でのユーザー操作に関する情報を提供します。 |

## <a name="create-and-authenticate-the-client"></a>クライアントを作成して認証する

ユーザー アクセス トークンを使用して `MeetingUIClient` インスタンスを初期化します。これにより、会議に参加できるようになります。 **ViewController.swift** で `viewDidLoad` コールバックに次のコードを追加します。

```swift
do {
    let communicationTokenRefreshOptions = CommunicationTokenRefreshOptions(initialToken: "<USER_ACCESS_TOKEN>", refreshProactively: true, tokenRefresher: fetchTokenAsync(completionHandler:))
    let credential = try CommunicationTokenCredential(withOptions: communicationTokenRefreshOptions)
    meetingUIClient = MeetingUIClient(with: credential)
}
catch {
    print("Failed to create communication token credential")
}
```

`<USER_ACCESS_TOKEN>` は、リソース用の有効なユーザー アクセス トークンに置き換えます。 まだトークンを入手していない場合は、[ユーザー アクセス トークン](../../access-tokens.md)に関するドキュメントを参照してください。

### <a name="setup-token-refreshing"></a>セットアップ トークンを更新する

`fetchTokenAsync` メソッドを作成します。 その後、ユーザー トークンを取得するための `fetchToken` ロジックを追加します。

```swift
private func fetchTokenAsync(completionHandler: @escaping TokenRefreshHandler) {
    func getTokenFromServer(completionHandler: @escaping (String) -> Void) {
        completionHandler("<USER_ACCESS_TOKEN>")
    }
    getTokenFromServer { newToken in
        completionHandler(newToken, nil)
    }
}
```

`<USER_ACCESS_TOKEN>` は、リソース用の有効なユーザー アクセス トークンに置き換えます。

## <a name="join-a-meeting"></a>会議に参加する

`join` メソッドは、 *[Join Meeting]\(会議に参加する\)* ボタンをタップしたときに実行されるアクションとして設定されます。 `MeetingUIClient` を使用して会議に参加するように実装を更新します。

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
    })
}
```

`<MEETING URL>` は、Microsoft Teams 会議のリンクに置き換えます。

### <a name="get-a-microsoft-teams-meeting-link"></a>Microsoft Teams 会議のリンクを取得する

Microsoft Teams 会議のリンクは、Graph API を使用して取得できます。 この点については、[Graph のドキュメント](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)で詳しく説明されています。
Communication Services 通話 SDK は、Microsoft Teams 会議のフル リンクを受け入れます。 このリンクは、`onlineMeeting` リソースの一部として返され、[`joinWebUrl` プロパティ](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)からアクセスできます。必要な会議情報は、Teams 会議の招待自体に含まれる **[会議に参加]** の URL から取得することもできます。

## <a name="run-the-code"></a>コードの実行

iOS シミュレーターでアプリをビルドして実行するには、 **[製品]**  >  **[実行]** の順に選択するか、(&#8984;-R) キーボード ショートカットを使用します。

:::image type="content" source="../media/ios/quick-start-join-meeting.png" alt-text="クイック スタート アプリの最終的な外観":::

:::image type="content" source="../media/ios/quick-start-meeting-joined.png" alt-text="会議に参加した後の最終的な外観":::

> [!NOTE]
> 初めて会議に参加するときに、マイクへのアクセスを求めるメッセージが表示されます。 運用環境のアプリケーションでは、`AVAudioSession` API を使用して[アクセス許可の状態を確認し](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources)、アクセス許可が付与されていない場合はアプリケーションの動作を適切に更新する必要があります。

## <a name="add-localization-support-based-on-your-app"></a>アプリに基づいてローカライズ サポートを追加する

Microsoft Teams SDK では、100 を超える文字列とリソースがサポートされています。 ベース言語と英語は、フレームワーク バンドルに含まれます。 それ以外は、パッケージに付属する `Localizations.zip` ファイルに含まれます。

#### <a name="add-localizations-to-the-sdk-based-on-what-your-app-supports"></a>アプリのサポート対象に基づいてローカライズを SDK に追加する

1. アプリの Xcode プロジェクトの [Info]\(情報\) > [Localizations]\(ローカライズ\) リストから、アプリケーションでサポートするローカライズの種類を指定します
2. パッケージに含まれている Localizations.zip を解凍します
3. 解凍したフォルダーから、アプリのサポート対象に基づいてローカライズ フォルダーを TeamsAppSDK.framework のルートにコピーします


## <a name="sample-code"></a>サンプル コード

サンプル アプリは [GitHub](https://github.com/Azure-Samples/teams-embed-ios-getting-started) からダウンロードできます

---
title: iOS 用の Azure Communication Services Teams Embed の使用
description: この概要では、iOS 用の Azure Communication Services Teams Embed ライブラリを使用する方法について説明します。
author: palatter
ms.author: palatter
ms.date: 24/02/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 1a6c8d05da04dc0f32fb278baf946ea363010903
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2021
ms.locfileid: "111546600"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../create-communication-resource.md)。
- 通話クライアントを有効にするための `User Access Token`。 [`User Access Token` を取得する方法](../../access-tokens.md)についての詳細
- [アプリケーションへの Teams Embed の追加の概要](../getting-started-with-teams-embed.md)に関するクイックスタートを完了します

## <a name="teams-embed-call-or-meeting-status-events-capturing"></a>Teams Embed の通話または会議の状態イベントの取得

参加しているグループの通話または会議の状態は、`MeetingUIClientCallDelegate` デリゲートから取得できます。 この状態には、接続状態、参加者の数、マイクやカメラの状態などのモダリティが含まれます。   

`MeetingUIClientCallDelegate` をクラスに追加し、必要な変数を追加します。

```swift
class ViewController: UIViewController, MeetingUIClientCallDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
    
    //Rest of the UIViewController code
}
```

通話または会議への参加が正常に開始された後、`self.meetingUIClientCall?.meetingUIClientCallDelegate` を `self` に設定します。

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```

アプリに必要な `MeetingUIClientCallDelegate` プロトコル メソッドを実装し、不要なものに対してスタブを追加します。

```swift
    func meetingUIClient(didUpdateCallState callState: MeetingUIClientCallState) {
        switch callState {
        case .connecting:
            print("Call state has changed to 'Connecting'")
        case .connected:
            print("Call state has changed to 'Connected'")
        case .waitingInLobby:
            print("Call state has changed to 'Waiting in Lobby'")
        case .ended:
            print("Call state has changed to 'Ended'")
        }
    }
    
    func meetingUIClient(didUpdateRemoteParticipantCount remoteParticipantCount: UInt) {
        print("Remote participant count has changed to: \(remoteParticipantCount)")
    }
    
    func onIsMutedChanged() {
    }
    
    func onIsSendingVideoChanged() {
    }
    
    func onIsHandRaisedChanged(_ participantIds: [Any]) {
    }
```

## <a name="bring-your-own-identity-from-the-app-to-the-participants-in-the-sdk-call"></a>SDK 呼び出しの参加者にアプリの独自の ID を使用します。

アプリでは、通話または会議の参加者にユーザーの ID 値を割り当てて、既定値を上書きできます。 値には、アバター、名前、サブタイトル、およびロールが含まれます。  

### <a name="assigning-avatars-for-call-participants"></a>通話参加者へのアバターの割り当て

クラスに `MeetingUIClientCallIdentityProviderDelegate` を追加します。

```swift
class ViewController: UIViewController, MeetingUIClientCallIdentityProviderDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

通話または会議への参加が正常に開始された後、`self.meetingUIClientCall?.MeetingUIClientIdentityProviderDelegate` を `self` に設定します。

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallIdentityProviderDelegate = self
            }
        }
    })
}
```

`avatarFor` プロトコル メソッドを追加して実装し、それぞれの `identifier` を対応するアバターにマップします。

```swift
    func avatarFor(identifier: CommunicationIdentifier, size: MeetingUIClientAvatarSize, completionHandler: @escaping (UIImage?) -> Void) {
        if let userIdentifier = identifier as? CommunicationUserIdentifier
        {
            if (userIdentifier.identifier.starts(with: "8:teamsvisitor:")) {
                // Anonymous teams user will start with prefix 8:teamsvistor:
                let image = UIImage (named: "avatarPink")
                completionHandler(image!)
            }
            else if (userIdentifier.identifier.starts(with: "8:orgid:")) {
                // OrgID user will start with prefix 8:orgid:
                let image = UIImage (named: "avatarDoctor")
                completionHandler(image!)
            }
            else if (userIdentifier.identifier.starts(with: "8:acs:")) {
                // ACS user will start with prefix 8:acs:
                let image = UIImage (named: "avatarGreen")
                completionHandler(image!)
            } else {
                completionHandler(nil)
            }
        } else {
            completionHandler(nil)
        }
    }

```

その他の必須の MeetingUIClientCallIdentityProviderDelegate プロトコル メソッドをクラスに割り当てます。その際、空の実装を持たせることもできます。
```swift
    func displayNameFor(identifier: CommunicationIdentifier, completionHandler: @escaping (String?) -> Void) {
    }
    
    func subTitleFor(identifier: CommunicationIdentifier, completionHandler: @escaping (String?) -> Void) {
    }
    
    func roleFor(identifier: CommunicationIdentifier, completionHandler: @escaping (MeetingUIClientUserRole) -> Void) {
    }
```

## <a name="use-teams-embed-sdk-and-azure-communication-calling-sdk-in-the-same-app"></a>Teams Embed SDK と Azure Communication Calling SDK を同じアプリで使用する

Teams Embed SDK では内部に Azure Communication Calling SDK (ACS) も用意されているため、同じアプリで両方の SDK 機能を使用できます。 一度に初期化して使用できるのは、1 つの SDK のみです。 両方の SDK を同時に初期化して使用すると、予期しない動作が発生します。 

Teams Embed SDK から Azure Communication Calling SDK にアクセスするために、`TeamsAppSDK` をインポートします。 
```swift
import TeamsAppSDK
```

ACS の使用のための変数を宣言します
```swift
class ViewController: UIViewController {

    private var callClient: CallClient?
    private var callAgent: CallAgent?
    private var call: Call?
    
    //Rest of the UIViewController code
}
    
```

初期化を行うには、新しい `CallClient` を作成します。 作成したものを `viewDidLoad` または他のメソッドに追加します。

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    self.callClient = CallClient()
}
```
すべての ACS API は、そのドキュメントに記載されているように使用してください。 このドキュメントでは、API の使用方法については説明しません。 

使用する必要がなくなったか、Teams Embed SDK をアプリで使用することが必要になったら、ACS SDK を破棄し、`nil` をその変数に設定します。
```swift
    public func disposeAcsSdk()
    {
        self.call = nil
        self.callAgent?.dispose()
        self.callClient?.dispose()
        self.callAgent = nil
        self.callClient = nil
    }

```

Teams Embed SDK の初期化は、`MeetingUIClient` の作成時にも実行されます。 作成したものを `viewDidLoad` または他のメソッドに追加します。
```swift
override func viewDidLoad() {
    super.viewDidLoad()
    do {
        let communicationTokenRefreshOptions = CommunicationTokenRefreshOptions(initialToken: "<USER_ACCESS_TOKEN>", refreshProactively: true, tokenRefresher: fetchTokenAsync(completionHandler:))
        let credential = try CommunicationTokenCredential(withOptions: communicationTokenRefreshOptions)
        meetingUIClient = MeetingUIClient(with: credential)
    }
    catch {
        print("Failed to create communication token credential")
    }
}

private func fetchTokenAsync(completionHandler: @escaping TokenRefreshHandler) {
    func getTokenFromServer(completionHandler: @escaping (String) -> Void) {
        completionHandler("<USER_ACCESS_TOKEN>")
    }
    getTokenFromServer { newToken in
        completionHandler(newToken, nil)
    }
}

```
Teams Embed SDK API は、そのドキュメントに記載されているように使用してください。 このドキュメントでは、API の使用方法については説明しません。 

使用する必要がなくなったか、ACS SDK をアプリで使用することが必要になったら、Teams Embed SDK を破棄し、`nil` をその変数に設定します。
```swift
    private func disposeTeamsSdk() {
        self.meetingUIClient?.dispose(completionHandler: { (error: Error?) in
            if (error != nil) {
                print("Dispose failed: \(error!)")
            } else {
                self.meetingUIClient = nil
                self.meetingUIClientCall = nil
            }
        })        
    }

```

Teams Embed SDK の破棄は、アクティブな通話がない場合にのみ可能です。 アクティブな通話がある場合、`meetingUIClient?.dispose` は完了ハンドラーでエラーを返します。 アクティブな通話を終了し、アクティブな通話が残っていない場合は `self.disposeTeamsSdk()` を呼び出します。
```swift
    
    private var shouldDispose: Bool = false

    public func endMeeting() {
        meetingUIClientCall?.hangUp(completionHandler: { (error: Error?) in
            if (error != nil) {
                print("End meeting failed: \(error!)")
                // There are no active calls
                self.disposeTeamsSdk()
            } else {
                // Ending active call is in progress.
                self.shouldDispose = true;
            }
        })
    }

```

`MeetingUIClientCallDelegate` プロトコル メソッド `meetingUIClientCall(didUpdateCallState callState: MeetingUIClientCallState)` を実装して、終了したアクティブな通話に関する状態の更新を取得し、その後 Teams Embed SDK を破棄します。
```swift
    func meetingUIClientCall(didUpdateCallState callState: MeetingUIClientCallState) {
        switch callState {
        case .connecting:
        case .connected:
        case .waitingInLobby:
        case .ended:
            if (self.shouldDispose) {
                self.disposeTeamsSdk()
            }
        @unknown default:
            print("Unsupported state")
        }
    }
```

その他の必須の `MeetingUIClientCallDelegate` プロトコル メソッドをクラスに追加します

```swift
    func meetingUIClientCall(didUpdateRemoteParticipantCount remoteParticipantCount: UInt) {
        print("Remote participant count has changed to: \(remoteParticipantCount)")
    }

    func onIsMutedChanged() {
        print("Mute state changed to: \(meetingUIClientCall?.isMuted ?? false)")
    }
    
    func onIsSendingVideoChanged() {
        print("Sending video state changed to: \(meetingUIClientCall?.isSendingVideo ?? false)")
    }
    
    func onIsHandRaisedChanged(_ participantIds: [Any]) {
        print("Self participant raise hand status changed to: \(meetingUIClientCall?.isHandRaised ?? false)")
    }
```

## <a name="receive-information-about-user-actions-in-the-ui-and-add-your-own-custom-functionalities"></a>UI でのユーザー操作に関する情報を受け取り、独自のカスタム機能を追加します。

`MeetingUIClientCallUserEventDelegate` デリゲート メソッドは、リモート参加者のプロファイルのユーザー操作で呼び出されます。
通話または会議に参加する際、参加オプション プロパティ `enableNamePlateOptionsClickDelegate` を `true` に設定します。
このプロパティを設定すると、リモート参加者のプロファイルで名前プレート オプションが有効になり、`MeetingUIClientCallUserEventDelegate` が有効になります。

クラスに `MeetingUIClientCallUserEventDelegate` を追加します。

```swift
class ViewController: UIViewController, MeetingUIClientCallUserEventDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

通話または会議への参加が正常に開始された後、`self.meetingUIClientCall?.meetingUIClientCallUserEventDelegate` を `self` に設定します。

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallUserEventDelegate = self
            }
        }
    })
}
```

`onNamePlateOptionsClicked` プロトコル メソッドを追加して実装し、それぞれの `identifier` を対応する通話参加者ユーザーにマップします。
このメソッドは、通話メイン画面でユーザー タイルまたはユーザー タイトル テキストが 1 回タップされると呼び出されます。

```swift
func onNamePlateOptionsClicked(identifier: CommunicationIdentifier) {
    if let userIdentifier = identifier as? CommunicationUserIdentifier
        {
            if (userIdentifier.identifier.starts(with: "8:acs:")) {
                // Custom behavior based on the user here.
                print("Acs user tile clicked")
            }
        }
}
```

`onParticipantViewLongPressed` プロトコル メソッドを追加して実装し、それぞれの `identifier` を対応する通話参加者ユーザーにマップします。
このメソッドは、通話メイン画面でユーザー タイルを長押ししたときに呼び出されます。

```swift
func onParticipantViewLongPressed(identifier: CommunicationIdentifier) {
    if let userIdentifier = identifier as? CommunicationUserIdentifier
        {
            if (userIdentifier.identifier.starts(with: "8:acs:")) {
                // Custom behavior based on the user here.
                print("Acs user tile clicked")
            }
        }
}
```
## <a name="user-experience-customization"></a>ユーザー エクスペリエンスのカスタマイズ

SDK のユーザー エクスペリエンスは、アプリ固有のアイコンを提供したり、通話制御バーを置き換えたりすることによってカスタマイズできます。 

### <a name="customize-ui-icons-in-a-call-or-meeting"></a>通話または会議の UI アイコンをカスタマイズする

通話または会議に表示されるアイコンは、`MeetingUIClient` で公開されている `public func set(iconConfig: Dictionary<MeetingUIClientIconType, String>)` メソッドを使用してカスタマイズできます。
カスタマイズ可能なアイコンの一覧は、`MeetingUIClientIconType` にあります。

```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

MeetingUIClient を初期化した後、`MeetingUIClientIconType` でサポートされている通話アイコンのアイコン構成 `meetingUIClient?.set(iconConfig: self.getIconConfig())` を設定します。

```swift
private func initMeetingUIClient() {
    meetingUIClient = MeetingUIClient(with: credential)
    meetingUIClient?.set(iconConfig: self.getIconConfig())
}

func getIconConfig() -> Dictionary<MeetingUIClientIconType, String> {
    var iconConfig = Dictionary<MeetingUIClientIconType, String>()
    iconConfig.updateValue("camera_fill", forKey: MeetingUIClientIconType.VideoOn)
    iconConfig.updateValue("camera_off", forKey: MeetingUIClientIconType.VideoOff)
    iconConfig.updateValue("microphone_fill", forKey: MeetingUIClientIconType.MicOn)
    iconConfig.updateValue("microphone_off", forKey: MeetingUIClientIconType.MicOff)
    iconConfig.updateValue("speaker_fill", forKey: MeetingUIClientIconType.Speaker)
    return iconConfig
}
```

### <a name="customize-main-call-screen"></a>メイン通話画面のカスタマイズ

`MeetingUIClient` は、メイン通話画面の UI をカスタマイズするためのサポートを提供します。 現時点では、`MeetingUIClientInCallScreenDelegate` プロトコル メソッドを使用した UI のカスタマイズがサポートされています。
通話画面制御アクションは、`MeetingUIClientCall` に存在するメソッドを通じて公開されます。

クラスに `MeetingUIClientInCallScreenDelegate` を追加します。

```swift
class ViewController: UIViewController, MeetingUIClientInCallScreenDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

通話または会議に参加する前に、`meetingUIClient?.meetingUIClientInCallScreenDelegate` を `self` に設定します。

```swift
private func joinGroupCall() {
    meetingUIClient?.meetingUIClientInCallScreenDelegate = self
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: "<GROUP_ID>")
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join call failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```

`provideControlTopBar` プロトコル メソッドを追加して実装し、メイン通話画面の上部情報バーを提供します。

```swift
func provideControlTopBar() -> UIView? {
    let topView = UIStackView.init()
    // add your customization here
    return topView
}
```

その他の必須の `MeetingUIClientInCallScreenDelegate` プロトコル メソッドをクラスに割り当てます。その際、nil を返す空の実装を持たせることもできます。
```swift
func provideControlBottomBar() -> UIView? {
    return nil
}

func provideScreenBackgroudColor() -> UIColor? {
    return nil
}
```

## <a name="customize-on-staging-call-screen"></a>ステージング通話画面のカスタマイズ

`MeetingUIClient` は、ステージング通話画面の UI をカスタマイズするためのサポートを提供します。 現時点では、`MeetingUIClientStagingScreenDelegate` プロトコル メソッドを使用した UI のカスタマイズがサポートされています。
通話または会議に参加する際、参加オプション プロパティ `enableCallStagingScreen` を `true` に設定して、ステージング画面を表示します。

クラスに `MeetingUIClientStagingScreenDelegate` を追加します。

```swift
class ViewController: UIViewController, MeetingUIClientStagingScreenDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

通話または会議に参加する前に、`meetingUIClient?.meetingUIClientStagingScreenDelegate` を `self` に設定します。

```swift
private func joinGroupCall() {
    meetingUIClient?.meetingUIClientStagingScreenDelegate = self
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: "<GROUP_ID>")
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join call failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```

`provideJoinButtonCornerRadius` プロトコル メソッドを追加して実装し、参加ボタンの角が丸く見えるように変更します。

```swift
func provideJoinButtonCornerRadius() -> CGFloat {
    return 24
}
```

その他の必須の `MeetingUIClientStagingScreenDelegate` プロトコル メソッドをクラスに追加します。その際、nil を返す空の実装を持たせることもできます。
```swift
func provideJoinButtonBackgroundColor() -> UIColor? {
    return nil
}

func provideStagingScreenBackgroundColor() -> UIColor? {
    return nil
}
```

## <a name="customize-on-connecting-call-screen"></a>通話の接続画面をカスタマイズする

`MeetingUIClient` は、通話の接続画面の UI をカスタマイズするためのサポートを提供します。 現時点では、`MeetingUIClientConnectingScreenDelegate` プロトコル メソッドを使用した UI のカスタマイズがサポートされています。
`MeetingUIClient` で公開されているアイコン構成メソッド `set(iconConfig: Dictionary<MeetingUIClientIconType, String>)` を使用して、表示されているアイコンだけを変更し、`MeetingUIClient` によって提供される機能を使用します。


クラスに `MeetingUIClientConnectingScreenDelegate` を追加します。

```swift
class ViewController: UIViewController, MeetingUIClientConnectingScreenDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

通話または会議に参加する前に、`meetingUIClient?.meetingUIClientConnectingScreenDelegate` を `self` に設定します。

```swift
private func joinGroupCall() {
    meetingUIClient?.meetingUIClientConnectingScreenDelegate = self
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: "<GROUP_ID>")
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join call failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```

`provideConnectingScreenBackgroundColor` プロトコル メソッドを追加して実装し、接続画面の背景色を変更します。

```swift
func provideConnectingScreenBackgroundColor() -> UIColor?
    return 24
}
```

## <a name="perform-operations-with-the-call"></a>通話での操作を実行する

通話制御アクションは、`MeetingUIClientCall` に存在するメソッドを通じて公開されます。
これらのメソッドは、UI が `MeetingUIClient` カスタマイズ デリゲートを使用してカスタマイズされている場合に、通話アクションを制御するのに役立ちます。

通話に必要な変数を追加しました。
```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

`join` メソッド `completionHandler` から `meetingUIClientCall` 値を `self.meetingUIClientCall` 変数に割り当てます。
```swift
private func joinGroupCall() {
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: "<GROUP_ID>")
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join call failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```
### <a name="mute-and-unmute"></a>ミュートとミュート解除

`mute` メソッドを呼び出して、アクティブな通話がある場合にマイクをミュートします。
マイクの状態の変化は、`MeetingUIClientCallDelegate` の `onIsMutedChanged` メソッドで通知されます。

```swift
// Mute the microphone for an active call.
public func mute(completionHandler: @escaping (Error?) -> Void)

    meetingUIClientCall?.mute { [weak self] (error) in
        if error != nil {
            print("Mute call failed: \(error!)")
        }
}
```

`unmute` メソッドを呼び出して、アクティブな通話がある場合にマイクのミュートを解除します。

```swift
// Unmute the microphone for an active call.
public func unmute(completionHandler: @escaping (Error?) -> Void)

meetingUIClientCall?.unmute { [weak self] (error) in
    if error != nil {
        print("Mute call failed: \(error!)")
    }
}
```

### <a name="other-operations-available-in-from-the--meetinguiclientcall-class"></a>`MeetingUIClientCall` クラスから使用できるその他の操作。

```swift
// Start the video for an active call.
public func startVideo(completionHandler: @escaping (Error?) -> Void)

// Stop the video for an active call.
public func startVideo(completionHandler: @escaping (Error?) -> Void)

// Set the preferred audio route in the call for self user.
public func setAudio(route: MeetingUIClientAudioRoute, completionHandler: @escaping (Error?) -> Void)

// Raise the hand of current user for an active call.
public func raiseHand(completionHandler: @escaping (Error?) -> Void)

// Lower the hand of user provided in the identifier for an active call.
// public func lowerHand(identifier: CommunicationIdentifier, completionHandler: @escaping (Error?) -> Void)

// Show the call roster for an active call.
public func showCallRoster(completionHandler: @escaping (Error?) -> Void)

// Change the layout in the call for self user.
public func getSupportedLayoutModes() -> [MeetingUIClientLayoutMode]
public func changeLayout(mode: MeetingUIClientLayoutMode, completionHandler: @escaping (Error?) -> Void)

// Hangs up the call or leaves the meeting.
public func hangUp(completionHandler: @escaping (Error?) -> Void)

// Set the user role for an active call.
public func setRoleFor(identifier: CommunicationIdentifier, userRole: MeetingUIClientUserRole, completionHandler: @escaping (Error?) -> Void)
```

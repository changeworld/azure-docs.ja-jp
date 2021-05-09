---
title: iOS 用の Azure Communication Services Teams Embed の使用
description: この概要では、iOS 用の Azure Communication Services Teams Embed ライブラリを使用する方法について説明します。
author: palatter
ms.author: palatter
ms.date: 24/02/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: b6b3a2b45c3013170e8341228dd7b78b46881015
ms.sourcegitcommit: b4032c9266effb0bf7eb87379f011c36d7340c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2021
ms.locfileid: "107925271"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../create-communication-resource.md)。
- 通話クライアントを有効にするための `User Access Token`。 [`User Access Token` を取得する方法](../../access-tokens.md)についての詳細
- [アプリケーションへの Teams Embed の追加の概要](../getting-started-with-teams-embed.md)に関するクイックスタートを完了します

## <a name="teams-embed-events"></a>Teams Embed イベント

クラスに `MeetingUIClientDelegate` を追加します。

```swift
class ViewController: UIViewController, MeetingUIClientDelegate {

    private var meetingUIClient: MeetingUIClient?
```

`meetingUIClientDelegate` を `self` に設定します。

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    meetingUIClient?.meetingUIClientDelegate = self
}
```

`didUpdateCallState` および `didUpdateRemoteParticipantCount` 関数を実装します。

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
    
    func meetingUIClient(didUpdateRemoteParticipantCount remoteParticpantCount: UInt) {
        print("Remote participant count has changed to: \(remoteParticpantCount)")
    }
```

## <a name="assigning-avatars-for-users"></a>ユーザーにアバターを割り当てる

クラスに `MeetingUIClientIdentityProviderDelegate` を追加します。

```swift
class ViewController: UIViewController, MeetingUIClientIdentityProviderDelegate {

    private var meetingUIClient: MeetingUIClient?
```

会議に参加する前に、`MeetingUIClientIdentityProviderDelegate` を `self` に設定します。

```swift
private func joinMeeting() {
    meetingUIClient?.meetingUIClientIdentityProviderDelegate = self
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: <MEETING_URL>)
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
    })
}
```

`avatarFor` を追加、実装し、対応するアバターを各 `userMri` に割り当てます。

```swift
    func avatarFor(userIdentifier: String, completionHandler: @escaping (UIImage?) -> Void) {
        if (userIdentifier.starts(with: "8:teamsvisitor:")) {
            // Anonymous teams user will start with prefix 8:teamsvistor:
            let image = UIImage (named: "avatarPink")
            completionHandler(image!)
        }
        else if (userIdentifier.starts(with: "8:orgid:")) {
            // OrgID user will start with prefix 8:orgid:
            let image = UIImage (named: "avatarDoctor")
            completionHandler(image!)
        }
        else if (userIdentifier.starts(with: "8:acs:")) {
            // ACS user will start with prefix 8:acs:
            let image = UIImage (named: "avatarGreen")
            completionHandler(image!)
        }
        else {
            completionHandler(nil)
        }
}

```

MeetingUIClientIdentityProviderDelegate プロトコルのその他の必須メソッドをクラスに割り当てます。その際、空の実装を持たせることもできます。
```swift
    func displayNameFor(userIdentifier: String, completionHandler: @escaping (String?) -> Void) {
    }
    
    func subTitleFor(userIdentifier: String, completionHandler: @escaping (String?) -> Void) {
    }
```

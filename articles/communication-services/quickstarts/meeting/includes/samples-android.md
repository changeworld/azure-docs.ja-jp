---
title: Android 用の Azure Communication Services Teams Embed の使用
description: この概要では、Android 用の Azure Communication Services Teams Embed ライブラリを使用する方法について説明します。
author: palatter
ms.author: palatter
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 5f74ab05517c2859cecb9913c19a9ab4c454c85c
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113215129"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../create-communication-resource.md)。
- 通話クライアントを有効にするための `User Access Token`。 [`User Access Token` を取得する方法](../../access-tokens.md)についての詳細
- [アプリケーションへの Teams Embed の追加の概要](../getting-started-with-teams-embed.md)に関するクイックスタートを完了します

## <a name="joining-a-group-call"></a>グループ通話への参加

グループ通話には、`MeetingUIClientGroupCallLocator` と `MeetingUIClientJoinOptions` を `meetingUIClient.join` API に指定することで参加できます。 グループ通話では、他の参加者の呼び出しは行われません。 ユーザーは通話にサイレントで参加します。

メイン レイアウト ファイル (`app/src/main/res/layout/activity_main.xml`) に移動して、グループ通話に参加するボタンを設定します。 ID `join_groupCall` でボタンを作成します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/join_groupCall"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Join Group Call"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

**MainActivity.java** に移動し、`onCreate` メソッドでクリック アクションにこのボタンを追加します。

```java
package com.yourcompany.teamsembedandroidgettingstarted;

import com.azure.android.communication.ui.meetings.MeetingUIClient;
import com.azure.android.communication.ui.meetings.MeetingUIClientCall;
import com.azure.android.communication.ui.meetings.MeetingUIClientGroupCallLocator;
import com.azure.android.communication.ui.meetings.MeetingUIClientJoinOptions;

import java.util.UUID;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        Button joinGroupCallButton = findViewById(R.id.join_groupCall);
        joinGroupCallButton.setOnClickListener(l -> joinGroupCall());
    }
}
```

クライアントの設定とトークンの指定は、[クイックスタート](../getting-started-with-teams-embed.md)で説明されている会議参加 API の場合と同じ方法で行えます。

`joinGroupCall` メソッドは、*グループ通話への参加* ボタンをクリックすると実行されるアクションとして設定されます。 グループ通話への参加は、`MeetingUIClient` を介して行えます。 `MeetingUIClientGroupCallLocator` を作成し、`MeetingUIClientJoinOptions` を使用して参加オプションを構成します。
`<GROUP_ID>` は UUID 文字列で置き換えてください。 グループ ID 文字列は、GUID 形式または UUID 形式である必要があります。

```java
/**
 * Join a group call with a groupID.
 */
private void joinGroupCall() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        UUID groupUUID = UUID.fromString("<GROUP_ID>");
        MeetingUIClientGroupCallLocator meetingUIClientGroupCallLocator = new MeetingUIClientGroupCallLocator(groupUUID);
        meetingUIClientCall = meetingUIClient.join(meetingUIClientGroupCallLocator, meetingJoinOptions);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join group call: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="teams-embed-call-or-meeting-status-events-capturing"></a>Teams Embed の通話または会議の状態イベントの取得

参加しているグループの通話または会議の状態は、`MeetingUIClientCallEventListener` クラスから取得できます。 この状態には、接続状態、参加者の数、マイクやカメラの状態などのモダリティが含まれます。

次のコードを `MainActivity.java` に追加します。

```java
import androidx.core.content.ContextCompat;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.common.CommunicationTokenRefreshOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClient;
import com.azure.android.communication.ui.meetings.MeetingUIClientCall;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallState;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallEventListener;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallIdentityProvider;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallIdentityProviderCallback;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallUserEventListener;
import com.azure.android.communication.ui.meetings.MeetingUIClientGroupCallLocator;
import com.azure.android.communication.ui.meetings.MeetingUIClientIconType;
import com.azure.android.communication.ui.meetings.MeetingUIClientJoinOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClientTeamsMeetingLinkLocator;
```

クラスに `MeetingUIClientCallEventListener` を追加します。

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientEventListener {
```

通話または会議への参加が正常に開始された後、パラメーター `this` で `setMeetingUIClientCallEventListener` を呼び出します。

```java
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);
    
    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);
    
    try {
        MeetingUIClientCall meetingUIClientCall = meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

アプリに必要な `MeetingUIClientCallEventListener` メソッドを実装し、不要なものにはスタブを追加します。

```java
@Override
public void onCallStateChanged(MeetingUIClientCallState callState) {
    switch(callState) {
        case CONNECTING:
            System.out.println("Call state changed to Connecting");
            break;
        case CONNECTED:
            System.out.println("Call state changed to Connected");
            break;
        case WAITING_IN_LOBBY:
            System.out.println("Call state changed to Waiting in Lobby");
            break;
        case ENDED:
            System.out.println("Call state changed to Ended");
            break;
    }
}

@Override
public void onRemoteParticipantCountChanged(int newCount) {
    System.out.println("Remote participant count changed: " + newCount);
}

@Override
public void onIsMutedChanged() {
}

@Override
public void onIsSendingVideoChanged() {
}

@Override
public void onIsHandRaisedChanged(List<String> participantIds) {
}
```

## <a name="bring-your-own-identity-from-the-app-to-the-participants-in-the-sdk-call"></a>SDK 呼び出しの参加者にアプリの独自の ID を使用します。

アプリでは、通話または会議の参加者にユーザーの ID 値を割り当てて、既定値を上書きできます。 値には、アバター、名前、およびサブタイトルなどがあります。

### <a name="assigning-avatars-for-call-participants"></a>通話参加者へのアバターの割り当て

クラスに `MeetingUIClientCallIdentityProvider` を追加します。

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientCallIdentityProvider {
```

パラメーター `this` で `setMeetingUIClientCallIdentityProvider` を呼び出します。

```java
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        MeetingUIClientCall meetingUIClientCall = meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallIdentityProvider(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

`provideAvatarFor` メソッドを実装し、それぞれの `userIdentifier` を対応するアバターにマップします。

```java
@Override
public void provideAvatarFor(CommunicationIdentifier communicationIdentifier, MeetingUIClientAvatarSize avatarSize, MeetingUIClientCallIdentityProviderCallback callback) {
    if(communicationIdentifier instanceof CommunicationUserIdentifier) {
        CommunicationUserIdentifier userIdentifier = (CommunicationUserIdentifier) communicationIdentifier;
        System.out.println("MeetingUIClientIdentityProvider.provideAvatarFor called for userIdentifier: " + userIdentifier.getId());
        if (userIdentifier.getId().startsWith("8:teamsvisitor:")) {
            callback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_avatar_placeholder_large_pink));
        } else if (userIdentifier.getId().startsWith("8:orgid:")) {
            callback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_doctor_avatar));
        } else if (userIdentifier.getId().startsWith("8:acs:")) {
            callback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_avatar_placeholder_large_green));
        }
    }
}
```

MeetingUIClientCallIdentityProvider インターフェイスのその他の必須メソッドをクラスに追加します。空の実装のままにすることもできます。

```java
@Override
public void provideDisplayNameFor(CommunicationIdentifier communicationIdentifier, MeetingUIClientCallIdentityProviderCallback callback) {
}

@Override
public void provideSubTitleFor(CommunicationIdentifier communicationIdentifier, MeetingUIClientCallIdentityProviderCallback callback) {
}
```
## <a name="receive-information-about-user-actions-in-the-ui-and-add-your-own-custom-functionalities"></a>UI でのユーザー操作に関する情報を受け取り、独自のカスタム機能を追加します。

### <a name="call-screen"></a>呼び出し画面

`MeetingUIClientCallUserEventListener` インターフェイス メソッドは、リモート参加者のプロファイルのユーザー操作で呼び出されます。

クラスに `MeetingUIClientCallUserEventListener` を追加します。

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientCallUserEventListener {
```

パラメーター `this` で `setMeetingUIClientCallUserEventListener` を呼び出します。

```java
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        MeetingUIClientCall meetingUIClientCall = meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallUserEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```
`onNamePlateOptionsClicked` メソッドを追加して実装し、それぞれの `userIdentifier` を対応する通話参加者ユーザーにマップします。
このメソッドは、通話メイン画面でユーザー タイトル テキストを 1 回タップすると呼び出されます。

```java
@Override
public void onNamePlateOptionsClicked(@NonNull Activity activity, @NonNull CommunicationIdentifier communicationIdentifier) {
    if(communicationIdentifier instanceof CommunicationUserIdentifier) {
        CommunicationUserIdentifier userIdentifier = (CommunicationUserIdentifier) communicationIdentifier;
        if (userIdentifier.getId().startsWith("8:acs:")) {
            // Custom behavior based on the user here.
            System.out.println("On user name plate clicked");
        }
    }
}
```

`onParticipantViewLongPressed` メソッドを追加して実装し、それぞれの `userIdentifier` を対応する通話参加者ユーザーにマップします。
このメソッドは、通話メイン画面でユーザー タイルを長押ししたときに呼び出されます。 長押しのカスタム処理の場合は `true` が返され、長押しの既定処理の場合は `false` が返されます。

```java
@Override
public boolean onParticipantViewLongPressed(@NonNull Activity activity, @NonNull CommunicationIdentifier communicationIdentifier) {
    if(communicationIdentifier instanceof CommunicationUserIdentifier) {
        CommunicationUserIdentifier userIdentifier = (CommunicationUserIdentifier) communicationIdentifier;
        if (userIdentifier.getId().startsWith("8:acs:")) {
            // Custom behavior based on the user here.
            System.out.println("On participant tile long pressed");
            return true;
        }
        return false;
    }
}
```

### <a name="call-roster"></a>呼び出し名簿

`MeetingUIClientCallRosterDelegate` インターフェイス メソッドは、呼び出し名簿でのユーザー操作で呼び出されます。

クラスに `MeetingUIClientCallRosterDelegate` を追加します。

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientCallRosterDelegate {
```

パラメーター `this` で `setMeetingUIClientCallRosterDelegate` を呼び出します。

```java
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        MeetingUIClientCall meetingUIClientCall = meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallRosterDelegate(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

`onCallParticipantCellTapped` メソッドを追加して実装し、それぞれの `userIdentifier` を対応する通話参加者ユーザーにマップします。
このメソッドは、[呼び出し名簿] 画面の [リモート参加者] セルを 1 回タップすると呼び出されます。 1 回のタップのカスタム処理の場合は `true` が返され、1 回のタップの既定処理の場合は `false` が返されます。

```java
@Override
public boolean onCallParticipantCellTapped(@NonNull Activity activity, @NonNull CommunicationIdentifier communicationIdentifier) {
    if(communicationIdentifier instanceof CommunicationUserIdentifier) {
        CommunicationUserIdentifier userIdentifier = (CommunicationUserIdentifier) communicationIdentifier;
        if (userIdentifier.getId().startsWith("8:acs:")) {
            // Custom behavior based on the user here.
            System.out.println("On call participant cell tapped");
            return true;
        }
        return false;
    }
}
```
## <a name="user-experience-customization"></a>ユーザー エクスペリエンスのカスタマイズ

SDK のユーザー エクスペリエンスは、アプリ固有のアイコンを指定することでカスタマイズできます。 

### <a name="customize-ui-icons-in-a-call-or-meeting"></a>通話または会議の UI アイコンをカスタマイズする
通話または会議に表示されるアイコンは、`MeetingUIClient` で公開されている `public void setIconConfig(Map<MeetingUIClientIconType, Integer> iconConfig)` メソッドを使用してカスタマイズできます。

MeetingUIClient を作成した後、`MeetingUIClientIconType` でサポートされている通話アイコンのアイコン構成 `meetingUIClient.setIconConfig(getIconConfig())` を設定します。

```java
private MeetingUIClient createMeetingUIClient() {
    MeetingUIClient meetingUIClient = new MeetingUIClient(credential);
    meetingUIClient.setIconConfig(getIconConfig());
}

private Map<IconType, Integer> getIconConfig() {
    Map<IconType, Integer> iconConfig = new HashMap<>();
    iconConfig.put(MeetingUIClientIconType.VIDEO_OFF, R.drawable.video_camera_off);
    iconConfig.put(MeetingUIClientIconType.VIDEO_ON, R.drawable.video_camera);
    iconConfig.put(MeetingUIClientIconType.MIC_ON, R.drawable.microphone_fill);
    iconConfig.put(MeetingUIClientIconType.MIC_OFF, R.drawable.microphone_off);
    iconConfig.put(MeetingUIClientIconType.MIC_PROHIBITED, R.drawable.mic_none);
    iconConfig.put(MeetingUIClientIconType.DEVICE_AUDIO, R.drawable.device_filled);
    iconConfig.put(MeetingUIClientIconType.SPEAKER, R.drawable.volume_high);
    iconConfig.put(MeetingUIClientIconType.SPEAKER_OFF, R.drawable.speaker_off);
    iconConfig.put(MeetingUIClientIconType.HEADSET, R.drawable.headset);
    iconConfig.put(MeetingUIClientIconType.BLUETOOTH, R.drawable.bluetooth_audio);
    iconConfig.put(MeetingUIClientIconType.HANGUP, R.drawable.close_app_bar);
    return iconConfig;
}
```

## <a name="perform-operations-with-the-call"></a>通話での操作を実行する

通話制御アクションは、`MeetingUIClientCall` に存在するメソッドを通じて公開されます。
これらのメソッドは、通話アクションを制御するのに役立ちます。

meetingUIClientCall の変数の追加

```java
public class MainActivity extends AppCompatActivity {
    private MeetingUIClientCall meetingUIClientCall;
```
Join メソッドの戻り値から meetingUIClientCall 変数を割り当てる

```java
private void joinGroupCall() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        UUID groupUUID = UUID.fromString("<GROUP_ID>");
        MeetingUIClientGroupCallLocator meetingUIClientGroupCallLocator = new MeetingUIClientGroupCallLocator(groupUUID);
        meetingUIClientCall = meetingUIClient.join(meetingUIClientGroupCallLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join group call: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```
### <a name="mute-and-unmute"></a>ミュートとミュート解除

`mute` メソッドを呼び出して、アクティブな通話がある場合にマイクをミュートします。
マイクの状態の変化は、`MeetingUIClientCallEventListener` の `onIsMutedChanged` メソッドで通知されます。

```java
// Mute the microphone for an active call.
public void mute() {
    try {
        meetingUIClientCall.mute();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Mute call failed: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

`unmute` メソッドを呼び出して、アクティブな通話がある場合にマイクのミュートを解除します。

```java
// Unmute the microphone for an active call.
public void unmute() {
    try {
        meetingUIClientCall.unmute();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Unmute call failed: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```
### <a name="other-operations-available-in-from-the--meetinguiclientcall-class"></a>`MeetingUIClientCall` クラスから使用できるその他の操作。

```java
// Start the video for an active call.
public void startVideo()

// Stop the video for an active call.
public void stopVideo()

// Set the preferred audio route in the call for self user.
public void setAudioRoute(MeetingUIClientAudioRoute audioRoute)

// Raise the hand of current user for an active call.
public void raiseHand()

// Lower the hand of current user for an active call.
public void lowerHand()

// Change the layout in the call for self user.
public List<MicrosoftTeamsSDKLayoutMode> getSupportedLayoutModes()
public void changeLayout(MeetingUIClientLayoutMode microsoftTeamsSDKLayoutMode)

// Hang up the call or leave the meeting.
public void hangUp()
```

## <a name="use-teams-embed-sdk-and-azure-communication-calling-sdk-in-the-same-app"></a>Teams Embed SDK と Azure Communication Calling SDK を同じアプリで使用する

Teams Embed SDK では内部に Azure Communication Calling SDK (ACS) も用意されているため、同じアプリで両方の SDK 機能を使用できます。 一度に初期化して使用できるのは、1 つの SDK のみです。 両方の SDK を同時に初期化して使用すると、予期しない動作が発生します。 

以下のインポートをクラスに追加します
```java
import com.azure.android.communication.calling.Call;
import com.azure.android.communication.calling.CallAgent;
import com.azure.android.communication.calling.CallClient;
import com.azure.android.communication.calling.CallState;
import com.azure.android.communication.calling.GroupCallLocator;
import com.azure.android.communication.calling.JoinCallOptions;
import com.azure.android.communication.common.CommunicationTokenCredential;
```

ACS の使用のための変数を宣言します
```java
public class MainActivity extends AppCompatActivity {
    private CallAgent agent;
    private CallClient mCallClient;
    private Call mCall;
```

初期化を行うには、新しい `CallClient` を作成します。 作成したものを `joinAcsCall` または他のメソッドに追加します。

```java
private void joinAcsCall() {
    if (mCallClient == null) {
        mCallClient = new CallClient();
    }
}
```
すべての ACS API は、そのドキュメントに記載されているように使用してください。 このドキュメントでは、API の使用方法については説明しません。 

使用する必要がなくなったか、Teams Embed SDK をアプリで使用することが必要になったら、ACS SDK を破棄し、`null` をその変数に設定します。
```java
private void stopAcs() {
    mCall = null;
    agent.dispose();
    agent = null;
    mCallClient.dispose();
    mCallClient = null;
}
```

Teams Embed SDK の初期化は、`MeetingUIClient` の作成時にも実行されます。 作成したものを `createMeetingUIClient` または他のメソッドに追加します。
```java
private MeetingUIClient createMeetingUIClient() { 
    try {
        CommunicationTokenRefreshOptions refreshOptions = new CommunicationTokenRefreshOptions(tokenRefresher, true, "<USER_ACCESS_TOKEN>");
        CommunicationTokenCredential credential = new CommunicationTokenCredential(refreshOptions);
        return new MeetingUIClient(credential);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting ui client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```
Teams Embed SDK API は、そのドキュメントに記載されているように使用してください。 このドキュメントでは、API の使用方法については説明しません。 

使用する必要がなくなったか、ACS SDK をアプリで使用することが必要になったら、Teams Embed SDK を破棄し、`null` をその変数に設定します。
```java
private void disposeTeamsSdk() {
    try {
        meetingUIClient.dispose();
        meetingUIClientCall = null;
        meetingUIClient = null;
    } catch (Exception ex) {
        Toast.makeText(getContext(), "Failed to teardown Teams Sdk: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Teams Embed SDK の破棄は、アクティブな通話がない場合にのみ可能です。 アクティブな通話がある場合、`meetingUIClient.dispose()` は例外を返します。 アクティブな通話を切断し、`disposeTeamsSdk()` を呼び出します。

```java
private void endMeeting() {
    try {
        meetingUIClientCall.hangUp();
        disposeTeamsSdk();
    } catch (Exception ex) {
        Toast.makeText(getContext(), "Failed to end meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

`MeetingUIClientCallEventListener` インターフェイス メソッド `onCallStateChanged(MeetingUIClientCallState callState)` を実装し、終了したアクティブな通話に関する状態の更新を取得して、その後、 Teams Embed SDK を破棄します。
```java
@Override
public void onCallStateChanged(MeetingUIClientCallState callState) {
    switch(callState) {
        case ENDED:
            disposeTeamsSdk();
            break;
        default:
        System.out.println("Call state changed to: " + callState.toString());
    }
}
```

その他の必須の `MeetingUIClientCallEventListener` インターフェイス メソッドをクラスに追加します

```java
@Override
public void onRemoteParticipantCountChanged(int newCount) {
    System.out.println("Remote participant count has changed to: " + newCount);
}

@Override
public void onIsMutedChanged() {
    System.out.println("Mute state changed to: " + meetingUIClientCall.isMuted());
}

@Override
public void onIsSendingVideoChanged() {
    System.out.println("Sending video state changed to: " + meetingUIClientCall.isSendingVideo());
}

@Override
public void onIsHandRaisedChanged(List<String> participantIds) {
    System.out.println("Self participant raise hand status changed to: " + meetingUIClientCall.isHandRaised());
}
```

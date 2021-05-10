---
title: Android 用の Azure Communication Services Teams Embed の使用
description: この概要では、Android 用の Azure Communication Services Teams Embed ライブラリを使用する方法について説明します。
author: palatter
ms.author: palatter
ms.date: 24/02/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 711c738adaaad8aff1e1f56b537b41a846e528db
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803119"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../create-communication-resource.md)。
- 通話クライアントを有効にするための `User Access Token`。 [`User Access Token` を取得する方法](../../access-tokens.md)についての詳細
- [アプリケーションへの Teams Embed の追加の概要](../getting-started-with-teams-embed.md)に関するクイックスタートを完了します

## <a name="teams-embed-events"></a>Teams Embed イベント

クラスに `MeetingEventListener` を追加します。

```java
public class MainActivity extends AppCompatActivity implements MeetingEventListener {

    private MeetingUIClient meetingUIClient;
```

`this` で `MeetingEventListener` を設定します。

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    createMeetingClient();
}

private void createMeetingClient() {
    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(tokenRefresher, true, ACS_TOKEN);
        meetingUIClient = new MeetingUIClient(credential);
        meetingUIClient.setMeetingEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

`onCallStateChanged` および `onRemoteParticipantCountChanged` メソッドを実装します。

```java
@Override
public void onCallStateChanged(CallState callState) {
    switch(callState) {
        case CONNECTING:
            System.out.println("Call state changed to 'Connecting'");
            break;
        case CONNECTED:
            System.out.println("Call state changed to 'Connected'");
            break;
        case WAITING_IN_LOBBY:
            System.out.println("Call state changed to 'Waiting in Lobby'");
            break;
        case ENDED:
            System.out.println("Call state changed to 'Ended'");
            break;
    }
}

@Override
public void onRemoteParticipantCountChanged(int newCount) {
    System.out.println("Remote participant count changed: " + newCount);
}
```

## <a name="assigning-avatars-for-users"></a>ユーザーにアバターを割り当てる

クラスに `MeetingIdentityProvider` を追加します。

```java
public class MainActivity extends AppCompatActivity implements MeetingIdentityProvider {

    private MeetingUIClient meetingUIClient;
```

`this` で `MeetingIdentityProvider` を設定します。

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    createMeetingClient();
}

private void createMeetingClient() {
    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(tokenRefresher, true, ACS_TOKEN);
        meetingUIClient = new MeetingUIClient(credential);
        meetingUIClient.setMeetingIdentityProvider(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

各 `userMri` を対応するアバターにマップします。

```java
@Override
public void provideAvatarFor(String userIdentifier, MeetingIdentityProviderCallback meetingIdentityProviderCallback) {
    Drawable myAvatar = null;
    try {
        System.out.println("MicrosoftTeamsSDKIdentityProvider.requestForAvatar called for userIdentifier: " + userIdentifier);
        if (userIdentifier.startsWith("8:teamsvisitor:")) {
            // get and provide avatar picture asynchronously with long fetching/decoding delay.
            System.out.println("update avatar for Anonymous user");
            String imageUrl = "https://mlccdn.blob.core.windows.net/dev/LWA/qingy/doughboy_36x36.png";
            updateAvatarFromUrl(imageUrl, meetingIdentityProviderCallback);
        } else if (userIdentifier.startsWith("8:orgid:")) {
            System.out.println("update avatar for OrgID user");
            String imageUrl = "https://mlccdn.blob.core.windows.net/dev/LWA/qingy/qingy_120.jpg";
            updateAvatarFromUrl(imageUrl, meetingIdentityProviderCallback);
        } else if (userIdentifier.startsWith("8:acs:")) {
            System.out.println("update avatar for ACS user");
            String imageUrl = "https://mlccdn.blob.core.windows.net/dev/LWA/qingy/msudan.png";
            updateAvatarFromUrl(imageUrl, meetingIdentityProviderCallback);
        }
    } catch (Exception e) {
        System.out.println("MicrosoftTeamsSDKIdentityProvider: Exception while requestForAvatar for userIdentifier: " + userIdentifier + e.getMessage());
    }
}

/**
    * download and callback to set the Avatar image from web URL.
    * We have a few sample images on CDN
    *                 String imageUrl = "https://mlccdn.blob.core.windows.net/dev/LWA/qingy/doughboy_36x36.png";
    *                 String imageUrl = "https://mlccdn.blob.core.windows.net/dev/LWA/qingy/qingy_120.jpg";
    *                 String imageUrl = "https://mlccdn.blob.core.windows.net/dev/LWA/qingy/msudan.png";
    */
private void updateAvatarFromUrl(String url, MeetingIdentityProviderCallback meetingIdentityProviderCallback) {
    String urlImage = url;
    WeakReference<MeetingIdentityProviderCallback> weakReferenceObserver = new WeakReference<>(meetingIdentityProviderCallback);
    new AsyncTask<String, Integer, Drawable>(){
        @Override
        protected Drawable doInBackground(String... strings) {
            Bitmap bitmap = null;
            try {
                HttpURLConnection connection = (HttpURLConnection) new URL(urlImage).openConnection();
                connection.connect();
                InputStream input = connection.getInputStream();
                bitmap = BitmapFactory.decodeStream(input);
            } catch (IOException e) {
                e.printStackTrace();
            }
            Drawable d = new BitmapDrawable(getResources(), bitmap);
            return d;
        }
        protected void onPostExecute(Drawable myAvatar) {
            // provide avatar when it is available.
            MeetingIdentityProviderCallback callback = weakReferenceObserver.get();
            if (callback != null) {
                System.out.println("invoke the callback method with fetched avatar");
                callback.onAvatarAvailable(myAvatar);
            } else {
                System.out.println("callback observer are reclaimed, there is no need to update avatar in UI anymore");
            }
        }
    }.execute();
}
```

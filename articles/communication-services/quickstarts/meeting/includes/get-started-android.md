---
title: クイックスタート - Azure Communication Services を使用して Teams 会議への参加を Android アプリに追加する
description: このクイックスタートでは、Android 用の Azure Communication Services Teams Embed ライブラリを使用する方法について説明します。
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5ac4c53550468d33e9ed533303749d29e772d766
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108479"
---
このクイックスタートでは、Android 用の Azure Communication Services Teams Embed ライブラリを使用して Teams 会議に参加する方法について説明します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Android アプリケーションを作成するための [Android Studio](https://developer.android.com/studio)。
- デプロイ済みの Communication Services リソース。 [Communication Services のリソースを作成する](../../create-communication-resource.md)。
- Azure Communication Service の[ユーザー アクセス トークン](../../access-tokens.md)。

## <a name="setting-up"></a>設定

### <a name="create-an-android-app-with-an-empty-activity"></a>空のアクティビティで Android アプリを作成する

Android Studio で、[Start a new Android Studio project]\(新しい Android Studio プロジェクトを開始する\) を選択します。

:::image type="content" source="../media/android/studio-new-project.png" alt-text="Android Studio で [Start a new Android Studio project]\(新しい Android Studio プロジェクトを開始する\) ボタンが選択された状態を示すスクリーンショット。":::

[Phone and Tablet]\(電話およびタブレット\) で、[Empty Activity]\(空のアクティビティ\) プロジェクト テンプレートを選択します。

:::image type="content" source="../media/android/studio-blank-activity.png" alt-text="プロジェクト テンプレート画面で [Empty Activity]\(空のアクティビティ\) オプションが選択された状態を示すスクリーンショット。":::

プロジェクトに `TeamsEmbedAndroidGettingStarted` という名前を付け、言語を java に設定して、[Minimum SDK]\(最小 SDK\) に "API 21: Android 5.0 (Lollipop)" 以上を選択します。

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="プロジェクト テンプレート画面 2 で [Empty Activity]\(空のアクティビティ\) オプションが選択された状態を示すスクリーンショット。":::


### <a name="install-the-azure-package"></a>Azure パッケージをインストールする

アプリ レベルの build.gradle で、次の行を dependencies および android セクションに追加します

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
}
```

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-common:1.0.0-beta.6'
    ...
}
```

### <a name="install-the-teams-embed-package"></a>Teams Embed パッケージをインストールする

`MicrosoftTeamsSDK` パッケージをダウンロードします。

次に、MicrosoftTeamsSDK フォルダーをプロジェクトの app フォルダーに解凍します。 例: `TeamsEmbedAndroidGettingStarted/app/MicrosoftTeamsSDK`.

### <a name="add-teams-embed-package-to-your-buildgradle"></a>Teams Embed パッケージを build.gradle に追加する

アプリレベルの `build.gradle` で、ファイルの末尾に次の行を追加します。

```groovy
apply from: 'MicrosoftTeamsSDK/MicrosoftTeamsSDK.gradle'
```

プロジェクトを Gradle のファイルと同期する

### <a name="create-application-class"></a>アプリケーション クラスを作成する

`TeamsEmbedAndroidGettingStarted` という名前の新しい Java クラス ファイルを作成します。 これがアプリケーション クラスになります。このアプリケーション クラスは、`TeamsSDKApplication` を拡張したものであることが必要です。 [Android ドキュメント](https://developer.android.com/reference/android/app/Application)

:::image type="content" source="../media/android/application-class-location.png" alt-text="Android Studio でのアプリケーション クラスの作成場所を示すスクリーンショット":::

```java
package com.microsoft.teamsembedandroidgettingstarted;

import com.microsoft.teamssdk.app.TeamsSDKApplication;

public class TeamsEmbedAndroidGettingStarted extends TeamsSDKApplication {
}
```

### <a name="update-themes"></a>テーマを更新する

`theme.xml` ファイルと `theme.xml (night)` ファイルの両方で、スタイル名を `AppTheme` に設定します。

:::image type="content" source="../media/android/theme-settings.png" alt-text="Android Studio における theme.xml ファイルを示すスクリーンショット":::

```xml
<style name="AppTheme" parent="Theme.AppCompat.DayNight.DarkActionBar">
```

```xml
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
```

### <a name="add-permissions-to-application-manifest"></a>アプリケーション マニフェストにアクセス許可を追加する

アプリケーション マニフェスト (`app/src/main/AndroidManifest.xml`) に `RECORD_AUDIO` アクセス許可を追加します。  


```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
    <uses-permission android:name="android.permission.RECORD_AUDIO"/>
    <application
```

### <a name="add-app-name-and-theme-to-application-manifest"></a>アプリケーション マニフェストにアプリ名とテーマを追加する

マニフェストに `xmlns:tools="http://schemas.android.com/tools" を追加します。

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
```

`.TeamsEmbedAndroidGettingStarted` を `android:name` に追加し、`android:name` を `tools:replace` に追加して、`android:theme` を `@style/AppTheme` に変更します

```xml
<application
    android:name=".TeamsEmbedAndroidGettingStarted"
    tools:replace="android:name"
    android:theme="@style/AppTheme"
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true">
```

### <a name="set-up-the-layout-for-the-app"></a>アプリのレイアウトを設定する

`join_meeting` という ID でボタンを作成します。 (`app/src/main/res/layout/activity_main.xml`) に移動して、ファイルの内容を以下に置き換えます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/join_meeting"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Join Meeting"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>メイン アクティビティのスキャフォールディングとバインドを作成する

レイアウトを作成したら、アクティビティの基本的なスキャフォールディングを、必要なバインディングと共に追加できます。 ボタンが押されたとき、このアクティビティによって実行時のアクセス許可の要求、会議クライアントの作成、会議への参加が行われます。 それぞれについては、その独自のセクションに説明があります。 

`onCreate` メソッドがオーバーライドされて、`getAllPermissions` と `createAgent` が呼び出され、[`Join Meeting`]\(会議に参加する\) ボタンのバインドが追加されます。 これは、アクティビティの作成時に 1 回だけ行われます。 `onCreate` の詳細については、ガイド「[アクティビティのライフサイクルについて](https://developer.android.com/guide/components/activities/activity-lifecycle)」を参照してください。

**MainActivity.java** に移動し、内容を次のコードに置き換えます。

```java
package com.yourcompany.teamsembedandroidgettingstarted;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.common.CommunicationTokenRefreshOptions;
import com.azure.android.communication.ui.meetings.MeetingJoinOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClient;

import java.util.ArrayList;
import java.util.concurrent.Callable;

public class MainActivity extends AppCompatActivity {

    private final String displayName = "John Smith";

    private MeetingUIClient meetingUIClient;
    private MeetingJoinOptions meetingJoinOptions;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        meetingJoinOptions = new MeetingJoinOptions(displayName);
        
        getAllPermissions();
        createMeetingClient();

        Button joinMeeting = findViewById(R.id.join_meeting);
        joinMeeting.setOnClickListener(l -> joinMeeting());
    }

    private void createMeetingClient() {
        // See section on creating meeting client
    }

    private void joinMeeting() {
        // See section on joining a meeting
    }

    private void getAllPermissions() {
        // See section on getting permissions
    }
}
```

### <a name="request-permissions-at-runtime"></a>実行時にアクセス許可を要求する

Android 6.0 以上 (API レベル 23) と `targetSdkVersion` 23 以上では、アプリのインストール時ではなく、実行時にアクセス許可が付与されます。 これをサポートするために、`getAllPermissions` を実装して、必要なアクセス許可ごとに `ActivityCompat.checkSelfPermission` と `ActivityCompat.requestPermissions` を呼び出すことができます。

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 202);
    }
}
```

> [!NOTE]
> アプリを設計するときは、これらのアクセス許可を要求するタイミングを検討してください。 アクセス許可は、事前に要求するのではなく、必要が生じたときに要求する必要があります。 詳細については、[Android のアクセス許可のガイド](https://developer.android.com/training/permissions/requesting)を参照してください。

## <a name="object-model"></a>オブジェクト モデル

Azure Communication Services Teams Embed ライブラリが備える主な機能のいくつかは、次のクラスとインターフェイスにより処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient| MeetingUIClient は、Teams Embed ライブラリへのメイン エントリ ポイントです。 |
| MeetingJoinOptions | MeetingJoinOptions は、表示名など、構成可能なオプションに使用されます。 |
| CallState | CallState は、呼び出し状態の変化をレポートする目的で使用されます。 `connecting`、`waitingInLobby`、`connected`、`ended` の各オプションがあります。 |

## <a name="create-a-meetingclient-from-the-user-access-token"></a>ユーザー アクセス トークンから MeetingClient を作成する

認証済みの会議クライアントは、ユーザー アクセス トークンを使用してインスタンス化することができます。 このトークンは通常、サービスがアプリケーションに固有の認証を使用して生成します。 ユーザー アクセス トークンの詳細については、[ユーザー アクセス トークン](../../access-tokens.md)に関するガイドを参照してください。 クイック スタートでは、`<USER_ACCESS_TOKEN>` を Azure Communication Service リソース用に生成されたユーザー アクセス トークンに置き換えます。

```java
private void createMeetingClient() {
    try {
        CommunicationTokenRefreshOptions refreshOptions = new CommunicationTokenRefreshOptions(tokenRefresher, true, "<USER_ACCESS_TOKEN>");
        CommunicationTokenCredential credential = new CommunicationTokenCredential(refreshOptions);
        meetingUIClient = new MeetingUIClient(credential);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="setup-token-refreshing"></a>セットアップ トークンを更新する

Callable `tokenRefresher` メソッドを作成します。 次に、ユーザー トークンを取得する `fetchToken` メソッドを作成します。 [その手順は、こちらでご覧いただけます](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-java)。

```java
Callable<String> tokenRefresher = () -> {
    return fetchToken();
};

public String fetchToken() {
    // Get token
    return USER_ACCESS_TOKEN;
}
```

## <a name="get-the-teams-meeting-link"></a>Teams 会議のリンクを取得する

Teams 会議のリンクは、Graph API を使用して取得できます。 この点については、[Graph のドキュメント](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)で詳しく説明されています。
Communication Services 通話 SDK は、Teams 会議のフル リンクを受け入れます。 このリンクは、`onlineMeeting` リソースの一部として返され、[`joinWebUrl` プロパティ](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)からアクセスできます。必要な会議情報は、Teams 会議の招待自体に含まれる **[会議に参加]** の URL から取得することもできます。

## <a name="start-a-meeting-using-the-meeting-client"></a>会議クライアントを使用して会議を開始する

会議には、`MeetingClient` を使用して参加できます。その際に必要なのは、`meetingURL` と `JoinOptions` だけです。 `<MEETING_URL>` は、Teams 会議の URL に置き換えてください。

```java
/**
 * Join a meeting with a meetingURL.
 */
private void joinMeeting() {
    try {
        meetingUIClient.join("<MEETING_URL>", meetingJoinOptions);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="launch-the-app-and-join-a-meeting"></a>アプリを起動して会議に参加する

ツール バーの [Run App]\(アプリの実行\) ボタン (Shift + F10) を使用してアプリを起動できるようになりました。 

:::image type="content" source="../media/android/quickstart-android-join-meeting.png" alt-text="完成したアプリケーションを示すスクリーンショット。":::

:::image type="content" source="../media/android/quickstart-android-joined-meeting.png" alt-text="完成したアプリケーションを示すスクリーンショット (会議への参加後)。":::

## <a name="sample-code"></a>サンプル コード

サンプル アプリは [GitHub](https://github.com/Azure-Samples/teams-embed-android-getting-started) からダウンロードできます

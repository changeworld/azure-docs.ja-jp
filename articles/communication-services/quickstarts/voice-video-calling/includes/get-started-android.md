---
title: クイック スタート - Azure Communication Services を使用して Android アプリに VOIP 通話を追加する
description: このチュートリアルでは、Android 用の Azure Communication Services 通話クライアント ライブラリを使用する方法について説明します
author: matthewrobertson
ms.author: marobert
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: a387261b392ea6718941f5eabe889e0c1a41fd5a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750608"
---
このクイックスタートでは、Android 用の Azure Communication Services 通話クライアント ライブラリを使用して、通話を開始する方法について説明します。

> [!NOTE]
> このドキュメントでは、呼び出し元のクライアント ライブラリのバージョン 1.0.0-beta.8 を使用します。

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

最小クライアント ライブラリである"API 26:Android 8.0 (Oreo)" 以上を選択します。

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="プロジェクト テンプレート画面 2 で [Empty Activity]\(空のアクティビティ\) オプションが選択された状態を示すスクリーンショット。":::


### <a name="install-the-package"></a>パッケージをインストールする

<!-- TODO: update with instructions on how to download, install and add package to project -->
プロジェクト レベルの build.gradle を見つけて、`buildscript` および `allprojects` の下のリポジトリの一覧に `mavenCentral()` を追加します
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
次に、モジュール レベルの build.gradle で、次の行を dependencies および android セクションに追加します

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.8'
    ...
}
```

### <a name="add-permissions-to-application-manifest"></a>アプリケーション マニフェストにアクセス許可を追加する

通話を行うために必要なアクセス許可を要求するには、最初にアプリケーション マニフェスト (`app/src/main/AndroidManifest.xml`) でそれを宣言する必要があります。 ファイルの内容を以下に置き換えます。

```xml
    <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.contoso.acsquickstart">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <!--Our calling client library depends on the Apache HTTP client library.
When targeting Android client library 28+, this library needs to be explicitly referenced.
See https://developer.android.com/about/versions/pie/android-9.0-changes-28#apache-p-->
        <uses-library android:name="org.apache.http.legacy" android:required="false"/>
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
    
```

### <a name="set-up-the-layout-for-the-app"></a>アプリのレイアウトを設定する

必要な入力は、通話先 ID のテキスト入力と電話を掛けるためのボタンの 2 つです。 これらは、デザイナーを使用するかレイアウトの xml を編集して追加できます。 ID に `call_button`、テキスト入力に `callee_id` を使用してボタンを作成します。 (`app/src/main/res/layout/activity_main.xml`) に移動して、ファイルの内容を以下に置き換えます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/call_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="16dp"
        android:text="Call"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <EditText
        android:id="@+id/callee_id"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="Callee Id"
        android:inputType="textPersonName"
        app:layout_constraintBottom_toTopOf="@+id/call_button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>メイン アクティビティのスキャフォールディングとバインドを作成する

レイアウトを作成したら、バインドと、アクティビティの基本的なスキャフォールディングを追加できます。 このアクティビティによって、実行時のアクセス許可の要求、通話エージェントの作成、ボタンが押されたときの電話の発信が行われます。 それぞれについては、その独自のセクションに説明があります。 `onCreate` メソッドがオーバーライドされて、`getAllPermissions` と `createAgent` が呼び出され、通話ボタンのバインドが追加されます。 これは、アクティビティの作成時に 1 回だけ行われます。 `onCreate` の詳細については、ガイド「[アクティビティのライフサイクルについて](https://developer.android.com/guide/components/activities/activity-lifecycle)」を参照してください。

**MainActivity.java** に移動し、内容を次のコードに置き換えます。

```java
package com.contoso.acsquickstart;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationUserIdentifier;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.calling.CallAgent;
import com.azure.android.communication.calling.CallClient;
import com.azure.android.communication.calling.StartCallOptions;


import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {
    
    private CallAgent callAgent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getAllPermissions();
        createAgent();
        
        // Bind call button to call `startCall`
        Button callButton = findViewById(R.id.call_button);
        callButton.setOnClickListener(l -> startCall());
    }

    /**
     * Request each required permission if the app doesn't already have it.
     */
    private void getAllPermissions() {
        // See section on requesting permissions
    }

    /**
      * Create the call agent for placing calls
      */
    private void createAgent() {
        // See section on creating the call agent
    }

    /**
     * Place a call to the callee id provided in `callee_id` text input.
     */
    private void startCall() {
        // See section on starting the call
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
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO, Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_PHONE_STATE};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 1);
    }
}
```

> [!NOTE]
> アプリを設計するときは、これらのアクセス許可を要求するタイミングを検討してください。 アクセス許可は、事前に要求するのではなく、必要が生じたときに要求する必要があります。 詳細については、[Android のアクセス許可のガイド](https://developer.android.com/training/permissions/requesting)を参照してください。

## <a name="object-model"></a>オブジェクト モデル

Azure Communication Services 通話クライアント ライブラリが備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient は、通話クライアント ライブラリへのメイン エントリ ポイントです。|
| CallAgent | CallAgent は、通話を開始および管理するために使用します。 |
| CommunicationUserCredential | CommunicationUserCredential は、CallAgent をインスタンス化するためのトークン資格情報として使用されます。|
| CommunicationIdentifier | CommunicationIdentifier は、呼び出しの一部となる可能性があるさまざまな種類の参加者として使用されます。|

## <a name="create-an-agent-from-the-user-access-token"></a>ユーザー アクセス トークンからエージェントを作成する

認証された通話エージェントは、ユーザー トークンを使用してインスタンス化できます。 通常このトークンは、アプリケーション固有の認証を使用してサービスから生成されます。 ユーザー アクセス トークンの詳細については、[ユーザー アクセス トークン](../../access-tokens.md)のガイドを参照してください。 クイック スタートでは、`<User_Access_Token>` を Azure Communication Service リソース用に生成されたユーザー アクセス トークンに置き換えます。

```java

/**
 * Create the call agent for placing calls
 */
private void createAgent() {
    String userToken = "<User_Access_Token>";

    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(userToken);
        callAgent = new CallClient().createCallAgent(getApplicationContext(), credential).get();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create call agent.", Toast.LENGTH_SHORT).show();
    }
}

```

## <a name="start-a-call-using-the-call-agent"></a>通話エージェントを使用して通話を開始する

電話の発信は通話エージェントを使用して行うことができ、必要となるのは、通話先 ID の一覧と通話オプションを指定することだけです。 クイックスタートでは、動画を含まない既定の通話オプションと、テキスト入力による 1 つの通話先 ID が使用されます。

```java
/**
 * Place a call to the callee id provided in `callee_id` text input.
 */
private void startCall() {
    EditText calleeIdView = findViewById(R.id.callee_id);
    String calleeId = calleeIdView.getText().toString();
    
    StartCallOptions options = new StartCallOptions();

    callAgent.call(
        getApplicationContext(),
        new CommunicationUserIdentifier[] {new CommunicationUserIdentifier(calleeId)},
        options);
}
```


## <a name="launch-the-app-and-call-the-echo-bot"></a>アプリを起動してエコー ボットを呼び出す

ツール バーの [Run App]\(アプリの実行\) ボタン (Shift + F10) を使用してアプリを起動できるようになりました。 `8:echo123` を呼び出して電話を発信できることを確認してください。 事前に録音したメッセージが再生された後、そのメッセージがもう一度繰り返されます。

:::image type="content" source="../media/android/quickstart-android-call-echobot.png" alt-text="完成したアプリケーションを示すスクリーンショット。":::

## <a name="sample-code"></a>サンプル コード

サンプル アプリは [GitHub](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/Add%20Voice%20Calling) からダウンロードできます

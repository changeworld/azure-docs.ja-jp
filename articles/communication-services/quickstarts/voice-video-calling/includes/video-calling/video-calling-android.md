---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: rifox
ms.openlocfilehash: d53c9a7d265366531cc22d6de726f84ddb9f7a50
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910777"
---
Communication Services 通話クライアント ライブラリを使用して 1 対 1 のビデオ通話をアプリに追加することによって、Azure Communication Services の使用を開始します。 このクイックスタートでは、Android 用の Azure Communication Services Calling SDK を使用して、ビデオ通話を開始および応答する方法について説明します。

## <a name="sample-code"></a>サンプル コード

最後までスキップする場合は、[GitHub](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/videoCallingQuickstart) からサンプル アプリをダウンロードします。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Android アプリケーションを作成するための [Android Studio](https://developer.android.com/studio)。
- デプロイ済みの Communication Services リソース。 [Communication Services のリソースを作成する](../../../create-communication-resource.md)。
- Azure Communication Service の[ユーザー アクセス トークン](../../../access-tokens.md)。

### <a name="create-an-android-app-with-an-empty-activity"></a>空のアクティビティで Android アプリを作成する

Android Studio で、[Start a new Android Studio project]\(新しい Android Studio プロジェクトを開始する\) を選択します。

:::image type="content" source="../../media/android/studio-new-project.png" alt-text="Android Studio で [Start a new Android Studio project]\(新しい Android Studio プロジェクトを開始する\) ボタンが選択された状態を示すスクリーンショット。":::

[Phone and Tablet]\(電話およびタブレット\) で、[Empty Activity]\(空のアクティビティ\) プロジェクト テンプレートを選択します。

:::image type="content" source="../../media/android/studio-blank-activity.png" alt-text="プロジェクト テンプレート画面で [Empty Activity]\(空のアクティビティ\) オプションが選択された状態を示すスクリーンショット。":::

[Minimum SDK]\(最小 SDK\) として "API 26: Android 8.0 (Oreo)" 以上を選択します。

:::image type="content" source="../../media/android/studio-calling-min-api.png" alt-text="プロジェクト テンプレート画面 2 で [Empty Activity]\(空のアクティビティ\) オプションが選択された状態を示すスクリーンショット。":::

### <a name="install-the-package"></a>パッケージをインストールする

プロジェクト レベルの `build.gradle` を見つけて、`buildscript` および `allprojects` の下のリポジトリの一覧に `mavenCentral()` を追加します
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
次に、モジュール レベルの `build.gradle` で、次の行を dependencies および android セクションに追加します。

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
    implementation 'com.azure.android:azure-communication-calling:1.0.0'
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

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <!--Our Calling SDK depends on the Apache HTTP SDK.
When targeting Android SDK 28+, this library needs to be explicitly referenced.
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

通話先 ID のテキスト入力、電話をかけるためのボタン、および電話を切るためのボタンが必要です。 また、ローカル ビデオをオンまたはオフにするための 2 つのボタンも必要です。 ローカルとリモートのビデオ ストリーム用に 2 つのコンテナーを配置する必要があります。 これらは、デザイナーを使用するかレイアウトの xml を編集して追加できます。 `app/src/main/res/layout/activity_main.xml` に移動して、ファイルの内容を以下に置き換えます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical">

            <EditText
                android:id="@+id/callee_id"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:ems="10"
                android:gravity="center"
                android:hint="Callee Id"
                android:inputType="textPersonName"
                app:layout_constraintBottom_toTopOf="@+id/call_button"
                app:layout_constraintEnd_toEndOf="parent"
                app:layout_constraintStart_toStartOf="parent"
                app:layout_constraintTop_toTopOf="parent"
                app:layout_constraintVertical_bias="0.064" />
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content">
                <Button
                    android:id="@+id/call_button"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="16dp"
                    android:gravity="center"
                    android:text="Call"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintStart_toStartOf="parent" />
                <Button
                    android:id="@+id/show_preview"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="16dp"
                    android:gravity="center"
                    android:text="Show Video"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintStart_toStartOf="parent" />
                <Button
                    android:id="@+id/hide_preview"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="16dp"
                    android:gravity="center"
                    android:text="Hide Video"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintStart_toStartOf="parent" />
                <Button
                    android:id="@+id/hang_up"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="16dp"
                    android:gravity="center"
                    android:text="Hang Up"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintStart_toStartOf="parent" />
            </LinearLayout>
            <FrameLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent">
                <LinearLayout
                    android:id="@+id/remotevideocontainer"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:gravity="end"
                    android:orientation="horizontal"
                    android:padding="10dp"></LinearLayout>
                <LinearLayout
                    android:id="@+id/localvideocontainer"
                    android:layout_width="180dp"
                    android:layout_height="320dp"
                    android:layout_gravity="right|bottom"
                    android:orientation="horizontal"
                    android:padding="10dp"></LinearLayout>

            </FrameLayout>
        </LinearLayout>
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>メイン アクティビティのスキャフォールディングとバインドを作成する

レイアウトを作成したら、バインドと、アクティビティの基本的なスキャフォールディングを追加できます。 このアクティビティによって、実行時のアクセス許可の要求、通話エージェントの作成、ボタンが押されたときの電話の発信が行われます。 それぞれについては、その独自のセクションに説明があります。 `onCreate` メソッドがオーバーライドされて、`getAllPermissions` と `createAgent` が呼び出され、通話ボタンのバインドが追加されます。 これは、アクティビティの作成時に 1 回だけ行われます。 `onCreate` の詳細については、ガイド「[アクティビティのライフサイクルについて](https://developer.android.com/guide/components/activities/activity-lifecycle)」を参照してください。

**MainActivity.java** に移動し、内容を次のコードに置き換えます。

```java
package com.example.acsquickstart;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.media.AudioManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;
import android.widget.LinearLayout;
import android.content.Context;
import com.azure.android.communication.calling.CallState;
import com.azure.android.communication.calling.CallingCommunicationException;
import com.azure.android.communication.calling.CameraFacing;
import com.azure.android.communication.calling.ParticipantsUpdatedListener;
import com.azure.android.communication.calling.PropertyChangedEvent;
import com.azure.android.communication.calling.PropertyChangedListener;
import com.azure.android.communication.calling.VideoDeviceInfo;
import com.azure.android.communication.common.CommunicationUserIdentifier;
import com.azure.android.communication.common.CommunicationIdentifier;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.calling.CallAgent;
import com.azure.android.communication.calling.CallClient;
import com.azure.android.communication.calling.StartCallOptions;
import com.azure.android.communication.calling.DeviceManager;
import com.azure.android.communication.calling.VideoOptions;
import com.azure.android.communication.calling.LocalVideoStream;
import com.azure.android.communication.calling.VideoStreamRenderer;
import com.azure.android.communication.calling.VideoStreamRendererView;
import com.azure.android.communication.calling.CreateViewOptions;
import com.azure.android.communication.calling.ScalingMode;
import com.azure.android.communication.calling.IncomingCall;
import com.azure.android.communication.calling.Call;
import com.azure.android.communication.calling.AcceptCallOptions;
import com.azure.android.communication.calling.ParticipantsUpdatedEvent;
import com.azure.android.communication.calling.RemoteParticipant;
import com.azure.android.communication.calling.RemoteVideoStream;
import com.azure.android.communication.calling.RemoteVideoStreamsEvent;
import com.azure.android.communication.calling.RendererListener;


import java.util.ArrayList;
import java.util.List;
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;

public class MainActivity extends AppCompatActivity {

    private CallAgent callAgent;
    private LocalVideoStream currentVideoStream;
    private DeviceManager deviceManager;
    private IncomingCall incomingCall;
    private Call call;
    VideoStreamRenderer previewRenderer;
    VideoStreamRendererView preview;
    final Map<Integer, StreamData> streamData = new HashMap<>();
    private boolean renderRemoteVideo = true;
    private ParticipantsUpdatedListener remoteParticipantUpdatedListener;
    private PropertyChangedListener onStateChangedListener;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getAllPermissions();
        createAgent();

        handleIncomingCall();

        Button callButton = findViewById(R.id.call_button);
        callButton.setOnClickListener(l -> startCall());
        Button hangupButton = findViewById(R.id.hang_up);
        hangupButton.setOnClickListener(l -> hangUp());
        Button startVideo = findViewById(R.id.show_preview);
        startVideo.setOnClickListener(l -> turnOnLocalVideo());
        Button stopVideo = findViewById(R.id.hide_preview);
        stopVideo.setOnClickListener(l -> turnOffLocalVideo());
        
        setVolumeControlStream(AudioManager.STREAM_VOICE_CALL);
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

    /**
     * Handle incoming calls
     */
    private void handleIncomingCall() {
        // See section on answering incoming call
    }

    /**
     * Mid-call operations
     */
    public void turnOnLocalVideo() {
        // See setion on 
    }
    public void turnOffLocalVideo() {
        
    }

    /**
     * End calls
     */
    private void hangUp() {
        // See section on ending the call
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

Azure Communication Services Calling SDK の主な機能のいくつかは、次のクラスとインターフェイスによって処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient は、Calling SDK へのメイン エントリ ポイントです。|
| CallAgent | CallAgent は、通話を開始および管理するために使用します。 |
| CommunicationTokenCredential | CommunicationTokenCredential は、CallAgent をインスタンス化するためのトークン資格情報として使用されます。|
| CommunicationIdentifier | CommunicationIdentifier は、呼び出しの一部となる可能性があるさまざまな種類の参加者として使用されます。|

## <a name="create-an-agent-from-the-user-access-token"></a>ユーザー アクセス トークンからエージェントを作成する

認証された呼び出しエージェントを作成するには、ユーザー トークンが必要です。 通常このトークンは、アプリケーション固有の認証を使用してサービスから生成されます。 ユーザー アクセス トークンの詳細については、[ユーザー アクセス トークン](../../../access-tokens.md)のガイドを参照してください。 

クイック スタートでは、`<User_Access_Token>` を Azure Communication Service リソース用に生成されたユーザー アクセス トークンに置き換えます。

```java
/**
 * Create the call agent for placing calls
 */
private void createAgent() {
    Context context = this.getApplicationContext();
    String userToken = "<User_Access_Token>";
    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(userToken);
        CallClient callClient = new CallClient();
        deviceManager = callClient.getDeviceManager(context).get();
        callAgent = callClient.createCallAgent(getApplicationContext(), credential).get(); 
    } catch (Exception ex) {
        Toast.makeText(context, "Failed to create call agent.", Toast.LENGTH_SHORT).show();
    }
}
```
## <a name="start-a-video-call-using-the-call-agent"></a>通話エージェントを使用してビデオ通話を開始する

電話の発信は通話エージェントを使用して行うことができ、必要となるのは、通話先 ID の一覧と通話オプションを指定することだけです。 

ビデオ通話を行うには、`deviceManager` の `getCameras` API を使用してローカル カメラを列挙する必要があります。 目的のカメラを選択したら、それを使用して `LocalVideoStream` インスタンスを構築し、それを call メソッドへの `localVideoStream` 配列内の項目として、`videoOptions` に渡します。 通話が接続されると、選択したカメラから他の参加者へのビデオ ストリームの送信が自動的に開始されます。

```java
private void startCall() {
    Context context = this.getApplicationContext();
    EditText calleeIdView = findViewById(R.id.callee_id);
    String calleeId = calleeIdView.getText().toString();
    ArrayList<CommunicationIdentifier> participants = new ArrayList<CommunicationIdentifier>();
    StartCallOptions options = new StartCallOptions();
    List<VideoDeviceInfo> cameras = deviceManager.getCameras();
    if(!cameras.isEmpty()) {
        VideoDeviceInfo camera = chooseCamera(cameras);
        currentVideoStream = new LocalVideoStream(camera, context);
        LocalVideoStream[] videoStreams = new LocalVideoStream[1];
        videoStreams[0] = currentVideoStream;
        VideoOptions videoOptions = new VideoOptions(videoStreams);
        options.setVideoOptions(videoOptions);
        showPreview(currentVideoStream);
    }
    participants.add(new CommunicationUserIdentifier(calleeId));

    call = callAgent.startCall(
            context,
            participants,
            options);
    
    //Subcribe to events on updates of call state and remote participants
    remoteParticipantUpdatedListener = this::handleRemoteParticipantsUpdate;
    onStateChangedListener = this::handleCallOnStateChanged;
    call.addOnRemoteParticipantsUpdatedListener(remoteParticipantUpdatedListener);
    call.addOnStateChangedListener(onStateChangedListener);
}
```

このクイックスタートでは、デバイスの前面カメラを優先的に使用します。 `chooseCamera` 関数は、カメラの列挙型を入力として受け取ります。 前面カメラが使用できない場合は、使用可能な最初のカメラを使用します。 [`Start Call`] をタップしたときに使用可能なカメラがない場合は、音声通話が開始されます。 ただし、リモート参加者がビデオで応答した場合は、リモート ビデオ ストリームを表示することができます。 

```java
VideoDeviceInfo chooseCamera(List<VideoDeviceInfo> cameras) {
    for (VideoDeviceInfo camera : cameras) {
        if (camera.getCameraFacing() == CameraFacing.FRONT) {
            return camera;
        }
    }
    return cameras.get(0);
}
```

`LocalVideoStream` インスタンスを構築したら、それを UI に表示するためのレンダラーを作成できます。 

```java
private void showPreview(LocalVideoStream stream) {
    previewRenderer = new VideoStreamRenderer(stream, this);
    LinearLayout layout = ((LinearLayout)findViewById(R.id.localvideocontainer));
    preview = previewRenderer.createView(new CreateViewOptions(ScalingMode.FIT));
    runOnUiThread(() -> {
        layout.addView(preview);
    });
}
```

## <a name="accept-an-incoming-call"></a>電話の着信を受け入れる

着信は、`callAgent` の `addOnIncomingCallListener` をサブスクライブすることによって取得できます。 

```java
private void handleIncomingCall() {
    callAgent.addOnIncomingCallListener((incomingCall) -> {
        this.incomingCall = incomingCall;
        Executors.newCachedThreadPool().submit(this::answerIncomingCall);
    });
}
```

ビデオ カメラをオンにして着信を受け入れるには、deviceManager getCameras API を使用してローカル カメラを列挙し、カメラを選択します。次に、`LocalVideoStream` インスタンスを構築して `acceptCallOptions` に渡してから、`call` オブジェクトで "accept" メソッドを呼び出します。 
```java
private void answerIncomingCall() {
    Context context = this.getApplicationContext();
    if (incomingCall == null){
        return;
    }
    AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
    List<VideoDeviceInfo> cameras = deviceManager.getCameras();
    if(!cameras.isEmpty()) {
        VideoDeviceInfo camera = chooseCamera(cameras);
        currentVideoStream = new LocalVideoStream(camera, context);
        LocalVideoStream[] videoStreams = new LocalVideoStream[1];
        videoStreams[0] = currentVideoStream;
        VideoOptions videoOptions = new VideoOptions(videoStreams);
        acceptCallOptions.setVideoOptions(videoOptions);
        showPreview(currentVideoStream);
    }
    try {
        call = incomingCall.accept(context, acceptCallOptions).get();
    } catch (InterruptedException e) {
        e.printStackTrace();
    } catch (ExecutionException e) {
        e.printStackTrace();
    }

    //Subcribe to events on updates of call state and remote participants
    remoteParticipantUpdatedListener = this::handleRemoteParticipantsUpdate;
    onStateChangedListener = this::handleCallOnStateChanged;
    call.addOnRemoteParticipantsUpdatedListener(remoteParticipantUpdatedListener);
    call.addOnStateChangedListener(onStateChangedListener);
}
```

## <a name="remote-participant-and-remote-video-streams"></a>リモート参加者とリモート ビデオ ストリーム

通話を開始したり、着信に応答したりするときは、リモート参加者を処理するために `addOnRemoteParticipantsUpdatedListener` イベントをサブスクライブする必要があります。 

```java
remoteParticipantUpdatedListener = this::handleRemoteParticipantsUpdate;
call.addOnRemoteParticipantsUpdatedListener(remoteParticipantUpdatedListener);
```
同じクラス内で定義されているイベント リスナーを使用する場合は、リスナーを変数にバインドします。 リスナーのメソッドを追加および削除するには、この変数を引数として渡します。

リスナーを引数として直接渡すと、そのリスナーへの参照が失われる可能性があります。 Java ではこれらのリスナーの新しいインスタンスが作成され、以前に作成されたものは参照されません。 それらは引き続き正しく動作しますが、参照がなくなってしまうため削除できません。

### <a name="remote-participant-and-remote-video-stream-update"></a>リモート参加者とリモート ビデオ ストリームの更新

このクイックスタートでは、1 対 1 の呼び出しを実装しているため、追加された参加者のみを処理します。 リモート参加者が削除されると、通話は終了します。 追加された参加者については、`addOnVideoStreamsUpdatedListener` をサブスクライブして、ビデオ ストリームの更新を処理します。 

```java
public void handleRemoteParticipantsUpdate(ParticipantsUpdatedEvent args) {
    LinearLayout participantVideoContainer = findViewById(R.id.remotevideocontainer);
    handleAddedParticipants(args.getAddedParticipants(),participantVideoContainer);
}

private void handleAddedParticipants(List<RemoteParticipant> participants, LinearLayout participantVideoContainer) {
    for (RemoteParticipant remoteParticipant : participants) {
        remoteParticipant.addOnVideoStreamsUpdatedListener(videoStreamsEventArgs -> videoStreamsUpdated(videoStreamsEventArgs));
    }
}

private void videoStreamsUpdated(RemoteVideoStreamsEvent videoStreamsEventArgs) {
    for(RemoteVideoStream stream : videoStreamsEventArgs.getAddedRemoteVideoStreams()) {
        StreamData data = new StreamData(stream, null, null);
        streamData.put(stream.getId(), data);
        if (renderRemoteVideo) {
            startRenderingVideo(data);
        }
    }

    for(RemoteVideoStream stream : videoStreamsEventArgs.getRemovedRemoteVideoStreams()) {
        stopRenderingVideo(stream);
    }
}
```

### <a name="render-remote-videos"></a>リモート ビデオをレンダリングする

リモート ビデオ ストリームのレンダラーを作成し、それをビューにアタッチして、リモート ビューのレンダリングを開始します。 レンダリングを停止するには、ビューを破棄します。 

```java
void startRenderingVideo(StreamData data){
    if (data.renderer != null) {
        return;
    }
    LinearLayout layout = ((LinearLayout)findViewById(R.id.remotevideocontainer));
    data.renderer = new VideoStreamRenderer(data.stream, this);
    data.renderer.addRendererListener(new RendererListener() {
        @Override
        public void onFirstFrameRendered() {
            String text = data.renderer.getSize().toString();
            Log.i("MainActivity", "Video rendering at: " + text);
        }

        @Override
        public void onRendererFailedToStart() {
            String text = "Video failed to render";
            Log.i("MainActivity", text);
        }
    });
    data.rendererView = data.renderer.createView(new CreateViewOptions(ScalingMode.FIT));
    runOnUiThread(() -> {
        layout.addView(data.rendererView);
    });
}

void stopRenderingVideo(RemoteVideoStream stream) {
    StreamData data = streamData.get(stream.getId());
    if (data == null || data.renderer == null) {
        return;
    }
    runOnUiThread(() -> {
        ((LinearLayout) findViewById(R.id.remotevideocontainer)).removeAllViews();
    });
    data.rendererView = null;
    // Dispose renderer
    data.renderer.dispose();
    data.renderer = null;
}

static class StreamData {
    RemoteVideoStream stream;
    VideoStreamRenderer renderer;
    VideoStreamRendererView rendererView;
    StreamData(RemoteVideoStream stream, VideoStreamRenderer renderer, VideoStreamRendererView rendererView) {
        this.stream = stream;
        this.renderer = renderer;
        this.rendererView = rendererView;
    }
}
```

## <a name="call-state-update"></a>通話状態の更新
このクイックスタートでは、`CallState` の変更を処理します。 通話が接続接続されたときは、リモート参加者を処理します。通話が切断されたときは、`previewRenderer` を破棄してローカル ビデオを停止します。 

```groovy
private void handleCallOnStateChanged(PropertyChangedEvent args) {
    if (call.getState() == CallState.CONNECTED) {
        LinearLayout participantVideoContainer = findViewById(R.id.remotevideocontainer);
        handleAddedParticipants(call.getRemoteParticipants(),participantVideoContainer);
    }
    if (call.getState() == CallState.DISCONNECTED) {
        if (previewRenderer != null) {
            previewRenderer.dispose();
        }
    }
}
```

## <a name="end-a-call"></a>通話を終了する
call インスタンスで `hangUp()` 関数を呼び出して通話を終了し、`previewRenderer` を破棄してローカル ビデオを停止します。 
```java
private void hangUp() {
    try {
        call.hangUp().get();
    } catch (ExecutionException | InterruptedException e) {
        e.printStackTrace();
    }
    if (previewRenderer != null) {
        previewRenderer.dispose();
    }
}
```

## <a name="run-the-code"></a>コードの実行

Android Studio のツール バーの [`Run 'App'`]\(アプリの実行\) ボタンを使用してアプリを起動できるようになりました。 

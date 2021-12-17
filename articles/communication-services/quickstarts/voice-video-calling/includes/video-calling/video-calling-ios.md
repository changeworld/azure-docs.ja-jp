---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: rifox
ms.openlocfilehash: f2006869868a94b36078734756fd5e6992cd08b2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253279"
---
Communication Services Calling SDK を使用して 1 対 1 のビデオ通話をアプリに追加することによって、Azure Communication Services の使用を開始します。 このクイックスタートでは、iOS 用の Azure Communication Services Calling SDK を使用して、ビデオ通話を開始および応答する方法について説明します。

## <a name="sample-code"></a>サンプル コード

最後までスキップしたい場合は、[GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Video%20Calling) のサンプルとしてこのクイックスタートをダウンロードできます。

## <a name="prerequisites"></a>前提条件
- アクティブなサブスクリプションが含まれる Azure アカウントを取得します。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Xcode](https://developer.apple.com/xcode/) を実行しており、有効な開発者証明書がキーチェーンにインストールされている Mac。
- アクティブな Communication Services リソースを作成します。 [Communication Services のリソースを作成する](../../../create-communication-resource.md?tabs=windows&pivots=platform-azp)。
- 通話クライアントのインスタンスを作成するためのユーザー アクセス トークンを作成します。 [ユーザー アクセス トークンを作成および管理する方法に関するページを参照してください](../../../access-tokens.md?pivots=programming-language-csharp)。

## <a name="setting-up"></a>設定
### <a name="creating-the-xcode-project"></a>Xcode プロジェクトを作成する
Xcode で、新しい iOS プロジェクトを作成し、 [単一ビュー アプリ] テンプレートを選択します。 このチュートリアルでは [SwiftUI フレームワーク](https://developer.apple.com/xcode/swiftui/)を使用します。そのため、[Language]\(言語\) を [Swift] に設定し、[User Interface]\(ユーザー インターフェイス\) を [SwiftUI] に設定する必要があります。 このクイック スタートでは、テストは作成しません。 [Include Tests]\(テストを含める\) チェック ボックスはオフにしてかまいません。

:::image type="content" source="../../media/ios/xcode-new-ios-project.png" alt-text="Xcode 内で新しいプロジェクトを作成するウィンドウのスクリーンショット。":::

### <a name="installing-cocoapods"></a>CocoaPods のインストール
このガイドを使用して、お使いの Mac に [CocoaPods をインストール](https://guides.cocoapods.org/using/getting-started.html)してください。 

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>CocoaPods でパッケージと依存関係をインストールする
1. アプリケーションのポッドファイルを作成するために、ターミナルを開いてプロジェクト フォルダーに移動し、pod init を実行します。

2. 次のコードをポッドファイルに追加して保存します。

```
platform :ios, '13.0'
use_frameworks!

target 'VideoCallingQuickstart' do
  pod 'AzureCommunicationCalling', '~> 1.0.0'
end
```

3. pod install を実行します。

4. Xcode で .xcworkspace を開きます。


### <a name="request-access-to-the-microphone-and-camera"></a>マイクとカメラへのアクセスを要求する
デバイスのマイクとカメラにアクセスするには、アプリの情報プロパティ リストを `NSMicrophoneUsageDescription` と `NSCameraUsageDescription` で更新する必要があります。 関連付けられた値を文字列に設定します。これは、ユーザーからのアクセスの要求を求めるためにシステムによって使用されるダイアログに含められます。

プロジェクト ツリーの `Info.plist` のエントリを右クリックし、[Open As]\(形式を指定して開く\) > [Source Code]\(ソース コード \) の順に選択します。 最上位の `<dict>` セクションに以下の行を追加してから、ファイルを保存します。

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
<key>NSCameraUsageDescription</key>
<string>Need camera access for video calling</string>
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する
プロジェクトの `ContentView.swift` ファイルを開き、そのファイルの先頭に import 宣言を追加して、`AzureCommunicationCalling` ライブラリと `AVFoundation` をインポートします。 AVFoundation は、コードからオーディオ アクセス許可を取得するために使用されます。

```Swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>オブジェクト モデル
Azure Communication Services Calling SDK for iOS の主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| Name                         | 説明                                                                                                                                                                        |
| :--------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                   | CallClient は、Calling SDK へのメイン エントリ ポイントです。                                                                                                                         |
| CallAgent                    | CallAgent は、通話を開始および管理するために使用します。                                                                                                                                   |
| CommunicationTokenCredential | CommunicationTokenCredential は、CallAgent をインスタンス化するためのトークン資格情報として使用されます。                                                                                     |
| CommunicationIdentifier      | CommunicationIdentifier はユーザーの ID を表すために使用され、次のいずれかになります: CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication。 |

## <a name="create-the-call-agent"></a>通話エージェントを作成する
ContentView 構造体の実装を、ユーザーが通話を開始して終了できるようにする、いくつかの単純な UI コントロールに置き換えます。 このクイック スタートでは、これらのコントロールにビジネス ロジックをアタッチします。

```Swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?
    @State var deviceManager: DeviceManager?
    @State var localVideoStream:[LocalVideoStream]?
    @State var incomingCall: IncomingCall?
    @State var sendingVideo:Bool = false
    @State var errorMessage:String = "Unknown"

    @State var remoteVideoStreamData:[Int32:RemoteVideoStreamData] = [:]
    @State var previewRenderer:VideoStreamRenderer? = nil
    @State var previewView:RendererView? = nil
    @State var remoteRenderer:VideoStreamRenderer? = nil
    @State var remoteViews:[RendererView] = []
    @State var remoteParticipant: RemoteParticipant?
    @State var remoteVideoSize:String = "Unknown"
    @State var isIncomingCall:Bool = false
    
    @State var callObserver:CallObserver?
    @State var remoteParticipantObserver:RemoteParticipantObserver?

    var body: some View {
        NavigationView {
            ZStack{
                Form {
                    Section {
                        TextField("Who would you like to call?", text: $callee)
                        Button(action: startCall) {
                            Text("Start Call")
                        }.disabled(callAgent == nil)
                        Button(action: endCall) {
                            Text("End Call")
                        }.disabled(call == nil)
                        Button(action: toggleLocalVideo) {
                            HStack {
                                Text(sendingVideo ? "Turn Off Video" : "Turn On Video")
                            }
                        }
                    }
                }
                // Show incoming call banner
                if (isIncomingCall) {
                    HStack() {
                        VStack {
                            Text("Incoming call")
                                .padding(10)
                                .frame(maxWidth: .infinity, alignment: .topLeading)
                        }
                        Button(action: answerIncomingCall) {
                            HStack {
                                Text("Answer")
                            }
                            .frame(width:80)
                            .padding(.vertical, 10)
                            .background(Color(.green))
                        }
                        Button(action: declineIncomingCall) {
                            HStack {
                                Text("Decline")
                            }
                            .frame(width:80)
                            .padding(.vertical, 10)
                            .background(Color(.red))
                        }
                    }
                    .frame(maxWidth: .infinity, alignment: .topLeading)
                    .padding(10)
                    .background(Color.gray)
                }
                ZStack{
                    VStack{
                        ForEach(remoteViews, id:\.self) { renderer in
                            ZStack{
                                VStack{
                                    RemoteVideoView(view: renderer)
                                        .frame(width: .infinity, height: .infinity)
                                        .background(Color(.lightGray))
                                }
                            }
                            Button(action: endCall) {
                                Text("End Call")
                            }.disabled(call == nil)
                            Button(action: toggleLocalVideo) {
                                HStack {
                                    Text(sendingVideo ? "Turn Off Video" : "Turn On Video")
                                }
                            }
                        }
                        
                    }.frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .topLeading)
                    VStack{
                        if(sendingVideo)
                        {
                            VStack{
                                PreviewVideoStream(view: previewView!)
                                    .frame(width: 135, height: 240)
                                    .background(Color(.lightGray))
                            }
                        }
                    }.frame(maxWidth:.infinity, maxHeight:.infinity,alignment: .bottomTrailing)
                }
            }
     .navigationBarTitle("Video Calling Quickstart")
        }.onAppear{
            // Authenticate the client
            
            // Initialize the CallAgent and access Device Manager
            
            // Ask for permissions
        }
    }
}

//Functions and Observers

struct PreviewVideoStream: UIViewRepresentable {
    let view:RendererView
    func makeUIView(context: Context) -> UIView {
        return view
    }
    func updateUIView(_ uiView: UIView, context: Context) {}
}

struct RemoteVideoView: UIViewRepresentable {
    let view:RendererView
    func makeUIView(context: Context) -> UIView {
        return view
    }
    func updateUIView(_ uiView: UIView, context: Context) {}
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

### <a name="authenticate-the-client"></a>クライアントを認証する
CallAgent インスタンスを初期化するには、電話をかけたり受けたりできるようにするユーザー アクセス トークンが必要です。 まだトークンを入手していない場合は、[ユーザー アクセス トークン](../../../access-tokens.md?pivots=programming-language-csharp)に関するドキュメントを参照してください。

トークンを入手したら、`ContentView.swift` の `onAppear` コールバックに次のコードを追加します。 `<USER ACCESS TOKEN>` を、リソース用の有効なユーザー アクセス トークン**で置き換える必要があります。

```Swift
var userCredential: CommunicationTokenCredential?
do {
    userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}
```

### <a name="initialize-the-callagent-and-access-device-manager"></a>CallAgent を初期化し、デバイス マネージャーにアクセスする
CallClient から CallAgent インスタンスを作成するには、初期化されると CallAgent オブジェクトを非同期に返す `callClient.createCallAgent` メソッドを使用します。 デバイス マネージャーを使用すると、オーディオおよびビデオ ストリームを送信するために通話内で使用できるローカル デバイスを列挙できます。 また、マイクやカメラにアクセスするための許可をユーザーに要求することもできます。 

```Swift
self.callClient = CallClient()
self.callClient?.createCallAgent(userCredential: userCredential!) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
        return
    }

    else {
        self.callAgent = agent
        print("Call agent successfully created.")
        self.callAgent!.delegate = incomingCallHandler
        self.callClient?.getDeviceManager { (deviceManager, error) in
            if (error == nil) {
                print("Got device manager instance")
                self.deviceManager = deviceManager
            } else {
                print("Failed to get device manager instance")
            }
        }
    }
}
```

### <a name="ask-for-permissions"></a>アクセス許可を求める
オーディオとビデオのアクセス許可を求めるには、次のコードを `onAppear` コールバックに追加する必要があります。 

```Swift
AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
    if granted {
        AVCaptureDevice.requestAccess(for: .video) { (videoGranted) in
            /* NO OPERATION */
        }
    }
}
```

## <a name="display-local-video"></a>ローカル ビデオを表示する
通話を開始する前に、ビデオに関連する設定を管理できます。 このクイックスタートでは、通話の前または通話中にローカル ビデオを切り替える方法の実装について説明します。 

最初に、`deviceManager` を使用してローカル カメラにアクセスする必要があります。 目的のカメラを選択したら、`LocalVideoStream` を構築します。次に、`VideoStreamRenderer` を作成して、それを `previewView` にアタッチします。 通話中は、`startVideo` または `stopVideo` を使用して、リモート参加者への `LocalVideoStream` の送信を開始または停止できます。 この関数は、着信の処理でも機能します。 

```Swift
func toggleLocalVideo() {
    // toggling video before call starts
    if (call == nil)
    {
        if(!sendingVideo)
        {
            self.callClient = CallClient()
            self.callClient?.getDeviceManager { (deviceManager, error) in
                if (error == nil) {
                    print("Got device manager instance")
                    self.deviceManager = deviceManager
                } else {
                    print("Failed to get device manager instance")
                }
            }
            guard let deviceManager = deviceManager else {
                return
            }
            let camera = deviceManager.cameras.first
            let scalingMode = ScalingMode.fit
            if (self.localVideoStream == nil) {
                self.localVideoStream = [LocalVideoStream]()
            }
            localVideoStream!.append(LocalVideoStream(camera: camera!))
            previewRenderer = try! VideoStreamRenderer(localVideoStream: localVideoStream!.first!)
            previewView = try! previewRenderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
            self.sendingVideo = true
        }
        else{
            self.sendingVideo = false
            self.previewView = nil
            self.previewRenderer!.dispose()
            self.previewRenderer = nil
        }
    }
    // toggle local video during the call
    else{
        if (sendingVideo) {
            call!.stopVideo(stream: localVideoStream!.first!) { (error) in
                if (error != nil) {
                    print("cannot stop video")
                }
                else {
                    self.sendingVideo = false
                    self.previewView = nil
                    self.previewRenderer!.dispose()
                    self.previewRenderer = nil
                }
            }
        }
        else {
            guard let deviceManager = deviceManager else {
                return
            }
            let camera = deviceManager.cameras.first
            let scalingMode = ScalingMode.fit
            if (self.localVideoStream == nil) {
                self.localVideoStream = [LocalVideoStream]()
            }
            localVideoStream!.append(LocalVideoStream(camera: camera!))
            previewRenderer = try! VideoStreamRenderer(localVideoStream: localVideoStream!.first!)
            previewView = try! previewRenderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
            call!.startVideo(stream:(localVideoStream?.first)!) { (error) in
                if (error != nil) {
                    print("cannot start video")
                }
                else {
                    self.sendingVideo = true
                }
            }
        }
    }
}
```

## <a name="place-an-outgoing-call"></a>発信通話を行う
`startCall` メソッドは、 [Start Call]\(通話の開始\) ボタンがタップされたときに実行されるアクションとして設定されます。 このクイックスタートでは、発信通話は既定で音声のみです。 ビデオを使用して通話を開始するには、`VideoOptions` に `LocalVideoStream` を設定し、それを `startCallOptions` に渡して、通話の初期オプションを設定する必要があります。

```Swift
func startCall() {
        let startCallOptions = StartCallOptions()
        if(sendingVideo)
        {
            if (self.localVideoStream == nil) {
                self.localVideoStream = [LocalVideoStream]()
            }
            let videoOptions = VideoOptions(localVideoStreams: localVideoStream!)
            startCallOptions.videoOptions = videoOptions
        }
        let callees:[CommunicationIdentifier] = [CommunicationUserIdentifier(self.callee)]
        self.callAgent?.startCall(participants: callees, options: startCallOptions) { (call, error) in
            setCallAndObersever(call: call, error: error)
        }
    }
```

`CallObserver` と `RemotePariticipantObserver` は、通話中のイベントとリモート参加者を管理するために使用されます。 `setCallAndOberserver` 関数でオブザーバーを設定します。 

```Swift
func setCallAndObersever(call:Call!, error:Error?) {
    if (error == nil) {
        self.call = call
        self.callObserver = CallObserver(self)
        self.call!.delegate = self.callObserver
        self.remoteParticipantObserver = RemoteParticipantObserver(self)
    } else {
        print("Failed to get call object")
    }
}
```

## <a name="answer-an-incoming-call"></a>着信に応答する
着信に応答するには、`IncomingCallHandler` を実装して、着信に応答または拒否するための着信バナーを表示します。 次の実装を `IncomingCallHandler.swift` に挿入します。 

```Swift
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate {
    public var contentView: ContentView?
    private var incomingCall: IncomingCall?

    private static var instance: IncomingCallHandler?
    static func getOrCreateInstance() -> IncomingCallHandler {
        if let c = instance {
            return c
        }
        instance = IncomingCallHandler()
        return instance!
    }

    private override init() {}
    
    public func callAgent(_ callAgent: CallAgent, didRecieveIncomingCall incomingCall: IncomingCall) {
        self.incomingCall = incomingCall
        self.incomingCall?.delegate = self
        contentView?.showIncomingCallBanner(self.incomingCall!)
    }
    
    public func callAgent(_ callAgent: CallAgent, didUpdateCalls args: CallsUpdatedEventArgs) {
        if let removedCall = args.removedCalls.first {
            contentView?.callRemoved(removedCall)
            self.incomingCall = nil
        }
    }
}
```

`ContentView.swift` の `onAppear` コールバックに次のコードを追加して、`IncomingCallHandler` のインスタンスを作成する必要があります。

```Swift
let incomingCallHandler = IncomingCallHandler.getOrCreateInstance()
incomingCallHandler.contentView = self
```

CallAgent が正常に作成された後、CallAgent にデリゲートを設定します。

```Swift
self.callAgent!.delegate = incomingCallHandler
```

着信があると、`IncomingCallHandler` によって `showIncomingCallBanner` 関数が呼び出され、`answer` ボタンと `decline` ボタンが表示されます。 

```Swift
func showIncomingCallBanner(_ incomingCall: IncomingCall?) {
    isIncomingCall = true
    self.incomingCall = incomingCall
}
```

`answer` と `decline` に関連付けられるアクションは、次のように実装されます。 ビデオを使用して通話に応答するには、ローカル ビデオをオンにして、`AcceptCallOptions` のオプションに `localVideoStream` を設定する必要があります。 

```Swift
func answerIncomingCall() {
    isIncomingCall = false
    let options = AcceptCallOptions()
    if (self.incomingCall != nil) {
        guard let deviceManager = deviceManager else {
            return
        }        
        if (self.localVideoStream == nil) {
            self.localVideoStream = [LocalVideoStream]()
        }
        if(sendingVideo)
        {
            let camera = deviceManager.cameras.first
            localVideoStream!.append(LocalVideoStream(camera: camera!))
            let videoOptions = VideoOptions(localVideoStreams: localVideoStream!)
            options.videoOptions = videoOptions
        }
        self.incomingCall!.accept(options: options) { (call, error) in
            setCallAndObersever(call: call, error: error)
        }
    }
}

func declineIncomingCall(){
    self.incomingCall!.reject { (error) in }
    isIncomingCall = false
}
```

## <a name="remote-participant-video-streams"></a>リモート参加者のビデオ ストリーム
リモート参加者のビデオ ストリームのレンダリングを処理するための `RemoteVideoStreamData` クラスを作成できます。 

```Swift
public class RemoteVideoStreamData : NSObject, RendererDelegate {
    public func videoStreamRenderer(didFailToStart renderer: VideoStreamRenderer) {
        owner.errorMessage = "Renderer failed to start"
    }
    
    private var owner:ContentView
    let stream:RemoteVideoStream
    var renderer:VideoStreamRenderer? {
        didSet {
            if renderer != nil {
                renderer!.delegate = self
            }
        }
    }
    
    var views:[RendererView] = []
    init(view:ContentView, stream:RemoteVideoStream) {
        owner = view
        self.stream = stream
    }
    
    public func videoStreamRenderer(didRenderFirstFrame renderer: VideoStreamRenderer) {
        let size:StreamSize = renderer.size
        owner.remoteVideoSize = String(size.width) + " X " + String(size.height)
    }
}
```

## <a name="subscribe-to-events"></a>イベントをサブスクライブする
通話中に値が変化したときに通知されるイベントのコレクションをサブスクライブするには、`CallObserver` クラスを実装できます。 

```Swift
public class CallObserver: NSObject, CallDelegate, IncomingCallDelegate {
    private var owner: ContentView
    init(_ view:ContentView) {
            owner = view
    }
        
    public func call(_ call: Call, didChangeState args: PropertyChangedEventArgs) {
        if(call.state == CallState.connected) {
            initialCallParticipant()
        }
    }

    // render remote video streams when remote participant changes
    public func call(_ call: Call, didUpdateRemoteParticipant args: ParticipantsUpdatedEventArgs) {
        for participant in args.addedParticipants {
            participant.delegate = owner.remoteParticipantObserver
            for stream in participant.videoStreams {
                if !owner.remoteVideoStreamData.isEmpty {
                    return
                }
                let data:RemoteVideoStreamData = RemoteVideoStreamData(view: owner, stream: stream)
                let scalingMode = ScalingMode.fit
                data.renderer = try! VideoStreamRenderer(remoteVideoStream: stream)
                let view:RendererView = try! data.renderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
                data.views.append(view)
                self.owner.remoteViews.append(view)
                owner.remoteVideoStreamData[stream.id] = data
            }
            owner.remoteParticipant = participant
        }
    }

    // Handle remote video streams when the call is connected
    public func initialCallParticipant() {
        for participant in owner.call!.remoteParticipants {
            participant.delegate = owner.remoteParticipantObserver
            for stream in participant.videoStreams {
                renderRemoteStream(stream)
            }
            owner.remoteParticipant = participant
        }
    }
    
    //create render for RemoteVideoStream and attach it to view
    public func renderRemoteStream(_ stream: RemoteVideoStream!) {
        if !owner.remoteVideoStreamData.isEmpty {
            return
        }
        let data:RemoteVideoStreamData = RemoteVideoStreamData(view: owner, stream: stream)
        let scalingMode = ScalingMode.fit
        data.renderer = try! VideoStreamRenderer(remoteVideoStream: stream)
        let view:RendererView = try! data.renderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
        self.owner.remoteViews.append(view)
        owner.remoteVideoStreamData[stream.id] = data
    }
}
```

## <a name="remote-participant-management"></a>リモート参加者の管理
すべてのリモート参加者は `RemoteParticipant` 型で表され、通話インスタンスの `remoteParticipants` コレクションを通して使用できます。 リモート参加者のリモート ビデオ ストリームの更新をサブスクライブするには、`RemoteParticipantObserver` クラスを実装できます。 

```Swift
public class RemoteParticipantObserver : NSObject, RemoteParticipantDelegate {
    private var owner:ContentView
    init(_ view:ContentView) {
        owner = view
    }

    public func renderRemoteStream(_ stream: RemoteVideoStream!) {
        let data:RemoteVideoStreamData = RemoteVideoStreamData(view: owner, stream: stream)
        let scalingMode = ScalingMode.fit
        data.renderer = try! VideoStreamRenderer(remoteVideoStream: stream)
        let view:RendererView = try! data.renderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
        self.owner.remoteViews.append(view)
        owner.remoteVideoStreamData[stream.id] = data
    }
    
    // render RemoteVideoStream when remote participant turns on the video, dispose the renderer when remote video is off
    public func remoteParticipant(_ remoteParticipant: RemoteParticipant, didUpdateVideoStreams args: RemoteVideoStreamsEventArgs) {
        for stream in args.addedRemoteVideoStreams {
            renderRemoteStream(stream)
        }
        for stream in args.removedRemoteVideoStreams {
            for data in owner.remoteVideoStreamData.values {
                data.renderer?.dispose()
            }
            owner.remoteViews.removeAll()
        }
    }
}
```

## <a name="run-the-code"></a>コードの実行
iOS シミュレーターでアプリをビルドして実行するには、[Product]\(製品\) > [Run]\(実行\) の順に選択するか、(⌘-R) キーボード ショートカットを使用します。

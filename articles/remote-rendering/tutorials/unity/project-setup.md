---
title: Unity プロジェクトをゼロから設定する
description: Azure Remote Rendering 用に空の Unity プロジェクトを構成する方法について説明します。
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: 33801316e4c0446865169560bb42f98052acba70
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678653"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>チュートリアル:Unity プロジェクトをゼロから設定する

このチュートリアルでは、次の事項について説明します。

> [!div class="checklist"]
>
> * ARR 用の Unity プロジェクトをゼロから構成する。
> * レンダリング セッションを作成、停止する。
> * 既存のセッションを再利用する。
> * セッションへの接続とセッションからの切断を行う。
> * レンダリング セッションにモデルを読み込む。
> * 接続の統計情報を表示する。

## <a name="prerequisites"></a>前提条件

このチュートリアルには、次のものが必要です。

* アカウント情報 (アカウント ID、アカウント キー、サブスクリプション ID)。 アカウントをお持ちでない場合は、[アカウントを作成](../../how-tos/create-an-account.md)してください。
* Windows SDK 10.0.18362.0 [(ダウンロード)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* 最新バージョンの Visual Studio 2019 [(ダウンロード)](https://visualstudio.microsoft.com/vs/older-downloads/)
* Git ([ダウンロード](https://git-scm.com/downloads))
* Unity 2019.3.1 [(ダウンロード)](https://unity3d.com/get-unity/download)
  * 次のモジュールを Unity にインストールします。
    * **UWP** - ユニバーサル Windows プラットフォーム Build Support
    * **IL2CPP** - Windows Build Support (IL2CPP)

> [!TIP]
> [ARR サンプル リポジトリ](https://github.com/Azure/azure-remote-rendering)には、すべてのチュートリアルを対象に準備された Unity プロジェクトが格納されています。 それらのプロジェクトをリファレンスとして使用できます。

## <a name="create-a-new-unity-project"></a>新しい Unity プロジェクトを作成する

Unity Hub から新しいプロジェクトを作成します。
プロジェクトの作成先として、この例では `RemoteRendering` というフォルダーを想定しています。

![新しいプロジェクト ウィンドウ](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>プロジェクトのマニフェストを構成する

Unity プロジェクト フォルダーにある `Packages/manifest.json` ファイルに変更を加える必要があります。 このファイルをテキスト エディターで開き、次の行を追加します。

```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.11",
    "com.unity.render-pipelines.universal": "7.2.1",
    ...existing dependencies...
  }
}
```

ユニバーサル レンダー パイプライン パッケージは省略可能ですが、パフォーマンス上の理由から推奨されます。
マニフェストに変更を加えて保存すると、Unity によって自動的に最新の情報に更新されます。 *[プロジェクト]* ウィンドウにパッケージが読み込まれたことを確認します。

![パッケージのインポートを確認する](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>最新バージョンのパッケージがあることを確認する

プロジェクトで最新バージョンの remote-rendering パッケージが使用されていることを次の手順で確認します。
1. [Project]\(プロジェクト\) ウィンドウでパッケージを選択し、パッケージ アイコンをクリックします。![パッケージ アイコンを選択する](media/package-icons.png)
1. [Inspector]\(インスペクター\) で [View in Package Manager]\(パッケージ マネージャーで表示\) をクリックします。![パッケージ インスペクター](media/package-properties.png)
1. Remote Rendering パッケージの [Package Manager]\(パッケージ マネージャー\) ページで、更新ボタンが利用できるかどうかを確認します。 利用可能である場合は、それをクリックして、提供されている最新のバージョンにパッケージを更新します。![パッケージ マネージャーにおける ARR パッケージ](media/package-manager.png)
1. パッケージを更新しようとすると、コンソールにエラーが表示されることがあります。 その場合は、プロジェクトを閉じてから再度開いてみてください。

## <a name="configure-the-camera"></a>カメラを構成する

**[Main Camera]\(メイン カメラ\)** ノードを選択します。

1. その *[Transform]\(変換\)* をリセットします。

    ![カメラの変換をリセットする](media/camera-reset-transform.png)

1. **[Clear flags]\(フラグをクリア\)** を *[Solid Color]\(単色\)* に設定します。

1. **[Background]\(背景\)** を *[Black]\(黒\)* に設定します。

1. **[Clipping Planes]\(クリップ面\)** を *[Near]\(近\) = 0.3* および *[Far]\(遠\) = 20* に設定します。 つまり、30 cm より近いか 20 メートルより遠いジオメトリが、レンダリングによってクリップされます。

    ![Unity カメラのプロパティ](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>プロジェクトの設定を調整する

1. *[Edit]\(編集\) > [Project Settings]\(プロジェクトの設定\)* を開きます。
1. 左側の一覧から [Quality]\(品質\) を選択します。
1. **[Default Quality Level]\(既定の品質レベル\)** を *[Low]\(低\)* に変更します。

    ![プロジェクトの品質設定を変更する](media/settings-quality.png)

1. 左側の **[Graphics]\(グラフィックス\)** を選択します。
1. **[Scriptable Rendering Pipeline]\(スクリプト可能なレンダリング パイプライン\)** 設定を *[HybridRenderingPipeline]* に変更します。 ユニバーサル レンダー パイプラインを使用していない場合、この手順はスキップしてください。

    ![プロジェクトのグラフィックス設定を変更する](media/settings-graphics-lwrp.png)パッケージに用意されているパイプラインの種類が UI に一覧表示されない場合があります。その場合は、*HybridRenderingPipeline* 資産をフィールド上に手動でドラッグする必要があります。![プロジェクトのグラフィックス設定を変更する](media/hybrid-rendering-pipeline.png)
1. 左側にある **[Player]\(プレーヤー\)** を選択します。
1. **[Universal Windows Platform settings]\(ユニバーサル Windows プラットフォームの設定\)** タブを選択します。
1. Windows Mixed Reality をサポートするように **[XR Settings]\(XR 設定\)** を変更します。![プレーヤー設定](media/xr-player-settings.png)
1. 上のスクリーンショットのように設定を選択します。
    1. **[Virtual Reality Supported]\(サポートされている仮想現実\)** を有効にします。
    1. **[Depth Format]\(色深度形式\)** を *[16-Bit Depth]\(16 ビット深度\)* に設定します。
    1. **[Depth Buffer Sharing]\(深度バッファーの共有\)** を有効にします。
    1. **[Stereo Rendering Mode]\(ステレオ レンダリング モード\)** を *[Single Pass Instanced]\(シングル パス インスタンシング\)* に設定します。

1. 同じウィンドウの *[XR Settings]\(XR 設定\)* の上にある **[Publishing Settings]\(公開設定\)** を展開します。
1. **[Capabilities]\(機能\)** まで下へスクロールし、次の項目を選択します。
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * 開発用途では省略可: **PrivateNetworkClientServer**

      このオプションは、Unity リモート デバッガーをデバイスに接続する場合に必要となります。

1. **[Supported Device Families]\(サポートされるデバイス ファミリ\)** で **[Holographic]\(ホログラフィック\)** および **[Desktop]\(デスクトップ\)** を有効にします。

1. Mixed Reality Toolkit を使用する場合に推奨される設定と機能の詳細については、[MRTK のドキュメント](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview)を参照してください。

## <a name="validate-project-setup"></a>プロジェクトの設定を確認する

次の手順を実行して、プロジェクトの設定が正しいことを確認します。

1. Unity エディター ツール バーの [RemoteRendering] メニューから [ValidateProject] エントリを選択します。
1. [ValidateProject] ウィンドウを使用して、プロジェクトの設定をチェックし、必要に応じて修正します。

    ![Unity エディターのプロジェクトの確認](media/arr-unity-validation.png)

## <a name="create-a-script-to-initialize-azure-remote-rendering"></a>Azure Remote Rendering を初期化するスクリプトを作成する

[新しいスクリプト](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html)を作成し、**RemoteRendering** という名前を付けます。 スクリプト ファイルを開いて、その内容全体を次のコードに置き換えます。

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

#if UNITY_WSA
    using UnityEngine.XR.WSA;
#endif

// ask Unity to automatically append an ARRServiceUnity component when a RemoteRendering script is attached
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRendering : MonoBehaviour
{
    // fill out the variables below with your account details

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // See the documentation for the list of available regions.
    public string AccountDomain = "westus2.mixedreality.azure.com";
    public string AccountId = "<enter your account id here>";
    public string AccountKey = "<enter your account key here>";

    public uint MaxLeaseTimeHours = 0;
    public uint MaxLeaseTimeMinutes = 10;
    public RenderingSessionVmSize VMSize = RenderingSessionVmSize.Standard;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        // initialize Azure Remote Rendering for use in Unity:
        // it needs to know which camera is used for rendering the scene
        RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
        RemoteManagerUnity.InitializeManager(clientInit);

        // lookup the ARRServiceUnity component and subscribe to session events
        arrService = GetComponent<ARRServiceUnity>();
        arrService.OnSessionErrorEncountered += ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged += ARRService_OnSessionStatusChanged;
    }

#if !UNITY_EDITOR
    private void Start()
    {
        StartCoroutine(AutoStartSessionAsync());
    }
#endif

    private void OnDestroy()
    {
        arrService.OnSessionErrorEncountered -= ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged -= ARRService_OnSessionStatusChanged;

        RemoteManagerStatic.ShutdownRemoteRendering();
    }

    private void CreateFrontend()
    {
        if (arrService.Frontend != null)
        {
            // early out if the front-end has been created before
            return;
        }

        // initialize the ARR service with our account details
        AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
        accountInfo.AccountKey = AccountKey;
        accountInfo.AccountId = AccountId;
        accountInfo.AccountDomain = AccountDomain;

        arrService.Initialize(accountInfo);
    }

    public void CreateSession()
    {
        CreateFrontend();

        // StartSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.StartSession(new RenderingSessionCreationParams(VMSize, MaxLeaseTimeHours, MaxLeaseTimeMinutes));
    }

    public void StopSession()
    {
        arrService.StopSession();
    }

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);
    }

    private void ARRService_OnSessionErrorEncountered(ARRServiceUnity caller, SessionGeneralContext context)
    {
        Debug.LogError($"Session error encountered. Context: {context.ErrorMessage}. Request Cv: {context.RequestCorrelationVector}. Response Cv: {context.ResponseCorrelationVector}");
    }

    private async void LogSessionStatus(AzureSession session)
    {
        if (session != null)
        {
            var sessionProperties = await session.GetPropertiesAsync().AsTask();
            LogSessionStatus(sessionProperties);
        }
        else
        {
            var sessionProperties = arrService.LastProperties;
            Debug.Log($"Session ended: Id={sessionProperties.Id}");
        }
    }

    private void LogSessionStatus(RenderingSessionProperties sessionProperties)
    {
        Debug.Log($"Session '{sessionProperties.Id}' is {sessionProperties.Status}. Size={sessionProperties.Size}" +
            (!string.IsNullOrEmpty(sessionProperties.Hostname) ? $", Hostname='{sessionProperties.Hostname}'" : "") +
            (!string.IsNullOrEmpty(sessionProperties.Message) ? $", Message='{sessionProperties.Message}'" : ""));
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
}
```

このスクリプトは、Azure Remote Rendering を初期化し、レンダリングに使用するカメラ オブジェクトを指定します。また、 *[Play Mode]\(再生モード\)* がアクティブにされたときに、 **[Create Session]\(セッションの作成\)** ボタンをビューポートに配置します。

> [!CAUTION]
> Unity の再生モードがアクティブになっている状態でスクリプトを変更、保存すると、Unity がフリーズし、タスク マネージャーでのシャットダウンを余儀なくされることがあります。 そのため *RemoteRendering* スクリプトを編集する前に必ず、再生モードを停止しておいてください。

## <a name="test-azure-remote-rendering-session-creation"></a>Azure Remote Rendering セッションの作成をテストする

シーンに新しい GameObject を作成し、そこに *RemoteRendering* コンポーネントを追加します。 ご利用の Remote Rendering アカウントの適切な "*アカウント ドメイン*"、"*アカウント ID*"、"*アカウント キー*" を入力してください。

![Remote Rendering コンポーネントのプロパティ](media/remote-rendering-component.png)

エディターでアプリケーションを起動します ( **[Play]\(再生\) を押す**か、Ctrl + P キーを押します)。 ビューポートに **[Create Session]\(セッションの作成\)** ボタンが表示されます。 それをクリックすると、初めての ARR セッションが開始されます。

![初めてのセッションを作成する](media/test-create.png)

失敗した場合は、RemoteRendering コンポーネントのプロパティに入力したアカウントの詳細が正しいことを確認してください。 それ以外の場合、割り当てられたセッション ID を示すメッセージがコンソール ウィンドウに表示され、セッションが *[Starting]\(開始中\)* の状態であることがわかります。

![セッションが開始中であることを示す出力](media/create-session-output.png)

このとき、Azure によってサーバーが自動的にプロビジョニングされ、リモート レンダリング仮想マシンが起動されています。 通常、所要時間は **3 分から 5 分**です。 VM の準備が完了すると、Unity スクリプトの `OnSessionStatusChanged` コールバックが実行され、新しいセッション状態が出力されます。

![セッションの準備完了を示す出力](media/create-session-output-2.png)

これで終了です。 しばらくは何も行いません。 料金が請求されないよう、不要になったセッションは必ず停止してください。 このサンプルでは、 **[Stop Session]\(セッションの停止\)** ボタンをクリックするか、Unity シミュレーションを停止すればセッションを停止できます。 *ARRServiceUnity* コンポーネントの **Auto-Stop Session** プロパティ (既定値はオン) により、セッションは自動的に停止します。 クラッシュや接続の問題ですべてが失敗した場合は、*MaxLeaseTime* が経過するまでセッションの実行が継続された後、サーバーによってシャットダウンされます。

> [!NOTE]
> セッションの停止は直ちに実行され、取り消すことはできません。 定義した後は、同じ起動オーバーヘッドで新しいセッションを作成する必要があります。

## <a name="reusing-sessions"></a>セッションを再利用する

残念ながら、新しいセッションの作成は時間のかかる操作です。 そのため、セッションの作成は最小限にし、できるだけそれを再利用するようにしてください。

*RemoteRendering* スクリプトに次のコードを挿入します。重複する古いバージョンの関数は削除してください。

```csharp
    public string SessionId = null;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
        }
    }

    public async void QueryActiveSessions()
    {
        CreateFrontend();

        var allSessionsProperties = await arrService.Frontend.GetCurrentRenderingSessionsAsync().AsTask();

        Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");

        foreach (var sessionProperties in allSessionsProperties)
        {
            if (string.IsNullOrEmpty(SessionId))
            {
                Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");
                SessionId = sessionProperties.Id;
            }

            LogSessionStatus(sessionProperties);
        }
    }

    public void UseExistingSession()
    {
        CreateFrontend();

        // OpenSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.OpenSession(SessionId);
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
```

> [!CAUTION]
> このコードを実行する前に、RemoteRendering コンポーネントの **Auto-Stop Session** オプションを非アクティブ化してください。 そうしないと、シミュレーションを停止するたびに、作成したセッションがすべて自動的に停止され、セッションを再利用しようとするとエラーが発生します。

*[Play]\(再生\)* を押すと、 **[Create Session]\(セッションの作成\)** 、 **[Query Active Sessions]\(アクティブ セッションの照会\)** 、 **[Use Existing Session]\(既存のセッションの使用\)** の 3 つのボタンがビューポートに表示されます。 1 つ目のボタンは、常に新しいセッションを作成します。 2 つ目のボタンは、存在する "*アクティブ*" なセッションを照会します。 使用するセッション ID を手動で指定しなかった場合、このアクションによって、今後使用するためのセッション ID が自動的に選択されます。 3 つ目のボタンは、既存のセッションへの接続を試みます。 接続先は、*Session Id* コンポーネント プロパティを使用して手動で指定したセッションか、または *[Query Active Sessions]\(アクティブ セッションの照会\)* で検出されたセッションになります。

**AutoStartSessionAsync** 関数は、エディターの外部でボタンの押下をシミュレートする目的で使用されます。

> [!TIP]
> 停止されたセッション、有効期限が切れたセッション、またはエラー状態のセッションを開くことができます。 レンダリング目的には使用できませんが、非アクティブ状態のセッションを開いた後で、その詳細を照会することは可能です。 上のコードは、`ARRService_OnSessionStarted` でセッションの状態をチェックし、セッションが使用できない状態になると自動的に停止します。

この機能を使用すれば、セッションを作成したり再利用したりできるので、開発ワークフローが大幅に改善されるはずです。

サーバーの立ち上げに要する時間の関係上、セッションの作成はクライアント アプリケーションの外部でトリガーするのが一般的でしょう。

## <a name="connect-to-an-active-session"></a>アクティブ セッションに接続する

ここまでは、セッションを作成したり開いたりしてきました。 次のステップは、セッションへの "*接続*" です。 接続後、レンダリング サーバーは画像を生成して、ビデオ ストリームをアプリケーションに送信します。

*RemoteRendering* スクリプトに次のコードを挿入します。重複する古いバージョンの関数は削除してください。

```csharp
    private bool isConnected = false;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;

            // If our session properties are 'Ready' we are ready to start connecting to the runtime of the service.
            if (status == RenderingSessionStatus.Ready)
            {
                session.ConnectionStatusChanged += AzureSession_OnConnectionStatusChanged;
            }
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
            session.ConnectionStatusChanged -= AzureSession_OnConnectionStatusChanged;
        }
    }

    private void AzureSession_OnConnectionStatusChanged(ConnectionStatus status, Result result)
    {
        Debug.Log($"Connection status: '{status}', result: '{result}'");
        isConnected = (status == ConnectionStatus.Connected);
    }

    public void ConnectSession()
    {
        arrService.CurrentActiveSession?.ConnectToRuntime(new ConnectToRuntimeParams());
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }
    }

    private void LateUpdate()
    {
        // The session must have its runtime pump updated.
        // The update will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
        arrService.CurrentActiveSession?.Actions.Update();
    }

    private void OnDisable()
    {
        DisconnectSession();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
    }
#endif
```

この機能をテストするには、以下の手順に従います。

1. Unity の **[Play]\(再生\)** を押します。
1. セッションを開きます。
    1. 既存のセッションがある場合は、 **[Query Active Sessions]\(アクティブ セッションの照会\)** 、 **[Use Existing session]\(既存のセッションの使用\)** の順に押します。
    1. それ以外の場合は、 **[Create Session]\(セッションの作成\)** を押します。
1. **[Connect]\(接続\)** を押します。
1. 数秒後、接続に成功したことがコンソール出力に表示されます。
1. 今のところ、それ以外の動作は生じません。
1. **[Disconnect]\(切断\)** を押すか、Unity の再生モードを停止します。

>[!NOTE]
> セッションは、複数のユーザーが "*開いて*" その情報を照会できますが、セッションに "*接続*" できるのは一度に 1 ユーザーだけです。 既に接続しているユーザーが他にも存在する場合、**ハンドシェイク エラー**で接続に失敗します。

## <a name="load-a-model"></a>モデルを読み込む

*RemoteRendering* スクリプトに次のコードを挿入します。重複する古いバージョンの関数は削除してください。

```csharp

    public string ModelName = "builtin://Engine";

    private Entity modelEntity = null;
    private GameObject modelEntityGO = null;

#if UNITY_WSA
    private WorldAnchor modelWorldAnchor = null;
#endif

    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();

        // get the game object representation of this entity
        modelEntityGO = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the entity will sync translations with the server
        var sync = modelEntityGO.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        // set position to an arbitrary distance from the parent
        modelEntityGO.transform.position = Camera.main.transform.position + Camera.main.transform.forward * 2;
        modelEntityGO.transform.localScale = Vector3.one;

#if UNITY_WSA
        // anchor the model in the world
        modelWorldAnchor = modelEntityGO.AddComponent<WorldAnchor>();
#endif

        // load a model that will be parented to the entity
        // We are using the 'from SAS' flavor because that variant can load the built-in model
        var loadModelParams = new LoadModelFromSASParams(ModelName, modelEntity);
        var async = arrService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
        async.ProgressUpdated += (float progress) =>
        {
            Debug.Log($"Loading: {progress * 100.0f}%");
        };

        await async.AsTask();
    }

    public void DestroyModel()
    {
        if (modelEntity == null)
        {
            return;
        }

#if UNITY_WSA
        DestroyImmediate(modelWorldAnchor);
#endif

        modelEntity.Destroy();
        modelEntity = null;

        DestroyImmediate(modelEntityGO);
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }

        DestroyModel();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }

                    if (modelEntity == null)
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Load Model"))
                        {
                            LoadModel();
                        }
                    }
                    else
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Destroy Model"))
                        {
                            DestroyModel();
                        }
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
        while (!isConnected)
        {
            yield return null;
        }
        LoadModel();
    }
#endif
```

再生ボタンを押してセッションを開き、そのセッションに接続すると、 **[Load Model]\(モデルの読み込み\)** ボタンが表示されます。 それをクリックすると、読み込みの進行状況がコンソール出力に表示され、100% に達するとエンジンのモデルが表示されます。

![エディターに読み込まれたモデル](media/model-loaded-replace-me.png)

[WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) は、[ホログラムの安定性](https://docs.microsoft.com/windows/mixed-reality/hologram-stability)を目的に使用される重要なコンポーネントです。 ただしその効果は、Mixed Reality デバイスにデプロイして初めて得られます。

> [!TIP]
> 「[クイックスタート: モデルをレンダリング用に変換する](../../quickstarts/convert-model.md)」をご覧になった読者は、独自のモデルの変換方法を既にご存じかと思います。 レンダリングに必要な操作は、変換したモデルの URI を *[Model Name]\(モデル名\)* プロパティに入力するだけです。

## <a name="display-frame-statistics"></a>フレームの統計情報を表示する

Azure Remote Rendering では、接続の品質についてさまざまな情報が追跡されます。 この情報を簡単に表示するには、次の手順を実行します。

[新しいスクリプト](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html)を作成し、**RemoteFrameStats** という名前を付けます。 スクリプト ファイルを開いて、その内容全体を次のコードに置き換えます。

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class RemoteFrameStats : MonoBehaviour
{
    public Text FrameStats = null;

    ARRServiceStats arrServiceStats = null;

#if UNITY_EDITOR
    private void OnEnable()
    {
        arrServiceStats = new ARRServiceStats();
    }

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            FrameStats.text = string.Empty;
            return;
        }

        arrServiceStats.Update(RemoteManagerUnity.CurrentSession);

        if (FrameStats != null)
        {
            FrameStats.text = arrServiceStats.GetStatsString();
        }
    }
#endif
}
```

GameObject を作成し、*FrameStats* という名前を付けます。 それを *Main Camera* オブジェクトに子ノードとしてアタッチし、その位置を **x = 0、y = 0、z = 0.325** に設定します。 このオブジェクトに **RemoteFrameStats** コンポーネントを追加します。

**UI > Canvas** 子オブジェクトを *FrameStats* オブジェクトに追加し、そのプロパティを次のように設定します。

![Canvas のプロパティ](media/framestats-canvas.png)

Canvas の子として **UI > Text** オブジェクトを追加し、そのプロパティを次のように設定します。

![Text のプロパティ](media/framestats-text.png)

*FrameStats* オブジェクトを選択したうえで、円アイコンをクリックし、**Text** オブジェクトを選択して、**FrameStats フィールド**の内容を設定します。

![Text プロパティの設定](media/framestats-set-text.png)

これでリモート セッションに接続すると、このテキストにストリーミングの統計情報が表示されます。

![フレームの統計情報の出力](media/framestats-output.png)

ヘッドロックされたテキスト ボックスは邪魔になることが考えられるため、このコードでは、エディターの外部で統計情報の更新を無効にしています。 より洗練された実装については、[クイックスタート](../../quickstarts/render-model.md) プロジェクトをご覧ください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、空の Unity プロジェクトを構成して Azure Remote Rendering と連動させるために必要なあらゆる手順について説明しました。 次のチュートリアルでは、リモート エンティティを操作する方法についてさらに詳しく見ていきましょう。

> [!div class="nextstepaction"]
> [チュートリアル:Unity でリモート エンティティを操作する](working-with-remote-entities.md)

---
title: Microsoft Teams on Azure Virtual Desktop - Azure
description: Microsoft Teams on Azure Virtual Desktop の使用方法。
author: Heidilohr
ms.topic: how-to
ms.date: 10/15/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 7b2a0c17ef0e55dc4dbe583a2dc60845dde94224
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130072387"
---
# <a name="use-microsoft-teams-on-azure-virtual-desktop"></a>Microsoft Teams on Azure Virtual Desktop を使用する

>[!IMPORTANT]
>Teams のメディア最適化は、Microsoft 365 Government (GCC) と　GCC-High　環境ではサポートされています。 Teams のメディア最適化は、Microsoft 365 DoD ではサポートされていません。

>[!NOTE]
>Microsoft Teams のメディア最適化は、Windows 10 マシン上の Windows デスクトップ クライアントのみ利用できます。 メディア最適化の利用には、Windows デスクトップ クライアントのバージョン 1.2.1026.0 以降が必要です。

Microsoft Teams on Azure Virtual Desktop は、チャットとコラボレーションをサポートしています。 メディア最適化が加わると、さらに通話と会議の機能もサポートされます。 仮想デスクトップ インフラストラクチャ (VDI) 環境で Microsoft Teams を使用する方法の詳細については、「[仮想デスクトップ インフラストラクチャ用の Teams](/microsoftteams/teams-for-vdi/)」を参照してください。

Microsoft Teams のメディア最適化では、Teams を使った通話と会議の音声やビデオが Windows デスクトップ クライアントによってローカルで処理されます。 他のクライアントをお使いの場合は、引き続き Microsoft Teams on Azure Virtual Desktop を利用できますが、最適化された通話と会議は利用できません。 Teams のチャットとコラボレーションの機能は、プラットフォームを問わずサポートされています。 リモート セッションにおけるローカル デバイスのリダイレクトについては、「[ホスト プールのリモート デスクトップ プロトコル プロパティをカスタマイズする](#customize-remote-desktop-protocol-properties-for-a-host-pool)」をご確認ください。

## <a name="prerequisites"></a>前提条件

Microsoft Teams on Azure Virtual Desktop を使用するには、次の操作を行う必要があります。

- Microsoft Teams 用の[ネットワークを準備します](/microsoftteams/prepare-network/)。
- [Windows PC 上で Microsoft Teams を使用するためのハードウェア要件](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/)を満たしている Windows 10 または Windows 10 IoT Enterprise デバイスに [Windows デスクトップ クライアント](./user-documentation/connect-windows-7-10.md)をインストールします。
- Windows 10 マルチセッションまたは Windows 10 Enterprise 仮想マシン (VM) に接続します。

## <a name="install-the-teams-desktop-app"></a>Teams デスクトップ アプリをインストールする

このセクションでは、Windows 10 マルチセッションまたは Windows 10 Enterprise の VM イメージに Teams のデスクトップ アプリをインストールする方法を説明します。 詳細については、「[VDI で Teams のデスクトップ アプリをインストールまたは更新する](/microsoftteams/teams-for-vdi#install-or-update-the-teams-desktop-app-on-vdi)」を参照してください。

### <a name="prepare-your-image-for-teams"></a>Teams 用のイメージを準備する

Teams でメディアの最適化を有効にするには、ホストで次のレジストリ キーを設定します。

1. スタート メニューから、**RegEdit** を管理者として実行します。 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams** に移動します。 Teams のキーがまだ存在しない場合は作成します。

2. Teams のキー用に以下の値を作成します。

| 名前             | Type   | データ/値  |
|------------------|--------|-------------|
| IsWVDEnvironment | DWORD  | 1           |

### <a name="install-the-teams-websocket-service"></a>Teams WebSocket Service をインストールする

最新バージョンの [Remote Desktop WebRTC Redirector Service](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWNg9F) を VM イメージにインストールします。 インストール エラーが発生した場合には、[最新の Microsoft Visual C++ 再頒布可能パッケージ](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)をインストールして、もう一度やり直してください。

#### <a name="latest-websocket-service-versions"></a>最新の WebSocket サービスのバージョン

次の表は、最新バージョンの WebSocket サービスを示しています。

|Version        |リリース日  |
|---------------|--------------|
|1.1.2110.16001 |2021 年 10 月 15 日    |
|1.0.2106.14001 |2021 年 7 月 29 日    |
|1.0.2006.11001 |2020 年 7 月 28 日    |
|0.11.0         |2020 年 5 月 29 日    |

#### <a name="updates-for-version-11211016001"></a>バージョン 1.1.2110.16001 用の更新プログラム

- 画面の共有時、画面が真っ暗になる問題を解消しました。 この問題が発生していた場合、Teams ウィンドウのサイズを変更し、この更新プログラムで解決されることを確認してください。 サイズの変更後、画面共有が再び機能し始めた場合、この更新プログラムでこの問題が解決されます。
- これでホスト VM からミーティング、着信音、通知音量を制御できるようになりました。 この機能は、1.2.2459 以降の [Windows Desktop クライアント](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)でのみ使用できます。
- これでインストーラーによって、更新プログラムがインストールされる前に Teams が閉じられるようになります。
- 呼び出しウィンドウの終了後、全画面に戻らない問題を解決しました。

#### <a name="updates-for-version-10210614001"></a>バージョン 1.0.2106.14001 用の更新プログラム

WebRTC リダイレクター サービスと WebRTC クライアント プラグインの間の接続の信頼性が向上しました。

#### <a name="updates-for-version-10200611001"></a>バージョン 1.0.2006.11001 用の更新プログラム

- 通話または会議中に Teams アプリを最小化すると、着信ビデオのドロップが発生する問題を修正しました。
- マルチモニター デスクトップ セッションで共有するモニターを 1 つ選択するためのサポートが追加されました。

### <a name="install-microsoft-teams"></a>Microsoft Teams をインストールする

コンピューター別またはユーザー別のインストールを使用して、Teams のデスクトップ アプリを展開できます。 Microsoft Teams を Azure Virtual Desktop 環境内にインストールするには、次のようにします。

1. 環境に合った [Teams MSI パッケージ](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm)をダウンロードします。 64 ビットのオペレーティング システムでは、64 ビットのインストーラーを使用することをお勧めします。

      > [!IMPORTANT]
      > Teams デスクトップ クライアント バージョン 1.3.00.21759 の最新の更新プログラムでは、Teams のチャット、チャネル、予定表に UTC タイム ゾーンが表示されていた問題が修正されました。 新しいバージョンのクライアントでは、リモート セッション タイム ゾーンが表示されます。

2. 次のいずれかのコマンドを実行して、ホスト VM に MSI をインストールします。

    - ユーザー別のインストール

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name>
        ```

        この処理は、 **%AppData%** ユーザー フォルダーに Teams をインストールする既定のインストールです。 非永続的なセットアップへのユーザー別のインストールでは、Teams は正しく機能しません。

    - コンピューター別のインストール

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSER=1
        ```

        これにより、32 ビット オペレーティング システム上の Program Files (x86) フォルダーと、64 ビット オペレーティング システム上の Program Files フォルダーに Teams がインストールされます。 この時点で、ゴールデン イメージのセットアップが完了しています。 非永続的なセットアップのためには、マシン単位で Teams をインストールする必要があります。

        Teams のインストール時に設定できるフラグには、**ALLUSER=1** と **ALLUSERS=1** の 2 つがあります。 これらのパラメーターの違いを理解することが重要です。 **ALLUSER=1** パラメーターは、コンピューターごとのインストールを指定するために VDI 環境でのみ使用されます。 **ALLUSERS=1** パラメーターは、VDI 以外の環境と VDI 環境で使用できます。 このパラメーターを設定すると、 **[Teams Machine-Wide Installer]** がコントロール パネルの [プログラムと機能] のほか [Windows の設定] の [アプリと機能] に表示されます。 コンピューターの管理者資格情報を持つすべてのユーザーが Teams をアンインストールできます。

        > [!NOTE]
        > この時点では、ユーザーと管理者がサインイン中に Teams の自動起動を無効にすることはできません。

3. ホスト VM から MSI をアンインストールするには、次のコマンドを実行します。

      ```powershell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      これにより、オペレーティング システム環境に応じて Program Files (x86) フォルダーまたは Program Files フォルダーから Teams がアンインストールされます。

      > [!NOTE]
      > MSI 設定 ALLUSER=1 を使用して Teams をインストールすると、自動更新が無効になります。 少なくとも 1 か月に 1 回、Teams を更新することをお勧めします。 Teams デスクトップ アプリのデプロイの詳細については、「[Teams デスクトップ アプリを VM に展開する](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/)」を参照してください。

### <a name="verify-media-optimizations-loaded"></a>メディア最適化が読み込まれたことを確認する

WebSocket Service と Teams デスクトップ アプリのインストールが終わったら、以下の手順に従って Teams のメディア最適化が読み込まれていることを確認します。

1. Teams アプリケーションを終了し、再起動します。

2. ユーザー プロファイル画像を選択し、 **[情報]** を選択します。

3. **[バージョン]** を選択します。

      メディアの最適化が読み込まれている場合、バナーに "**Azure Virtual Desktop メディアは最適化されています**" というメッセージが表示されます。 バナーに "**Azure Virtual Desktop メディアが接続されていません**" というメッセージが表示された場合は、Teams アプリを終了し、もう一度やり直してください。

4. ユーザー プロファイル画像を選択し、 **[設定]** を選択します。

      メディア最適化が読み込まれていると、デバイス メニューにローカルで利用できるオーディオ デバイスとカメラが列挙されます。 メニューに **[リモート オーディオ]** が表示されている場合は、Teams アプリを終了し、もう一度やり直してください。 それでもデバイスがメニューに表示されない場合は、ローカル PC のプライバシー設定を確認してください。 **[設定]**  >  **[プライバシー]**  >  **[アプリのアクセス許可 - マイク]** の下にある "**アプリからのマイクへのアクセスを許可する**" のトグルが **[オン]** になっていることを確認します。 リモート セッションから切断してから、もう一度オーディオ デバイスとビデオ デバイスを再接続します。 ビデオを使用して通話やミーティングに参加するには、アプリがカメラにアクセスするためのアクセス許可も付与する必要があります。

      最適化が読み込まれない場合は、Teams をアンインストールしてから再インストールし、もう一度確認してください。

## <a name="known-issues-and-limitations"></a>既知の問題と制限事項

仮想環境で使用する Teams は、仮想化されていない環境で使用する Teams とは異なります。 仮想環境での Teams に対する制約の詳細については、「[仮想デスクトップ インフラストラクチャ用の Teams](/microsoftteams/teams-for-vdi#known-issues-and-limitations)」を参照してください。

### <a name="client-deployment-installation-and-setup"></a>クライアントのデプロイ、インストール、およびセットアップ

- マシン単位のインストールの場合、VDI 環境の Teams が VDI 以外の環境の Teams クライアントのように自動的に更新されることはありません。 クライアントを更新するには、新しい MSI をインストールして VM イメージを更新する必要があります。
- Teams のメディア最適化は、Windows 10 が稼働しているマシン上の Windows デスクトップ クライアントでのみサポートされています。
- クライアント エンドポイント デバイスで定義されている明示的な HTTP プロキシの使用はサポートされていません。

### <a name="calls-and-meetings"></a>通話と会議

- Azure Virtual Desktop 環境内の Teams デスクトップ クライアントでは、ライブ イベントの作成はサポートされませんが、ライブ イベントに参加することはできます。 現時点でライブ イベントを作成する場合には、代わりにリモート セッション内で [Teams Web クライアント](https://teams.microsoft.com)を使用することをお勧めします。
- 通話、会議とも、現時点ではアプリケーションの共有をサポートしていません。 デスクトップ セッションは、デスクトップ共有をサポートしています。
- 制御の付与と取得は、現時点ではサポートされていません。
- Teams on Azure Virtual Desktop は、一度に 1 つの着信ビデオ入力のみをサポートしています。 つまり、だれかが画面を共有しようとすると、そのたびに会議リーダーの画面に代わってそのユーザーの画面が表示されます。
- WebRTC の制約により、ビデオ ストリームの解像度は着信、発信とも 720p となっています。
- Teams アプリでは、HID ボタンや他のデバイスを使った LED コントロールはサポートされていません。
- New Meeting Experience (NME) は、現在、VDI 環境ではサポートされていません。

Teams の既知の問題のうち、仮想環境に関係のないものについては、[組織における Teams のサポート](/microsoftteams/known-issues)に関するページを参照してください。

## <a name="collect-teams-logs"></a>Teams のログを収集する

お使いの Azure Virtual Desktop 環境内で Teams デスクトップ アプリに問題が発生した場合は、クライアント ログを収集します。クライアント ログは、ホスト VM 上の **%appdata%\Microsoft\Teams\logs.txt** にあります。

通話と会議に問題が発生した場合は、**Ctrl** + **Alt** + **Shift** + **1** を押して Teams Web クライアントのログを収集します。 ログは、ホスト VM 上の **%userprofile%\Downloads\MSTeams Diagnostics Log DATE_TIME.txt** に書き込まれます。

## <a name="contact-microsoft-teams-support"></a>Microsoft Teams サポートに問い合わせる

Microsoft Teams サポートに問い合わせる場合には、[Microsoft 365 管理センター](/microsoft-365/admin/contact-support-for-business-products)にアクセスしてください。

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>ホスト プールのリモート デスクトップ プロトコル プロパティをカスタマイズする

マルチモニター エクスペリエンス、マイクの有効化、オーディオ リダイレクトなど、ホスト プールのリモート デスクトップ プロトコル (RDP) のプロパティをカスタマイズすると、ユーザーのニーズに応じた最適なエクスペリエンスを提供できます。

メディア最適化ありの Teams を使用する場合には、デバイスのリダイレクトを有効にする必要はありません。 メディア最適化なしの Teams を使用する場合には、次の RDP プロパティを設定し、マイクとカメラのリダイレクトを有効にしてください。

- `audiocapturemode:i:1`: ローカル デバイスからのオーディオのキャプチャを有効にし、リモート セッション内のオーディオ アプリケーションをリダイレクトします。
- `audiomode:i:0`: ローカル コンピューター上でオーディオを再生します。
- `camerastoredirect:s:*`: すべてのカメラをリダイレクトします。

詳細については、「[ホスト プールのリモート デスクトップ プロトコル プロパティをカスタマイズする](customize-rdp-properties.md)」をご確認ください。

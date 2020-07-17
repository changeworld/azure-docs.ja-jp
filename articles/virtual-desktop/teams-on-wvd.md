---
title: Windows Virtual Desktop での Microsoft Teams - Azure
description: Windows Virtual Desktop で Microsoft Teams を使用する方法について説明します。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/29/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3fc44ca18f237fecd1c694e96f9ebc2d5b541757
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564461"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Windows Virtual Desktop で Microsoft Teams を使用する

>[!IMPORTANT]
>Microsoft Teams のメディア最適化は現在、パブリック プレビューの段階です。 Teams を運用環境のワークロードにデプロイする前に、Teams の最適化されたユーザー エクスペリエンスを評価することをお勧めします。 特定の機能はサポート対象ではなく、機能が制限されることがあります。

>[!NOTE]
>Microsoft Teams のメディア最適化は、Windows 10 マシン上の Windows デスクトップ クライアントのみ利用できます。 メディア最適化の利用には、Windows デスクトップ クライアントのバージョン 1.2.1026.0 以降が必要です。

Windows Virtual Desktop 上の Microsoft Teams は、チャットとコラボレーションをサポートしています。 メディア最適化が加わると、さらに通話と会議の機能もサポートされます。 仮想デスクトップ インフラストラクチャ (VDI) 環境で Microsoft Teams を使用する方法の詳細については、「[仮想デスクトップ インフラストラクチャ用の Teams](/microsoftteams/teams-for-vdi/)」を参照してください。

Microsoft Teams のメディア最適化では、Teams を使った通話と会議の音声やビデオが Windows デスクトップ クライアントによってローカルで処理されます。 他のクライアントをお使いの場合には、Windows Virtual Desktop 上で Microsoft Teams を利用できるという点は引き続き同じですが、最適化された通話と会議は利用できません。 Teams のチャットとコラボレーションの機能は、プラットフォームを問わずサポートされています。 リモート セッションにおけるローカル デバイスのリダイレクトについては、「[ホスト プールのリモート デスクトップ プロトコル プロパティをカスタマイズする](#customize-remote-desktop-protocol-properties-for-a-host-pool)」をご確認ください。

## <a name="prerequisites"></a>前提条件

Windows Virtual Desktop で Microsoft Teams を使用するには、次の操作を行う必要があります。

- Microsoft Teams 用の[ネットワークを準備します](/microsoftteams/prepare-network/)。
- [Windows PC 上で Microsoft Teams を使用するためのハードウェア要件](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/)を満たしている Windows 10 または Windows 10 IoT Enterprise デバイスに [Windows デスクトップ クライアント](connect-windows-7-and-10.md)をインストールします。
- Windows 10 マルチセッションまたは Windows 10 Enterprise 仮想マシン (VM) に接続します。
- マシン単位のインストールを使って、ホストに Teams のデスクトップ アプリをインストールします。 Microsoft Teams のメディア最適化の利用には、Teams デスクトップ アプリのバージョン 1.3.00.4461 以降が必要です。

## <a name="install-the-teams-desktop-app"></a>Teams デスクトップ アプリをインストールする

このセクションでは、Windows 10 マルチセッションまたは Windows 10 Enterprise の VM イメージに Teams のデスクトップ アプリをインストールする方法を説明します。 詳細については、「[VDI で Teams のデスクトップ アプリをインストールまたは更新する](/microsoftteams/teams-for-vdi#install-or-update-the-teams-desktop-app-on-vdi/)」を参照してください。

### <a name="prepare-your-image-for-teams"></a>Teams 用のイメージを準備する

Teams でメディアの最適化を有効にするには、ホストで次のレジストリ キーを設定します。

1. スタート メニューから、**RegEdit** を管理者として実行します。 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams** に移動します。
2. Teams のキー用に以下の値を作成します。

| 名前             | Type   | データ/値  |
|------------------|--------|-------------|
| IsWVDEnvironment | DWORD  | 1           |

### <a name="install-the-teams-websocket-service"></a>Teams WebSocket Service をインストールする

VM イメージに [WebSocket Service](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4yj0i) をインストールします。 インストール エラーが発生した場合には、[最新の Microsoft Visual C++ 再頒布可能パッケージ](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)をインストールして、もう一度やり直してください。

### <a name="install-microsoft-teams"></a>Microsoft Teams をインストールする

コンピューター別またはユーザー別のインストールを使用して、Teams のデスクトップ アプリを展開できます。 Microsoft Teams を Windows Virtual Desktop 環境にインストールするには、次のようにします。

1. 環境に合った [Teams MSI パッケージ](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/)をダウンロードします。 64 ビットのオペレーティング システムでは、64 ビットのインストーラーを使用することをお勧めします。

      > [!NOTE]
      > Microsoft Teams のメディア最適化の利用には、Teams デスクトップ アプリのバージョン 1.3.00.4461 以降が必要です。

2. 次のいずれかのコマンドを実行して、ホスト VM に MSI をインストールします。

    - ユーザー別のインストール

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSERS=1
        ```

        この処理は、 **%AppData%** ユーザー フォルダーに Teams をインストールする既定のインストールです。 非永続的なセットアップへのユーザー別のインストールでは、Teams は正しく機能しません。

    - コンピューター別のインストール

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSER=1 ALLUSERS=1
        ```

        これにより、64 ビット オペレーティング システム上の Program Files (x86) フォルダーと、32 ビット オペレーティング システム上の Program Files フォルダーに Teams がインストールされます。 この時点で、ゴールデン イメージのセットアップが完了しています。 非永続的なセットアップのためには、マシン単位で Teams をインストールする必要があります。

        次にセッションで Teams を開いたときに、資格情報の入力を求められます。

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

1. ユーザー プロファイル画像を選択し、 **[情報]** を選択します。
2. **[バージョン]** を選択します。

      メディアの最適化が読み込まれている場合、バナーに **"WVD Media optimized" (WVD メディアは最適化されています)** と表示されます。 バナーに **"WVD Media not connected" (WVD メディアが接続されていません)** と表示された場合には、Teams アプリを終了し、もう一度やり直してください。

3. ユーザー プロファイル画像を選択し、 **[設定]** を選択します。

      メディア最適化が読み込まれていると、デバイス メニューにローカルで利用できるオーディオ デバイスとカメラが列挙されます。 メニューに **[リモート オーディオ]** が表示されている場合は、Teams アプリを終了し、もう一度やり直してください。 メニューにデバイスが表示されない場合には、「[Microsoft Teams をインストールする](#install-microsoft-teams)」に戻り、インストール プロセスが完了しているかどうかを確認してください。

## <a name="known-issues-and-limitations"></a>既知の問題と制限事項

仮想環境で使用する Teams は、仮想化されていない環境で使用する Teams とは異なります。 仮想環境での Teams に対する制約の詳細については、「[仮想デスクトップ インフラストラクチャ用の Teams](/microsoftteams/teams-for-vdi#known-issues-and-limitations/)」を参照してください。

### <a name="client-deployment-installation-and-setup"></a>クライアントのデプロイ、インストール、およびセットアップ

- マシン単位のインストールの場合、VDI 環境の Teams が VDI 以外の環境の Teams クライアントのように自動的に更新されることはありません。 クライアントを更新するには、新しい MSI をインストールして VM イメージを更新する必要があります。
- Teams では現在、チャット、チャネル、予定表で UTC タイム ゾーンのみを表示しています。
- Teams のメディア最適化は、Windows 10 が稼働しているマシン上の Windows デスクトップ クライアントでのみサポートされています。
- エンドポイントで定義されている明示的な HTTP プロキシの使用はサポートされていません。

### <a name="calls-and-meetings"></a>通話と会議

- Windows Virtual Desktop 環境内の Teams デスクトップ クライアントは、ライブ イベントをサポートしていません。 現時点でライブ イベントに参加する場合には、代わりにリモート セッション内で [Teams Web クライアント](https://teams.microsoft.com)を使用することをお勧めします。
- 通話または会議の最中に Teams アプリを最小化すると、アプリを展開したときに着信ビデオ フィードが消えてしまうことがあります。
- 通話、会議とも、現時点ではアプリケーションの共有をサポートしていません。 デスクトップ セッションは、デスクトップ共有をサポートしています。
- マルチモニター構成でデスクトップを共有すると、全部のモニターが共有されます。
- 制御の付与と取得は、現時点ではサポートされていません。
- Windows Virtual Desktop 上の Teams は、一度に 1 つの着信ビデオ入力のみをサポートしています。 つまり、だれかが画面を共有しようとすると、そのたびに会議リーダーの画面に代わってそのユーザーの画面が表示されます。
- WebRTC の制約により、ビデオ ストリームの解像度は着信、発信とも 720p となっています。
- Teams アプリでは、HID ボタンや他のデバイスを使った LED コントロールはサポートされていません。

Teams の既知の問題のうち、仮想環境に関係のないものについては、[組織における Teams のサポート](/microsoftteams/known-issues/)に関するページを参照してください。

## <a name="uservoice-site"></a>UserVoice サイト

Windows Virtual Desktop 上の Microsoft Teams に関するフィードバックは、[UserVoice サイト](https://microsoftteams.uservoice.com/)からお送りください。

## <a name="collect-teams-logs"></a>Teams のログを収集する

Windows Virtual Desktop 環境で Teams デスクトップ アプリに問題が発生した場合には、クライアント ログを収集します。クライアント ログは、ホスト VM 上の **%appdata%\Microsoft\Teams\logs.txt** にあります。

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

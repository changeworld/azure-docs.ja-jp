---
title: Windows Virtual Desktop での Microsoft Teams - Azure
description: Windows Virtual Desktop で Microsoft Teams を使用する方法について説明します。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a860b005457c6e02187423a3ffbbc63fe7c758b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187530"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Windows Virtual Desktop で Microsoft Teams を使用する

> 適用対象:Windows 10、および Windows 10 IoT Enterprise

仮想化環境には、Microsoft Teams などのコラボレーション アプリに関する特定の一連の課題 (待機時間の増加、ホストの高い CPU 使用率、オーディオとビデオの全体的なパフォーマンスの低下など) があります。 VDI 環境で Microsoft Teams を使用する方法について詳しくは、[仮想化されたデスクトップ インフラストラクチャのためのチーム](https://docs.microsoft.com/microsoftteams/teams-for-vdi)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

Windows Virtual Desktop で Microsoft Teams を使用するには、次の操作を行う必要があります。

- Microsoft Teams の[ハードウェア要件](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)を満たしている Windows 10 デバイスに [Windows デスクトップ クライアント](connect-windows-7-and-10.md)をインストールします。
- Windows 10 マルチセッションまたは Windows 10 Enterprise 仮想マシン (VM) に接続します。
- Microsoft Teams 用の[ネットワークを準備します](https://docs.microsoft.com/microsoftteams/prepare-network)。

## <a name="use-unoptimized-microsoft-teams"></a>最適化されていない Microsoft Teams を使用する

Windows Virtual Desktop 環境で Microsoft Teams を使用して、Microsoft Teams のチャット機能とコラボレーション機能を活用できます。 Windows Virtual Desktop では、VDI オーディオとビデオ (AV) 最適化で Teams がサポートされません。 通話と会議はサポートされていません。 組織のポリシーによって許可される場合、音声で通話したり、音声で会議に参加したりできますが、通話の音声品質は最適化されておらず、ホスト構成次第であり、安定していないことがあります。 詳細については、「[VDI 上の Teams のパフォーマンスに関する考慮事項](https://docs.microsoft.com/microsoftteams/teams-for-vdi#teams-on-vdi-performance-considerations)」を参照してください。

### <a name="prepare-your-image-for-teams"></a>Teams 用のイメージを準備する

Teams をコンピューター別にインストールするには、ホストで次のレジストリ キーを設定します。

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 1
```

### <a name="install-microsoft-teams"></a>Microsoft Teams をインストールする

コンピューター別のインストールを使用して、Teams のデスクトップ アプリを展開できます。 Microsoft Teams を Windows Virtual Desktop 環境にインストールするには、次のようにします。

1. 環境に合った [Teams MSI パッケージ](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm)をダウンロードします。 64 ビットのオペレーティング システムでは、64 ビットのインストーラーを使用することをお勧めします。
2. 次のコマンドを実行して、ホスト VM に MSI をインストールします。

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      これにより、Program Files または Program Files (x86) に Teams がインストールされます。 次回 Teams にサインインして起動すると、アプリによって資格情報の入力が要求されます。

      > [!NOTE]
      > この時点では、ユーザーと管理者がサインイン中に Teams の自動起動を無効にすることはできません。

      ホスト VM から MSI をアンインストールするには、次のコマンドを実行します。

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > MSI 設定 ALLUSER=1 を使用して Teams をインストールすると、自動更新は無効になります。 少なくとも 1 か月に 1 回、Teams を更新することをお勧めします。 Teams デスクトップ アプリのデプロイの詳細については、「[Teams デスクトップ アプリを VM に展開する](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm)」を参照してください。

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>ホスト プールのリモート デスクトップ プロトコル プロパティをカスタマイズする
マルチ モニター エクスペリエンス、マイクの有効化、オーディオ リダイレクトなど、ホスト プールのリモート デスクトップ プロトコル (RDP) のプロパティをカスタマイズすると、ニーズに基づいてユーザーに最適なエクスペリエンスを提供できます。 **Set-RdsHostPool** コマンドレットの **-CustomRdpProperty** パラメーターを使用して、Windows Virtual Desktop の RDP プロパティをカスタマイズできます。
サポートされているプロパティとその既定値の全リストについては、「[サポートされるリモート デスクトップ RDP ファイルの設定](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context)」を参照してください。

---
title: "モビリティ サービスのインストール (VMware/物理から Azure へ) | Microsoft Docs"
description: "この記事では、モビリティ サービス エージェントをオンプレミスのマシンにインストールし、それらの保護を開始する方法について説明します。"
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 2/20/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 1f4075d6a3ab81bdbde614bbee400bd23f6cea20
ms.openlocfilehash: 1fd481d06f355547fd15200999c4bca3a503ec31
ms.lasthandoff: 02/23/2017

---

# <a name="installing-mobility-service-vmwarephysical-to-azure"></a>モビリティ サービスのインストール (VMware/物理から Azure へ)
モビリティ サービスは、Azure にレプリケートするすべてのマシン (VMware VM または物理サーバー) にデプロイする必要があります。 マシン上のデータの書き込みをキャプチャし、プロセス サーバーに転送します。 保護が必要なサーバーにモビリティ サービスをデプロイする方法には、次の方法があります


1. [System Center Configuration Manager などのソフトウェア デプロイメント ツールを使用してモビリティ サービスをインストールする](site-recovery-install-mobility-service-using-sccm.md)
2. [Azure Automation と Desired State Configuration(DSC) を使用してモビリティ サービスをインストールする](site-recovery-automate-mobility-service-install.md)
3. [グラフィカル ユーザー インターフェイス (GUI) を使用して、モビリティ サービスを手動でインストールする](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-using-the-graphical-user-interface)
4. [コマンド ラインを使用して、モビリティ サービスを手動でインストールする](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-using-command-line)
5. [Azure Site Recovery からのプッシュ インストールを使用してモビリティ サービスをインストールする](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-using-push-install-from-azure-site-recovery)


>[!IMPORTANT]
> バージョン 9.7.0.0 以降、Windows 仮想マシンでは、モビリティ サービスのインストーラーによって、公開されている最新の [Azure VM エージェント](../virtual-machines/virtual-machines-windows-extensions-features.md#azure-vm-agent)もインストールされます。 これにより、マシンが Azure にフェールオーバーされたときに、VM 拡張機能を使用するために必要なこの前提条件を満たすことができます。

## <a name="prerequisites"></a>前提条件
モビリティ サービスをサーバーに手動でインストールする前に、次の前提条件を実行してください。
1. 構成サーバーにログインし、コマンド プロンプトを管理者権限で開きます。
2. bin フォルダーに移動し、パスフレーズ ファイルを作成します

  ```
  cd %ProgramData%\ASR\home\svsystems\bin
  genpassphrase.exe -v > MobSvc.passphrase
  ```
3. このファイルはモビリティ サービスのインストール時に必要となるので、安全な場所に保存してください。
4. このディレクトリには、サポートされているすべてのオペレーティング システムを対象に、モビリティ サービスのインストーラーが置かれています     

  `%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

#### <a name="mobility-service-installer-to-operating-system-mapping"></a>モビリティ サービスのインストーラーからオペレーティング システムへのマッピング

| インストーラー ファイルのテンプレート名| オペレーティング システム |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 (64 ビット) SP1</br> Windows Server 2012 (64 ビット) </br> Windows Server 2012 R2 (64 ビット) |
|Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz| RHEL 6.4、6.5、6.6、6.7、6.8 (64 ビットのみ) </br> CentOS 6.4、6.5、6.6、6.7、 6.8 (64 ビットのみ) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (64 ビットのみ)|
|Microsoft-ASR_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4、6.5 (64 ビットのみ)|


## <a name="install-mobility-service-manually-using-the-graphical-user-interface"></a>グラフィカル ユーザー インターフェイスを使用して、モビリティ サービスを手動でインストールする

>[!NOTE]
> グラフィカル ユーザー インターフェイスを使用したインストールは、Microsoft Windows オペレーティング システムについてのみサポートされています。

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-using-command-line"></a>コマンド ラインを使用して、モビリティ サービスを手動でインストールする
### <a name="command-line-based-install-on-windows-computers"></a>Windows コンピューターでのコマンド ライン ベースのインストール
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-based-install-on-linux-computers"></a>Linux コンピューターでのコマンド ライン ベースのインストール
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-using-push-install-from-azure-site-recovery"></a>Azure Site Recovery からのプッシュ インストールを使用してモビリティ サービスをインストールする
Azure Site Recovery を使用してモビリティ サービスのプッシュ インストールを実行するには、すべてのターゲット コンピューターで、次の前提条件が満たされている必要があります。

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


## <a name="next-steps"></a>次のステップ
モビリティ サービスがインストールされたら、Azure Portalで **[+ レプリケート]** ボタンをクリックして、それらの VM の保護を有効化できます。


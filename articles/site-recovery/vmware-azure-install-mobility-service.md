---
title: モビリティ サービスのインストール (VMware または物理から Azure へ) | Microsoft Docs
description: モビリティ サービス エージェントをインストールして、オンプレミスの VMware 仮想マシンと、Azure Site Recovery を使用した物理サーバーを保護する方法について説明します。
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ramamill
ms.openlocfilehash: 094c1776c0760c04d85aff6ad3d812a2ad7afa56
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526999"
---
# <a name="install-the-mobility-service"></a>モビリティ サービスをインストールする 

Azure Site Recovery モビリティ サービスは、Azure にレプリケートする VMware VM と物理サーバーにインストールされます。 このサービスは、コンピューター上のデータ書き込みをキャプチャし、プロセス サーバーに転送します。 モビリティ サービスは、Azure にレプリケートするすべてのコンピューター (VMware VM または物理サーバー) にデプロイします。 モビリティ サービスは、保護するサーバーと VMware VM に対して、次の方法でデプロイできます。


* [System Center Configuration Manager などのソフトウェア デプロイメント ツールを使用してインストールする](vmware-azure-mobility-install-configuration-mgr.md)
* [Azure Automation と Desired State Configuration (Automation DSC) を使用してインストールする](vmware-azure-mobility-deploy-automation-dsc.md)
* [UI から手動でインストールする](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui)
* [コマンド プロンプトから手動でインストールする](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt)
* [Site Recovery のプッシュ インストールを使用してインストールする](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)


>[!IMPORTANT]
> バージョン 9.7.0.0 以降、**Windows VM では**、モビリティ サービスのインストーラーによって、公開されている最新の [Azure VM エージェント](../virtual-machines/extensions/features-windows.md#azure-vm-agent)もインストールされます。 コンピューターが Azure にフェールオーバーされたときに、コンピューターは VM 拡張機能を使用するためのエージェント インストール前提条件を満たしています。
> </br>**Linux VM** では、WALinuxAgent を手動でインストールする必要があります。

## <a name="prerequisites"></a>前提条件
モビリティ サービスをサーバーに手動でインストールする前に、次の前提条件を完了してください。
1. 構成サーバーにサインインし、コマンド プロンプト ウィンドウを管理者として開きます。
2. bin フォルダーに移動し、パスフレーズ ファイルを作成します。

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. 安全な場所にパスフレーズ ファイルを格納してください。 モビリティ サービスのインストール中にファイルを使用します。
4. サポートされているすべてのオペレーティング システムのモビリティ サービスのインストーラーは、%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository フォルダーにあります。

### <a name="mobility-service-installer-to-operating-system-mapping"></a>モビリティ サービスのインストーラーからオペレーティング システムへのマッピング

オペレーティング システム バージョンと互換性のあるモビリティ サービス パッケージの一覧については、[VMware 仮想マシンと物理サーバー用にサポートされているオペレーティング システム](vmware-physical-azure-support-matrix.md#replicated-machines)の一覧をご覧ください。

| インストーラー ファイルのテンプレート名| オペレーティング システム |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 SP1 (64 ビット) </br> Windows Server 2012 (64 ビット) </br> Windows Server 2012 R2 (64 ビット) </br> Windows Server 2016 (64 ビット) |
|Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* (64 ビットのみ) </br> CentOS 6.* (64 ビットのみ) |
|Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* (64 ビットのみ) </br> CentOS 7.* (64 ビットのみ) |
|Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1、SP2、SP3 (64 ビットのみ)|
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (64 ビットのみ)|
|Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 (64 ビットのみ)|
|Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4、6.5 (64 ビットのみ)|
|icrosoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04 (64 ビットのみ)|
|Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS サーバー (64 ビットのみ)|
|Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 (64 ビットのみ)|
|Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8 (64 ビットのみ)|

## <a name="install-mobility-service-manually-by-using-the-gui"></a>GUI を使用して、モビリティ サービスを手動でインストールする

>[!IMPORTANT]
> ある Azure サブスクリプション/リージョンから別の Azure サブスクリプション/リージョンに Azure IaaS 仮想マシンをレプリケートする目的で構成サーバーを使用している場合は、コマンドラインベースのインストール方法を使用してください。

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-at-a-command-prompt"></a>コマンド プロンプトで、モビリティ サービスを手動でインストールする

### <a name="command-line-installation-on-a-windows-computer"></a>Windows コンピューターへのコマンドライン インストール
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-installation-on-a-linux-computer"></a>Linux コンピューターへのコマンドライン インストール
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-by-push-installation-from-azure-site-recovery"></a>Azure Site Recovery からのプッシュ インストールを使用してモビリティ サービスをインストールする
Site Recovery を使用してモビリティ サービスのプッシュ インストールを実行できます。 すべてのターゲット コンピューターが以下の前提条件を満たす必要があります。

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


> [!NOTE]
モビリティ サービスがインストールされたら、Azure Portal で、**[+ レプリケート]** を選択し、これらの VM の保護を開始します。

## <a name="update-mobility-service"></a>モビリティ サービスを更新します。

> [!WARNING]
> 保護されたサーバー上のモビリティ サービスの更新を開始する前に、デプロイの一部である、構成サーバー、スケールアウト プロセス サーバー、およびマスター ターゲット サーバーを必ず更新します。

1. Azure Portal で、*コンテナー名* > **[レプリケートされたアイテム]** ビューの順に移動します。
2. 構成サーバーが既に最新バージョンに更新されている場合、"Site Recovery レプリケーション エージェントの新しい更新プログラムが利用可能です。 クリックしてインストールしてください" という通知が表示されます。

     ![[レプリケートされたアイテム] ウィンドウ](.\media\vmware-azure-install-mobility-service\replicated-item-notif.png)
3. この通知を選択して、仮想マシンの選択ページを開きます。
4. モビリティ サービスをアップグレードする仮想マシンを選択し、**[OK]** を選択します。

     ![[レプリケートされたアイテム] VM リスト](.\media\vmware-azure-install-mobility-service\update-okpng.png)

選択した各仮想マシンで、モビリティ サービスの更新ジョブが開始されます。

> [!NOTE]
> モビリティ サービスのインストールに使用するアカウントのパスワードを更新する方法については、[詳細をお読みください](vmware-azure-manage-configuration-server.md)。

## <a name="uninstall-mobility-service-on-a-windows-server-computer"></a>Windows Server コンピューターのモビリティ サービスをアンインストールする
Windows Server コンピューターのモビリティ サービスをアンインストールするには、次のメソッドのいずれかを使用します。

### <a name="uninstall-by-using-the-gui"></a>GUI を使用してアンインストールする
1. コントロール パネルで、**[プログラム]** をクリックします。
2. **Microsoft Azure Site Recovery Mobility Service/マスター ターゲット サーバー**を選択して **[アンインストール]** を選択します。

### <a name="uninstall-at-a-command-prompt"></a>コマンド プロンプトでアンインストールする
1. 管理者としてコマンド プロンプト ウィンドウを開きます。
2. モビリティ サービスをアンインストールするには、次のコマンドを実行します。

    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="uninstall-mobility-service-on-a-linux-computer"></a>Linux コンピューターのモビリティ サービスをアンインストールする
1. Linux サーバーで、**root** ユーザーとしてサインインします。
2. ターミナルで /user/local/ASR に移動します。
3. モビリティ サービスをアンインストールするには、次のコマンドを実行します。

    ```
    uninstall.sh -Y
    ```

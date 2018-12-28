---
title: Azure Site Recovery を使用した VMware VM と物理サーバーのディザスター リカバリーのためのモビリティ サービスについて | Microsoft Docs
description: Azure Site Recovery を使用して VMware VM と物理サーバーを Azure にディザスター リカバリーするためのモビリティ サービス エージェントについて説明します。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: e3967319cd41399209bd50886bce88efc8ba6ba6
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956518"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>VMware VM と物理サーバーのためのモビリティ サービスについて

[Azure Site Recovery](site-recovery-overview.md) を使用して VMware VM と物理サーバーのためのディザスター リカバリーを設定するとき、オンプレミスの VMware VM と物理サーバーごとに Site Recovery モビリティ サービスをインストールします。  このモビリティ サービスによって、マシン上のデータ書き込みがキャプチャされ、Site Recovery プロセス サーバーに転送されます。 次の方法でモビリティ サービスをデプロイできます。

[プッシュ インストール](vmware-azure-install-mobility-service.md):モビリティ サービスをプッシュ インストールするように Site Recovery を構成する:これを行うには、ディザスター リカバリーを設定するとき、Site Recovery プロセス サーバーがサービスをインストールする目的で VM または物理サーバーにアクセスするために使用するアカウントも設定します。
[手動インストール](vmware-physical-mobility-service-install-manual.md):UI またはコマンド プロンプトを使用し、各マシンでモビリティ サービスを手動インストールできます。
[自動デプロイ](vmware-azure-mobility-install-configuration-mgr.md):System Center Configuration Manager など、ソフトウェア デプロイ ツールでインストールを自動化できます。

## <a name="azure-virtual-machine-agent"></a>Azure 仮想マシン エージェント

- **Windows VM**:モビリティ サービスのバージョン 9.7.0.0 以降、[Azure VM エージェント](../virtual-machines/extensions/features-windows.md#azure-vm-agent)がモビリティ サービス インストーラーによってインストールされます。 マシンが Azure にフェールオーバーするとき、VM 拡張を使用するためのエージェント インストール前提条件を Azure VM が満たすようにします。
- **Linux VM**:フェールオーバー後、[WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) を Azure VM に手動インストールする必要があります。

## <a name="installer-files"></a>インストーラー ファイル

VMware VM と物理サーバーのオペレーティング システム別のインストーラー ファイルをまとめたのが次の表です。 開始前に[サポートされているオペレーティング システム](vmware-physical-azure-support-matrix.md#replicated-machines)を確認できます。


**インストーラー ファイル** | **オペレーティング システム (64 ビットのみ)** 
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.* 
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1、SP2、SP3 
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4、6.5
icrosoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS サーバー
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="anti-virus-on-replicated-machines"></a>レプリケートされるマシンでのウイルス対策

レプリケートするマシンでウイルス対策ソフトウェアがアクティブに実行されている場合は、モビリティ サービスのインストール フォルダーをウイルス対策の操作から除外してください (*C:\ProgramData\ASR\agent*)。 これにより、レプリケーションが意図したとおりに動作することが保証されます。

## <a name="update-the-mobility-service"></a>モビリティ サービスの更新

1. 保護されたマシン上のモビリティ サービスの更新を開始する前に、デプロイの一部である、構成サーバー、スケールアウト プロセス サーバー、マスター ターゲット サーバーを必ず更新します。
2. ポータルでコンテナーを開き、**[レプリケートされたアイテム]** を開きます。
3. 構成サーバーが最新版の場合、"Site Recovery レプリケーション エージェントの新しい更新プログラムが利用可能です。 クリックしてインストールしてください" という通知が表示されます。

     ![[レプリケートされたアイテム] ウィンドウ](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. 通知をクリックし、**[エージェントの更新]** でモビリティ サービスをアップグレードするマシンを選択します。 次に、 **[OK]** をクリックします

     ![[レプリケートされたアイテム] VM リスト](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. 選択したマシンごとに、モビリティ サービスの更新ジョブが開始されます。

## <a name="update-the-acount-used-for-push-installation-of-the-mobility-service"></a>モビリティ サービスのプッシュ インストールに使用されるアカウントを更新する

Site Recovery をデプロイしたとき、モビリティ サービスのプッシュ インストールを有効にする目的で、アカウントを指定しました。このアカウントは、マシンで複製が有効になっているとき、マシンにアクセスし、サービスをインストールするために Site Recovery プロセス サーバーによって使用されます。 このアカウントの資格情報を更新する場合、[こちらの手順](vmware-azure-manage-configuration-server.md)に従ってください。

## <a name="uninstall-the-mobility-service"></a>Mobility Service をアンインストールする

### <a name="on-a-windows-machine"></a>Windows マシンの場合

UI またはコマンド プロンプトからアンインストールします。

- **UI から**:マシンの [コントロール パネル] で **[プログラム]** を選択します。 **[Microsoft Azure Site Recovery Mobility Service/マスター ターゲット サーバー]** > 、**[アンインストール]** の順に選択します。
- **コマンド プロンプトから**:マシンの管理者としてコマンド プロンプト ウィンドウを開きます。 次のコマンドを実行します。 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="on-a-linux-machine"></a>Linux マシンの場合
1. Linux マシンで**ルート** ユーザーとしてサインインします。
2. ターミナルで /user/local/ASR に移動します。
3. 次のコマンドを実行します。

    ```
    uninstall.sh -Y
    ```

## <a name="next-steps"></a>次の手順

[モビリティ サービスにプッシュ インストールを設定する](vmware-azure-install-mobility-service.md)。

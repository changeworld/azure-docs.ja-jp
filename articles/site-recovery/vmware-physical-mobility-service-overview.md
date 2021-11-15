---
title: Azure Site Recovery を使用した VMware VM と物理サーバーのディザスター リカバリーのためのモビリティ サービスについて | Microsoft Docs
description: Azure Site Recovery サービスを使用した VMware VM と物理サーバーの Azure へのディザスター リカバリーのためのモビリティ サービス エージェントについて説明します。
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: how-to
ms.author: sharrai
ms.date: 08/19/2021
ms.openlocfilehash: 1bf251d6aa45aaf0306d3e595a674280214dc64a
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131867306"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>VMware VM と物理サーバーのためのモビリティ サービスについて

[Azure Site Recovery](site-recovery-overview.md) を使用して VMware 仮想マシン (VM) と物理サーバーのディザスター リカバリーを設定する場合は、オンプレミスの各 VMware VM と物理サーバーに Site Recovery モビリティ サービスをインストールします。 このモビリティ サービスによって、マシン上のデータ書き込みがキャプチャされ、Site Recovery プロセス サーバーに転送されます。 モビリティ サービスは、ユーザーがデプロイできるモビリティ サービス エージェント ソフトウェアによって次の方法でインストールされます。

- [プッシュ インストール](#push-installation):Azure portal 経由で保護が有効になっている場合は、Site Recovery がサーバーにモビリティ サービスをインストールします。
- 手動インストール: [ユーザー インターフェイス (UI)](#install-the-mobility-service-using-ui-classic) または[コマンド プロンプト](#install-the-mobility-service-using-command-prompt-classic)を使用して、各マシンにモビリティ サービスを手動でインストールできます。
- [自動デプロイ](vmware-azure-mobility-install-configuration-mgr.md):構成マネージャーなどのソフトウェア デプロイ ツールを使用して、モビリティ サービスのインストールを自動化できます。

> [!NOTE]
> モビリティ サービスは、VMware VM または物理マシンのソース マシン上のメモリの約 6 ～ 10% を使用します。

## <a name="antivirus-on-replicated-machines"></a>レプリケートされるマシン上のウイルス対策

レプリケートするマシンでウイルス対策ソフトウェアが実行されている場合は、モビリティ サービスのインストール フォルダー _C:\ProgramData\ASR\agent_ をウイルス対策の操作から除外してください。 この除外により、レプリケーションが期待どおりに動作するようになります。

## <a name="push-installation"></a>プッシュ インストール

プッシュ インストールは、[レプリケーションを有効にする](vmware-azure-enable-replication.md#enable-replication)ために Azure portal から実行されるジョブの不可欠な部分です。 保護してレプリケーションを有効にする一連の VM を選択すると、構成サーバーはモビリティ サービス エージェントをサーバーにプッシュし、エージェントをインストールして、構成サーバーへのエージェントの登録を完了します。

### <a name="prerequisites"></a>前提条件

- プッシュ インストールのすべての[前提条件](vmware-azure-install-mobility-service.md)を満たしていることを確認します。
- すべてのサーバー構成が、[VMware VM と物理サーバーの Azure へのディザスター リカバリーに関するサポート マトリックス](vmware-physical-azure-support-matrix.md)にある条件を満たしていることを確認します。
- 9\.36 バージョン以降の SUSE Linux Enterprise Server 11 SP3、RHEL 5、CentOS 5、Debian 7 については、最新のインストーラーが[構成サーバーとスケールアウト プロセス サーバーで確実に使用できる](#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server)ようにしてください。

プッシュ インストールのワークフローは、以降のセクションで説明されています。

### <a name="mobility-service-agent-version-923-and-higher"></a>モビリティ サービス エージェント バージョン 9.23 以上

バージョン 9.23 の詳細については、[Azure Site Recovery 用の更新プログラム ロールアップ 35](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) に関するページを参照してください。

モビリティ サービスのプッシュ インストール中に、次の手順が実行されます。

1. エージェントがソース マシンにプッシュされます。 エージェントのソース マシンへのコピーが、複数の環境エラーのために失敗する場合があります。 プッシュ インストールの失敗のトラブルシューティングについては、[ガイダンス](vmware-azure-troubleshoot-push-install.md)を参照してください。
1. エージェントがサーバーに正常にコピーされると、サーバー上で前提条件の確認が実行されます。
   - すべての前提条件が満たされた場合は、インストールが開始されます。
   - 1 つ以上の[前提条件](vmware-physical-azure-support-matrix.md)が満たされていない場合、インストールは失敗します。
1. エージェントのインストールの一部として、Azure Site Recovery 用のボリューム シャドウ コピー サービス (VSS) プロバイダーがインストールされます。 この VSS プロバイダーは、アプリケーション整合性復旧ポイントを生成するために使用されます。 VSS プロバイダーのインストールが失敗した場合、この手順はスキップされ、エージェントのインストールが続行されます。
1. エージェントのインストールは成功したが、VSS プロバイダーのインストールが失敗した場合は、ジョブの状態が **[警告]** としてマークされます。 これは、クラッシュ整合性復旧ポイントの生成に影響を与えません。

    - アプリケーション整合性復旧ポイントを生成するには、[このガイダンス](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine)を参照して、Site Recovery VSS プロバイダーの手動インストールを完了してください。
    - アプリケーション整合性復旧ポイントを生成したくない場合は、[レプリケーション ポリシーを変更](vmware-azure-set-up-replication.md#create-a-policy)して、アプリケーション整合性復旧ポイントをオフにしてください。

### <a name="mobility-service-agent-version-922-and-below"></a>モビリティ サービス エージェント バージョン 9.22 以下

1. エージェントがソース マシンにプッシュされます。 エージェントのソース マシンへのコピーが、複数の環境エラーのために失敗する場合があります。 プッシュ インストールの失敗をトラブルシューティングするには、[このガイダンス](vmware-azure-troubleshoot-push-install.md)を参照してください。
1. エージェントがサーバーに正常にコピーされると、サーバー上で前提条件の確認が実行されます。
   - すべての前提条件が満たされた場合は、インストールが開始されます。
   - 1 つ以上の[前提条件](vmware-physical-azure-support-matrix.md)が満たされていない場合、インストールは失敗します。

1. エージェントのインストールの一部として、Azure Site Recovery 用のボリューム シャドウ コピー サービス (VSS) プロバイダーがインストールされます。 この VSS プロバイダーは、アプリケーション整合性復旧ポイントを生成するために使用されます。
   - VSS プロバイダーのインストールが失敗したした場合、エージェントのインストールは失敗します。 エージェントのインストールの失敗を回避するには、[バージョン 9.23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) 以上を使用してクラッシュ整合性復旧ポイントを生成し、VSS プロバイダーの手動インストールを実行します。

## <a name="install-the-mobility-service-using-ui-classic"></a>UI を使用して Mobility サービスをインストールする (クラシック)

>[!NOTE]
> このセクションは、Azure Site Recovery - クラシックに適用されます。 [プレビューのインストール手順についてはこちらを参照してください](#install-the-mobility-service-using-ui-preview)
### <a name="prerequisites"></a>前提条件

- すべてのサーバー構成が、[VMware VM と物理サーバーの Azure へのディザスター リカバリーに関するサポート マトリックス](vmware-physical-azure-support-matrix.md)にある条件を満たしていることを確認します。
- サーバーのオペレーティング システムの[インストーラーを見つけます](#locate-installer-files)。

>[!IMPORTANT]
> Azure IaaS (サービスとしてのインフラストラクチャ) VM をある Azure リージョンから別のリージョンにレプリケートしている場合は、UI のインストール方法を使用しないでください。 [コマンド プロンプト](#install-the-mobility-service-using-command-prompt-classic)でのインストールを使用してください。

1. インストール ファイルをマシンにコピーして実行します。
1. **[Installation Option]\(インストール オプション\)** で **[Install mobility service]\(モビリティ サービスのインストール\)** を選択します。
1. インストールの場所を選択し、 **[インストール]** を選択します。

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="モビリティ サービスのインストール オプションのページ。":::

1. **[インストールの進行状況]** でインストールを監視します。 インストールが完了したら、 **[Proceed to Configuration]\(構成に進む\)** を選択して、構成サーバーにサービスを登録します。

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="インストールの進行状況と、インストールが完了したら、アクティブな構成を続けるボタンを示すスクリーンショット。":::

1. **[構成サーバーの詳細]** で、構成した IP アドレスとパスフレーズを指定します。

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="モビリティ サービスの登録ページ。":::

1. **[登録]** を選択して登録を完了します。

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="モビリティ サービスの登録の最後のページ。":::

## <a name="install-the-mobility-service-using-command-prompt-classic"></a>コマンド プロンプトを使用して Mobility サービスをインストールする (クラシック)

>[!NOTE]
> このセクションは、Azure Site Recovery - クラシックに適用されます。 [プレビューのインストール手順についてはこちらを参照してください](#install-the-mobility-service-using-command-prompt-preview)。

### <a name="prerequisites"></a>前提条件

- すべてのサーバー構成が、[VMware VM と物理サーバーの Azure へのディザスター リカバリーに関するサポート マトリックス](vmware-physical-azure-support-matrix.md)にある条件を満たしていることを確認します。
- サーバーのオペレーティング システムの[インストーラーを見つけます](#locate-installer-files)。

### <a name="windows-machine"></a>Windows マシン

- コマンド プロンプトから、次のコマンドを実行して、保護するサーバー上のローカル フォルダー (_C:\Temp_ など) にインストーラーをコピーします。 インストーラーのファイル名を実際のファイル名に置き換えます。

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- 次のコマンドを実行して、エージェントをインストールします。

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- 次のコマンドを実行して、エージェントを構成サーバーに登録します。

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>インストールの設定

設定 | 詳細
--- | ---
構文 | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
セットアップ ログ | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | 必須のインストール パラメーターです。 Mobility サービス (エージェント) またはマスター ターゲット (MasterTarget) をインストールするかどうかを指定します。  注: 以前のバージョンでは、正しいスイッチは Mobility サービス (MS) またはマスター ターゲット (MT) でした。
`/InstallLocation`| 省略可能なパラメーター。 モビリティ サービスのインストール場所 (任意のフォルダー) を指定します。
`/Platform` | 必須。 モビリティ サービスがインストールされるプラットフォームを指定します。 <br/> VMware VM/物理サーバーの場合は **VMware**。 <br/> Azure VM の場合は **Azure**。<br/><br/> Azure VM を物理マシンとして扱う場合は、**VMware** を指定します。
`/Silent`| 省略可能。 インストーラーをサイレント モードで実行するかどうかを指定します。

#### <a name="registration-settings"></a>登録設定
設定 | 詳細
--- | ---
構文 | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
エージェント構成ログ | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | 必須パラメーターです。 `<CSIP>` は、構成サーバーの IP アドレスを指定します。 任意の有効な IP アドレスを使用します。
`/PassphraseFilePath` |  必須。 パスフレーズの場所。 任意の有効な UNC またはローカル ファイル パスを使用します。

### <a name="linux-machine"></a>Linux マシン

1. ターミナル セッションから、保護するサーバー上のローカル フォルダー ( _/tmp_ など) にインストーラーをコピーします。 インストーラーのファイル名を Linux ディストリビューションの実際のファイル名に置き換えてから、次のコマンドを実行します。

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. 次のようにインストールします (root アカウントは必要ありませんが、root アクセス許可は必要です)。

   ```shell
   sudo ./install -d <Install Location> -r Agent -v VmWare -q
   ```

3. インストールが完了したら、モビリティ サービスを構成サーバーに登録する必要があります。 次のコマンドを実行して、モビリティ サービスを構成サーバーに登録します。

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>インストールの設定

設定 | 詳細
--- | ---
構文 | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | 必須のインストール パラメーターです。 モビリティ サービス (MS) またはマスター ターゲット (MT) をインストールするかどうかを指定します。
`-d` | 省略可能なパラメーター。 モビリティ サービスのインストールの場所 (`/usr/local/ASR`) を指定します。
`-v` | 必須。 モビリティ サービスがインストールされるプラットフォームを指定します。 <br/> VMware VM/物理サーバーの場合は **VMware**。 <br/> Azure VM の場合は **Azure**。
`-q` | 省略可能。 インストーラーをサイレント モードで実行するかどうかを指定します。

#### <a name="registration-settings"></a>登録設定

設定 | 詳細
--- | ---
構文 | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | 必須パラメーターです。 `<CSIP>` は、構成サーバーの IP アドレスを指定します。 任意の有効な IP アドレスを使用します。
`-P` |  必須。 パスフレーズが保存されているファイルの完全なファイル パス。 任意の有効なフォルダーを使用します。

## <a name="azure-virtual-machine-agent"></a>Azure 仮想マシン エージェント

- **Windows VM**:モビリティ サービスのバージョン 9.7.0.0 以降、[Azure VM エージェント](../virtual-machines/extensions/features-windows.md#azure-vm-agent)がモビリティ サービス インストーラーによってインストールされます。 これにより、マシンが Azure にフェールオーバーするとき、Azure VM は確実に、VM 拡張機能を使用するためのエージェント インストールの前提条件を満たすようになります。
- **Linux VM**: フェールオーバー後、[WALinuxAgent](../virtual-machines/extensions/update-linux-agent.md) は Azure VM に自動的にインストールされます。

## <a name="locate-installer-files"></a>インストーラー ファイルを検索する

構成サーバーで、フォルダー _%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository_ に移動します。 オペレーティング システムに基づいて、どのインストーラーが必要かを確認します。 VMware VM と物理サーバーのオペレーティング システム別のインストーラー ファイルをまとめたのが次の表です。 開始する前に、[サポートされているオペレーティング システム](vmware-physical-azure-support-matrix.md#replicated-machines)を確認できます。

> [!NOTE]
> ファイル名では、実際の値のプレースホルダーとして _version_ と _date_ が含まれている、次の表に示す構文が使用されます。 実際のファイル名は次の例のようになります。
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

インストーラー ファイル | オペレーティング システム (64 ビットのみ)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
[このフォルダーに手動でダウンロードして配置します](#rhel-5-or-centos-5-server) | Red Hat Enterprise Linux (RHEL) 5 </br> CentOS 5
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_RHEL8-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 8 </br> CentOS 8
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> SP2 と SP3 が含まれます。
[このフォルダーに手動でダウンロードして配置します](#suse-11-sp3-server) | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_SLES15-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 15
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 6.4 </br> Oracle Enterprise Linux 6.5
`Microsoft-ASR_UA_version_OL7-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 7
`Microsoft-ASR_UA_version_OL8-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 8
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14.04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Ubuntu Linux 16.04 LTS サーバー
`Microsoft-ASR_UA_version_UBUNTU-18.04-64_GA_date_release.tar.gz` | Ubuntu Linux 18.04 LTS サーバー
`Microsoft-ASR_UA_version_UBUNTU-20.04-64_GA_date_release.tar.gz` | Ubuntu Linux 20.04 LTS サーバー
[このフォルダーに手動でダウンロードして配置します](#debian-7-server) | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8
`Microsoft-ASR_UA_version_DEBIAN9-64_GA_date_release.tar.gz` | Debian 9

## <a name="download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server"></a>SUSE 11 SP3、RHEL 5、Debian 7 サーバー用の最新のモビリティ エージェント インストーラーをダウンロードします

### <a name="suse-11-sp3-server"></a>SUSE 11 SP3 サーバー

9.36 バージョン以降の **SUSE Linux Enterprise Server 11 SP3 マシンをアップデートまたは保護するための前提条件** には、以下があります。

1. 最新のモビリティ エージェント インストーラーが Microsoft ダウンロード センターからダウンロードされ、構成サーバーおよびすべてのスケールアウト プロセス サーバーのプッシュ インストーラー リポジトリに配置されていることを確認します
2. 最新の SUSE Linux Enterprise Server 11 SP3 エージェント インストーラーを[ダウンロード](site-recovery-whats-new.md)します。
3. 構成サーバーに移動し、次のパスに SUSE Linux Enterprise Server 11 SP3 エージェント インストーラーをコピーします: INSTALL_DIR\home\svsystems\pushinstallsvc\repository
1. 最新のインストーラーをコピーしたら、InMage PushInstall サービスを再起動します。
1. 次に、関連するスケールアウト プロセス サーバーに移動し、手順 3 と手順 4 を繰り返します。
1. **たとえば**、インストール パスが C:\Program Files (x86)\Microsoft Azure Site Recovery の場合、前述のディレクトリは次のようになります。
    1. C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository

### <a name="rhel-5-or-centos-5-server"></a>RHEL 5 または CentOS 5 サーバー

バージョン 9.36 以降の **RHEL 5 マシンを更新または保護するための前提条件** には、以下があります。

1. 最新のモビリティ エージェント インストーラーが Microsoft ダウンロード センターからダウンロードされ、構成サーバーおよびすべてのスケールアウト プロセス サーバーのプッシュ インストーラー リポジトリに配置されていることを確認します
2. 最新の RHEL 5 または CentOS 5 エージェント インストーラーを[ダウンロード](site-recovery-whats-new.md)します。
3. 構成サーバーに移動し、次のパスに RHEL 5 または CentOS 5 エージェント インストーラーをコピーします: INSTALL_DIR\home\svsystems\pushinstallsvc\repository
1. 最新のインストーラーをコピーしたら、InMage PushInstall サービスを再起動します。
1. 次に、関連するスケールアウト プロセス サーバーに移動し、手順 3 と手順 4 を繰り返します。
1. **たとえば**、インストール パスが C:\Program Files (x86)\Microsoft Azure Site Recovery の場合、前述のディレクトリは次のようになります。
    1. C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository

## <a name="debian-7-server"></a>Debian 7 サーバー

バージョン 9.36 以降の **Debian 7 マシンを更新または保護するための前提条件** には、以下があります。

1. 最新のモビリティ エージェント インストーラーが Microsoft ダウンロード センターからダウンロードされ、構成サーバーおよびすべてのスケールアウト プロセス サーバーのプッシュ インストーラー リポジトリに配置されていることを確認します
2. 最新の Debian 7 エージェント インストーラーを[ダウンロード](site-recovery-whats-new.md)します。
3. 構成サーバーに移動し、次のパスに Debian 7 エージェント インストーラーをコピーします: INSTALL_DIR\home\svsystems\pushinstallsvc\repository
1. 最新のインストーラーをコピーしたら、InMage PushInstall サービスを再起動します。
1. 次に、関連するスケールアウト プロセス サーバーに移動し、手順 3 と手順 4 を繰り返します。
1. **たとえば**、インストール パスが C:\Program Files (x86)\Microsoft Azure Site Recovery の場合、前述のディレクトリは次のようになります。
    1. C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository

## <a name="install-the-mobility-service-using-ui-preview"></a>UI を使用して Mobility サービスをインストールする (プレビュー)

>[!NOTE]
> このセクションは、Azure Site Recovery - プレビューに適用されます。 [クラシックのインストール手順についてはこちらを参照してください](#install-the-mobility-service-using-ui-classic)。

### <a name="prerequisites"></a>[前提条件]

次の手順で、サーバーのオペレーティング システム用のインストーラー ファイルを見つけます。  
- アプライアンス上で、*E:Software\Agents* フォルダーに移動します。
- ソース マシンのオペレーティング システムに対応するインストーラーをコピーし、ソース マシンのローカル フォルダー (*C:Azure Site Recovery\Agent* など) に置きます。

**次の手順で Mobility サービスをインストールします。**

1. コマンド プロンプトを開き、インストーラー ファイルが置かれているフォルダーに移動します。

   ```cmd
    cd C:\Azure Site Recovery\Agent*
   ```

2. 次のコマンドを実行して、インストーラー ファイルを抽出します。

   ```cmd
   .\Microsoft-ASR_UA*Windows*release.exe /q /x:C:\Azure Site Recovery\Agent
   ```

3. 次のコマンドを実行して、インストールを続行します。 これにより、インストーラーの UI が起動します。

   ```cmd
   .\UnifiedAgentInstaller.exe /Platform vmware /Role MS /CSType CSPrime /InstallLocation "C:\Azure Site Recovery\Agent"
   ```

   >[!NOTE]
   >UI に表示されるインストール先は、コマンドで指定した場所と同じです。

4. **[インストール]** をクリックします。

   これにより、Mobility サービスのインストールが開始されます。

   インストールが完了するまで待ちます。 完了すると、登録手順に進み、ソース マシンを任意のアプライアンスに登録することができます。

   ![Mobility サービスのインストール UI オプションを示す画像](./media/vmware-physical-mobility-service-overview-preview/mobility-service-install.png)

   ![Mobility サービスのインストールの進行状況を示す画像](./media/vmware-physical-mobility-service-overview-preview/installation-progress.png)

5. **[マシンの詳細]** フィールドに表示されている文字列をコピーします。

   このフィールドには、ソース マシンに固有の情報が含まれています。 この情報は、[Mobility サービスの構成ファイル](#generate-mobility-service-configuration-file)を生成するために必要です。

   ![ソース マシンの文字列を示す画像](./media/vmware-physical-mobility-service-overview-preview/source-machine-string.png)

6.  統合エージェント コンフィギュレーターで **Mobility サービス構成ファイル** のパスを指定します。
7.  **[登録]** をクリックします。

    これにより、ソース マシンがアプライアンスに正常に登録されます。

## <a name="install-the-mobility-service-using-command-prompt-preview"></a>コマンド プロンプトを使用して Mobility サービスをインストールする (プレビュー)

>[!NOTE]
> このセクションは、Azure Site Recovery - プレビューに適用されます。 [クラシックのインストール手順についてはこちらを参照してください](#install-the-mobility-service-using-command-prompt-classic)。

### <a name="windows-machine"></a>Windows マシン
1. コマンド プロンプトを開き、インストーラー ファイルが置かれているフォルダーに移動します。

   ```cmd
   cd C:\Azure Site Recovery\Agent
   ```
2. 次のコマンドを実行してインストーラー ファイルを抽出します。
   ```cmd
       .\Microsoft-ASR_UA*Windows*release.exe /q /x:C:\Azure Site Recovery\Agent
    ```
3. インストールを続行するには、次のコマンドを実行します。

   ```cmd

    .\UnifiedAgentInstaller.exe /Platform vmware /Silent /Role MS /CSType CSPrime /InstallLocation "C:\Azure Site Recovery\Agent"
   ```
    インストールが完了したら、パラメーター *Agent Config Input* と共に生成された文字列をコピーします。 この文字列は、[Mobility サービスの構成ファイル](#generate-mobility-service-configuration-file)を生成するために必要です。

    ![構成ファイルをダウンロードするためのサンプル文字列 ](./media/vmware-physical-mobility-service-overview-preview/configuration-string.png)

4. 正常にインストールが完了したら、次のコマンドを使用してソース マシンを前述のアプライアンスに登録します。

   ```cmd
   "C:\Azure Site Recovery\Agent\agent\UnifiedAgentConfigurator.exe" /SourceConfigFilePath "config.json" /CSType CSPrime
   ```

#### <a name="installation-settings"></a>インストールの設定

設定 | 詳細
--- | ---
構文 | `.\UnifiedAgentInstaller.exe /Platform vmware /Role MS /CSType CSPrime /InstallLocation <Install Location>`
`/Role` | 必須のインストール パラメーターです。 Mobility サービス (MS) をインストールするかどうかを指定します。
`/InstallLocation`| 省略可能。 モビリティ サービスのインストール場所 (任意のフォルダー) を指定します。
`/Platform` | 必須。 モビリティ サービスがインストールされるプラットフォームを指定します。 <br/> VMware VM/物理サーバーの場合は **VMware**。 <br/> Azure VM の場合は **Azure**。<br/><br/> Azure VM を物理マシンとして扱う場合は、**VMware** を指定します。
`/Silent`| 省略可能。 インストーラーをサイレント モードで実行するかどうかを指定します。
`/CSType`| 必須。 プレビューまたはレガシ アーキテクチャを定義するために使用されます (CSPrime または CSLegacy)。

#### <a name="registration-settings"></a>登録設定

設定 | 詳細
--- | ---
構文 | `"<InstallLocation>\UnifiedAgentConfigurator.exe" /SourceConfigFilePath "config.json" /CSType CSPrime >`
`/SourceConfigFilePath` | 必須。 Mobility サービス構成ファイルの完全なファイル パス。 任意の有効なフォルダーを使用します。
`/CSType` |  必須。 プレビューまたはレガシ アーキテクチャを定義するために使用されます (CSPrime または CSLegacy)。


### <a name="linux-machine"></a>Linux マシン

1. ターミナル セッションから、保護するサーバー上のローカル フォルダー ( **/tmp** など) にインストーラーをコピーします。 次のコマンドを実行します。

   ```shell
       cd /tmp ;
       tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. インストールするには、次のコマンドを使用します。
   ```shell
        ./install -q -r MS -v VmWare -c CSPrime
    ```

    インストールが完了したら、パラメーター *Agent Config Input* と共に生成された文字列をコピーします。 この文字列は、[Mobility サービスの構成ファイル](#generate-mobility-service-configuration-file)を生成するために必要です。

3. 正常にインストールが完了したら、次のコマンドを使用してソース マシンを前述のアプライアンスに登録します。

   ```shell
        <InstallLocation>/Vx/bin/UnifiedAgentConfigurator.sh -c CSPrime -S config.json -q
    ```
#### <a name="installation-settings"></a>インストールの設定

  設定 | 詳細
  --- | ---
    構文 | `./install -q -r MS -v VmWare -c CSPrime`
    `-r` | 必須。 インストール パラメーター。 Mobility サービス (MS) をインストールするかどうかを指定します。
    `-d` | 省略可能。 モビリティ サービスのインストールの場所 (`/usr/local/ASR`) を指定します。
    `-v` | 必須。 モビリティ サービスがインストールされるプラットフォームを指定します。 <br/> VMware VM/物理サーバーの場合は **VMware**。 <br/> Azure VM の場合は **Azure**。
    `-q` | 省略可能。 インストーラーをサイレント モードで実行するかどうかを指定します。
    `-c` | 必須。 プレビューまたはレガシ アーキテクチャを定義するために使用されます (CSPrime または CSLegacy)。

#### <a name="registration-settings"></a>登録設定

  設定 | 詳細
  --- | ---
    構文 | `cd <InstallLocation>/Vx/bin UnifiedAgentConfigurator.sh -c CSPrime -S -q`  
    `-s` | 必須。 Mobility サービス構成ファイルの完全なファイル パス。 任意の有効なフォルダーを使用します。
    `-c` |  必須。 プレビューまたはレガシ アーキテクチャを定義するために使用されます (CSPrime または CSLegacy)。
    `-q` |  省略可能。 インストーラーをサイレント モードで実行するかどうかを指定します。

## <a name="generate-mobility-service-configuration-file"></a>Mobility サービス構成ファイルを生成する

  Mobility サービス構成ファイルを生成するには、次の手順を実行します。

  1. ソース マシンを登録するアプライアンスに移動します。 Microsoft Azure Appliance Configuration Manager を開き、 **[Mobility service configuration details]\(Mobility サービスの構成の詳細\)** セクションに移動します。
  2. Mobility サービスからコピーした [マシンの詳細] の文字列を貼り付け、ここの入力フィールドに貼り付けます。
  3. **[Download configuration file]\(構成ファイルのダウンロード\)** をクリックします。

  ![Mobility サービスのダウンロード構成ファイル オプションを示す画像](./media/vmware-physical-mobility-service-overview-preview/download-configuration-file.png)

これにより、Mobility サービス構成ファイルがダウンロードされます。 このファイルをソース マシンのローカル フォルダーにコピーします。 Mobility サービス インストーラーと同じフォルダーに配置できます。

[Mobility サービスのアップグレード](upgrade-mobility-service-preview.md)に関する情報を参照してください。

## <a name="next-steps"></a>次のステップ

[モビリティ サービスにプッシュ インストールを設定する](vmware-azure-install-mobility-service.md)。

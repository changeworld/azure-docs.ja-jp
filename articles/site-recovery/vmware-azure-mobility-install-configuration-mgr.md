---
title: Azure Site Recovery でインストールのディザスター リカバリー用のモビリティ サービスを自動化する
description: Azure Site Recovery で、VMware/物理サーバーのディザスター リカバリー用のモビリティ サービスのインストールを自動化する方法。
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 12/22/2019
ms.author: ramamill
ms.openlocfilehash: 318b73011901e9ab07643bc2ecec28e5016e8702
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2020
ms.locfileid: "75613909"
---
# <a name="automate-mobility-service-installation"></a>モビリティ サービスのインストールを自動化する

この記事では、[Azure Site Recovery](site-recovery-overview.md) でモビリティ サービス エージェントのインストールと更新を自動化する方法について説明します。

オンプレミス VMware VM と物理サーバーのディザスター リカバリー用の Site Recovery を Azure にデプロイする場合、レプリケートする各マシンにモビリティ サービス エージェントをインストールします。 このモビリティ サービスによって、マシン上のデータ書き込みが取り込まれ、レプリケーションのために Site Recovery プロセス サーバーに転送されます。 モビリティ サービスは、次のようないくつかの方法でデプロイできます。

- **プッシュ インストール**:Azure portal でマシンのレプリケーションを有効にする場合に、Site Recovery によってモビリティ サービス エージェントをインストールします。
- **手動インストール**:各マシンにモビリティ サービスを手動でインストールします。 プッシュ インストールと手動インストールの[詳細情報](vmware-physical-mobility-service-overview.md)を参照してください。
- **自動デプロイ**:System Center Configuration Manager などのソフトウェア デプロイ ツールや、Intigua JetPatch などのサード パーティ ツールなどで、インストールを自動化します。

インストールと更新を自動化することで、以下の場合の解決策が得られます。

- 組織で、保護されたサーバーへのプッシュ インストールが許可されない。
- 会社のポリシーで、パスワードを定期的に変更することが求められている。 プッシュ インストール用のパスワードを指定する必要がある。
- セキュリティ ポリシーで、特定のマシンにファイアウォール例外を追加することが許可されていない。
- ホスティング サービス プロバイダーとしてサービスを提供しており、Site Recovery を使用したプッシュ インストールに必要な顧客のマシン資格情報を提供したくない。
- エージェントのインストールを多数のサーバーに同時にスケーリングする必要がある。
- 計画メンテナンスの期間中にインストールとアップグレードをスケジュールしたい。



## <a name="prerequisites"></a>前提条件

インストールを自動化するには、次のものが必要です。

- [Configuration Manager](https://docs.microsoft.com/configmgr/) や [JetPatch](https://jetpatch.com/microsoft-azure-site-recovery/) などの、デプロイ済みのソフトウェア インストール ソリューション。 
-  [Azure](tutorial-prepare-azure.md) と[オンプレミス](vmware-azure-tutorial-prepare-on-premises.md)で、VMware のディザスター リカバリー用、または[物理サーバー](physical-azure-disaster-recovery.md)のディザスター リカバリー用のデプロイの前提条件が満たされている。 ディザスター リカバリーの[サポート要件](vmware-physical-azure-support-matrix.md)も確認する必要があります。

## <a name="prepare-for-automated-deployment"></a>自動デプロイを準備する

次の表に、モビリティ サービスのデプロイを自動化するためのツールとプロセスをまとめています。

**ツール** | **詳細** | **手順**
--- | --- | ---
**構成マネージャー** | 1.上記の[前提条件](#prerequisites)が満たされていることを確認します。 <br/><br/>2.ソース環境をセットアップ (OVF テンプレートを使用して Site Recovery 構成サーバーを VMware VM としてデプロイするための OVA ファイルのダウンロードなど) して、ディザスター リカバリーをデプロイします。<br/><br/> 2.構成サーバーを Site Recovery サービスに登録し、ターゲット Azure 環境を設定して、レプリケーション ポリシーを構成します。<br/><br/> 3.モビリティ サービスの自動デプロイのために、構成サーバーのパスフレーズとモビリティ サービスのインストール ファイルを含むネットワーク共有を作成します。<br/><br/> 4.インストールまたは更新を含む Configuration Manager パッケージを作成し、モビリティ サービスのデプロイを準備します。<br/><br/> 5.その後、モビリティ サービスがインストールされているマシンに対して Azure へのレプリケーションを有効にできます。 | [Configuration Manager を使用して自動化する](#automate-with-configuration-manager)。
**JetPatch** | 1.上記の[前提条件](#prerequisites)が満たされていることを確認します。 <br/><br/> 2.ソース環境をセットアップ (OVF テンプレートを使用して Azure Site Recovery 用の JetPatch Agent Manager を Site Recovery 環境にダウンロードしてデプロイするなど) して、ディザスター リカバリーをデプロイします。<br/><br/> 2.構成サーバーを Site Recovery に登録し、ターゲット Azure 環境を設定して、レプリケーション ポリシーを構成します。<br/><br/> 3.自動デプロイのために、JetPatch Agent Manager の構成を初期化して完了します。<br/><br/> 4.JetPatch では、モビリティ サービス エージェントのデプロイとアップグレードを自動化する Site Recovery ポリシーを作成できます。 <br/><br/> 5.その後、モビリティ サービスがインストールされているマシンに対して Azure へのレプリケーションを有効にできます。 | [JetPatch Agent Manager を使用して自動化します](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)。<br/><br/> JetPatch で[エージェントのインストールをトラブルシューティングします](https://kc.jetpatch.com/hc/articles/360035981812)。

## <a name="automate-with-configuration-manager"></a>Configuration Manager を使用して自動化する

### <a name="prepare-the-installation-files"></a>インストール ファイルを準備する

1. 前提条件を満たしていることを確認します。
2. 構成サーバーを実行しているマシンからアクセスできる、セキュリティで保護されたネットワーク ファイル共有 (SMB 共有) を作成します。
3. Configuration Manager で、モビリティ サービスをインストールまたは更新する[サーバーを分類](https://docs.microsoft.com/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections)します。 1 つのコレクションにはすべての Windows サーバーを含め、もう 1 つにはすべての Linux サーバーを含めてください。 
5. ネットワーク共有で、次のフォルダーを作成します。

    - Windows マシンにインストールする場合は、フォルダー **MobSvcWindows** を作成します。
    - Linux マシンにインストールする場合は、フォルダー **MobSvcLinux** を作成します。

6. 構成サーバー マシンにサインインします。
7. マシンで管理者のコマンド プロンプトを開きます。
8. このコマンドを実行して、パスフレーズ ファイルを生成します。

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
9. MobSvc.passphrase ファイルを Windows フォルダーと Linux フォルダーにコピーします。
10. このコマンドを実行して、インストール ファイルを含むフォルダーを参照します。

    ```
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

11. これらのインストール ファイルをネットワーク共有にコピーします。

    - **MobSvcWindows** に **Microsoft-ASR_UA_version_Windows_GA_date_Release.exe** をコピーします
    - **MobSvcLinux** に次のものをコピーします。
        - Microsoft-ASR_UA*RHEL6-64*release.tar.gz
        - Microsoft-ASR_UA*RHEL7-64*release.tar.gz
        - Microsoft-ASR_UA*SLES11-SP3-64*release.tar.gz
        - Microsoft-ASR_UA*SLES11-SP4-64*release.tar.gz
        - Microsoft-ASR_UA*OL6-64*release.tar.gz
        - Microsoft-ASR_UA*UBUNTU-14.04-64*release.tar.gz
      
12. 次の手順で説明するように、Windows または Linux フォルダーにコードをコピーします。 次のことが前提となっています。
    - 構成サーバーの IP アドレスは 192.168.3.121 である。
    - セキュリティで保護されたネットワーク ファイル共有は **\\\ContosoSecureFS\MobilityServiceInstallers** である。

### <a name="copy-code-to-the-windows-folder"></a>Windows フォルダーにコードをコピーする

次のコードをコピーします。

- それを **install.bat** として **MobSvcWindows** フォルダーに保存します。
- このスクリプトの [CSIP] プレース ホルダーを、お使いの構成サーバーの IP アドレスの実際の値に置き換えてください。
- このスクリプトでは、モビリティ サービス エージェントの新規インストールと、インストール済みエージェントへの更新がサポートされます。

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log


```
### <a name="copy-code-to-the-linux-folder"></a>Linux フォルダーにコードをコピーする

次のコードをコピーします。

- それを **install_linux sh** として **MobSvcLinux** フォルダーに保存します。
- このスクリプトの [CSIP] プレース ホルダーを、お使いの構成サーバーの IP アドレスの実際の値に置き換えてください。
- このスクリプトでは、モビリティ サービス エージェントの新規インストールと、インストール済みエージェントへの更新がサポートされます。

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```


### <a name="create-a-package"></a>パッケージを作成する

1. Configuration Manager コンソールにサインインし、 **[ソフトウェア ライブラリ]**  >  **[アプリケーションの管理]**  >  **[パッケージ]** と移動します。
2. **[パッケージ]** を右クリックして **[パッケージの作成]** に進みます。
3. 名前、説明、製造元、言語、バージョンなど、パッケージの詳細を指定します。
4. **[このパッケージはソース ファイルを含む]** を選択します。
5. **[参照]** をクリックし、関連するインストーラー (MobSvcWindows または MobSvcLinux) が含まれているネットワーク共有とフォルダーを選択して、 **[次へ]** をクリックします。

   ![パッケージとプログラムの作成ウィザードのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. **[作成するプログラムの種類の選択]** ページで、 **[標準プログラム]**  >  **[次へ]** を選択します。

   ![パッケージとプログラムの作成ウィザードのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. **[この標準プログラムに関する情報の指定]** ページで、次の値を指定します。

    **パラメーター** | **Windows の値** | **Linux の値**
    --- | --- | ---
    **Name** | Microsoft Azure Mobility Service のインストール (Windows) | Microsoft Azure Mobility Service のインストール (Linux)。
    **コマンド ライン** | install.bat | ./install_linux.sh
    **プログラムの実行条件** | ユーザーがログオンしているかどうか | ユーザーがログオンしているかどうか
    **その他のパラメーター** | 既定の設定を使用する | 既定の設定を使用する

   ![パッケージとプログラムの作成ウィザードのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. **[Specify the requirements for this standard program]\(この標準プログラムの要件の指定\)** で、次のことを行います。

    - Windows マシンの場合は、 **[指定したプラットフォームだけで実行可能]** を選択します。 次に、[サポートされている Windows オペレーティング システム](vmware-physical-azure-support-matrix.md#replicated-machines)を選択します。 続けて、 **[次へ]** をクリックします。
    - Linux マシンでは、 **[任意のプラットフォームで実行可能]** を選択します。 続けて、 **[次へ]** をクリックします。
   
10. ウィザードを終了します。



### <a name="deploy-the-package"></a>パッケージをデプロイする

1. Configuration Manager コンソールで、パッケージを右クリックして **[コンテンツの配布]** に進みます。
   ![Configuration Manager コンソールのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. パッケージのコピー先とする配布ポイントを選択します。 [詳細については、こちらを参照してください](https://docs.microsoft.com/sccm/core/servers/deploy/configure/install-and-configure-distribution-points)。
3. ウィザードを完了します。 指定した配布ポイントへのパッケージのレプリケートが開始されます。
4. パッケージの配布が完了したら、パッケージを右クリックして **[展開]** に進みます。
   ![Configuration Manager コンソールのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. 以前に作成した Windows または Linux デバイスのコレクションを選択します。
6. **[コンテンツの配布先の指定]** ページで **[配布ポイント]** を選択します。
7. **[このソフトウェアの展開を制御する設定の指定]** ページで、 **[目的]** を **[必須]** に設定します。

   ![ソフトウェアの展開ウィザードのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. **[この展開のスケジュールを指定します]** でスケジュールを設定します。 [詳細については、こちらを参照してください](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched)。

    - モビリティ サービスは、指定したスケジュールに従ってインストールされます。 
    - 不要な再起動を避けるためには、月次のメンテナンス期間中またはソフトウェアの更新期間中にパッケージのインストールをスケジュールします。
9. **[配布ポイント]** ページで、設定を構成し、ウィザードを完了します。
10. Configuration Manager コンソールでデプロイの進行状況を監視します。 **[監視]** 、 >  **[展開]** 、 >  *[対象のパッケージ名]* と移動します。





### <a name="uninstall-the-mobility-service"></a>Mobility Service をアンインストールする
Configuration Manager パッケージを作成して Mobility Service をアンインストールできます。 これを行うには、次のスクリプトを実行します。

```
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT

```

## <a name="next-steps"></a>次のステップ
次に、VM の[保護を有効にします](vmware-azure-enable-replication.md)。

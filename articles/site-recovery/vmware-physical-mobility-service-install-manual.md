---
title: Azure Site Recovery での VMware VM と物理サーバーのディザスター リカバリーのためにモビリティ サービスを手動でインストールする | Microsoft Docs
description: Azure Site Recovery を使用して VMware VM と物理サーバーを Azure にディザスター リカバリーするために、モビリティ サービス エージェントをインストールする方法について説明します。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: d0180a32f2a4258cb728869e2cb9f2abd57cc545
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52845962"
---
# <a name="install-the-mobility-service-manually-on-vmware-vms-and-physical-servers"></a>VMware VM と物理サーバーにモビリティ サービスを手動でインストールする

[Azure Site Recovery](site-recovery-overview.md) を使用して VMware VM と物理サーバーのためのディザスター リカバリーを設定するとき、オンプレミスの VMware VM と物理サーバーごとに [Site Recovery モビリティ サービス](vmware-physical-mobility-service-overview.md)をインストールします。  このモビリティ サービスによって、マシン上のデータ書き込みがキャプチャされ、Site Recovery プロセス サーバーに転送されます。

この記事では、保護対象の各マシンにモビリティ サービスを手動でインストールする方法について説明します。

## <a name="create-a-passphrase"></a>パスフレーズを作成する

インストールする前に、インストールの間に使用されるパスフレーズを作成します。

1. 構成サーバーにサインインします。
2. 管理者としてコマンド プロンプトを開きます。
3. bin フォルダーに移動し、パスフレーズ ファイルを作成します。

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. 安全な場所にパスフレーズ ファイルを格納してください。 


## <a name="install-the-service-from-the-ui"></a>UI からサービスをインストールする

>[!IMPORTANT]
> Azure IaaS VM を Azure リージョン間でレプリケートしている場合は、この方法を使用しないでください。 代わりに、コマンド ライン ベースのインストール方法を使用します。

1. マシンのオペレーティング システムに必要なインストーラーのバージョンを見つけます。 インストーラーは、%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository フォルダーにあります。 必要なインストーラーを[確認](vmware-physical-mobility-service-overview.md#installer-files)します。
2. インストール ファイルをマシンにコピーして実行します。
3. **[Installation Option]\(インストール オプション\)** で **[Install mobility service]\(モビリティ サービスのインストール\)** を選択します。
4. インストール先の場所を選択し、**[インストール]** を選択します。

    ![モビリティ サービスのインストール オプションのページ](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

5. **[インストールの進行状況]** でインストールを監視します。 インストールが完了したら、**[Proceed to Configuration]\(構成に進む\)** を選択して、構成サーバーにサービスを登録します。

    ![モビリティ サービスの登録ページ](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

6.  **[Configuration Server Details]\(構成サーバーの詳細\)** で、構成した IP アドレスとパスフレーズを指定します。  

    ![モビリティ サービスの登録ページ](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

7. **[登録]** を選択して登録を完了します。

    ![モビリティ サービス登録の最後のページ](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-the-service-from-the-command-prompt"></a>コマンド プロンプトからサービスをインストールする

### <a name="on-a-windows-machine"></a>Windows マシンの場合

1. 保護するサーバー上のローカル フォルダー (C:\Temp など) にインストーラーをコピーします。 

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. 次のようにインストールします。

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

3. 構成サーバーにエージェントを登録します。

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>インストールの設定
**設定** | **詳細**
--- | ---
使用法 | UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
セットアップ ログ | %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log の下。
/Role | 必須のインストール パラメーターです。 モビリティ サービス (MS) またはマスター ターゲット (MT) をインストールするかどうかを指定します。
/InstallLocation| 省略可能なパラメーター。 モビリティ サービスのインストール場所 (任意のフォルダー) を指定します。
/Platform | 必須。 モビリティ サービスをインストールするプラットフォームを指定します。 VMware VM/物理サーバーの場合は **VMware**、Azure VM の場合は **Azure**。 
/Silent| 省略可能。 インストーラーをサイレント モードで実行するかどうかを指定します。

#### <a name="registration-settings"></a>登録設定
**設定** | **詳細**
--- | ---
使用法 | UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
エージェント構成ログ | %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log の下。
/CSEndPoint | 必須パラメーターです。 構成サーバーの IP アドレスを指定します。 任意の有効な IP アドレスを使用します。
/PassphraseFilePath |  必須。 パスフレーズの場所。 任意の有効な UNC またはローカル ファイル パスを使用します。


### <a name="on-a-linux-machine"></a>Linux マシンの場合

1. 保護するサーバー上のローカル フォルダー (/tmp など) にインストーラーをコピーします。 ターミナルから次のコマンドを実行します。
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. 次のようにインストールします。

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. インストールが完了したら、モビリティ サービスを構成サーバーに登録する必要があります。 以下のコマンドを実行して、モビリティ サービスを構成サーバーに登録できます。

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```


#### <a name="installation-settings"></a>インストールの設定
**設定** | **詳細**
--- | ---
使用法 | ./install -d <Install Location> -r <MS|MT> -v VmWare -q
-r | 必須のインストール パラメーターです。 モビリティ サービス (MS) またはマスター ターゲット (MT) をインストールするかどうかを指定します。
-d | 省略可能なパラメーター。 モビリティ サービスのインストール場所 (/usr/local/ASR) を指定します。
-v | 必須。 モビリティ サービスをインストールするプラットフォームを指定します。 VMware VM/物理サーバーの場合は **VMware**、Azure VM の場合は **Azure**。 
パラメーター | 省略可能。 インストーラーをサイレント モードで実行するかどうかを指定します。

#### <a name="registration-settings"></a>登録設定
**設定** | **詳細**
--- | ---
使用法 | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
-i | 必須パラメーターです。 構成サーバーの IP アドレスを指定します。 任意の有効な IP アドレスを使用します。
-P |  必須。 パスフレーズが保存されているファイルの完全なファイル パス。 任意の有効なフォルダーを使用します

## <a name="next-steps"></a>次の手順
- [VMware VM のディザスター リカバリーを設定する](vmware-azure-tutorial.md)
- [物理サーバーのディザスター リカバリーを設定する](physical-azure-disaster-recovery.md)

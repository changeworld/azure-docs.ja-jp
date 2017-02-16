---
title: "ソフトウェア展開ツールを使用して Azure Site Recovery の Mobility Service インストールを自動化する |Microsoft Docs"
description: "この記事は、System Center Configuration Manager などのソフトウェア展開ツールを使用して Mobility Service のインストールを自動化する場合に役立ちます"
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/10/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: da682645640d74485a1caaff82fd0483a0e3f149
ms.openlocfilehash: 6f10ea05edd3cb5b472b1102946b8da2dd1862d2

---
# <a name="automate-mobility-service-installation-using-software-deployment-tools"></a>ソフトウェア展開ツールを使用して Mobility Service インストールを自動化する

この記事では、System Center Configuration Manager (SCCM) を使用して、Azure Site Recovery の Mobility Service をデータセンターに展開する方法の例を示します。 SCCM のようなソフトウェア展開ツールを使用することには、次の利点があります。
* ソフトウェア更新の計画的なメンテナンス期間中に、展開の新規インストールとアップグレードをスケジュールできる。
* 同時に数百のサーバーを大規模に展開できる。


> [!NOTE]
> この記事では、System Center Configuration Manager 2012 R2 を使用して、展開アクティビティを示します。 Mobility Service のインストールは、[Azure Automation と Desired State Configuration](site-recovery-automate-mobility-service-install.md) を使用しても自動化できます。

## <a name="prerequisites"></a>前提条件
1. 環境内に既に配置されている System Center Configuration Manager(SCCM) などのソフトウェア展開ツール。
  * 2 つの[デバイス コレクション](https://technet.microsoft.com/library/gg682169.aspx)を作成します。1 つはすべての **Windows サーバー**用、もう&1; つは Azure Site Recovery を使用して保護するすべての **Linux サーバー**用とします。
3. Azure Site Recovery に既に登録されている構成サーバー。
4. SCCM サーバーがアクセスできるセキュリティで保護されたネットワーク ファイル共有 (SMB 共有)。

## <a name="deploy-mobility-service-on-computers-running-microsoft-windows-operating-systems"></a>Microsoft Windows オペレーティング システムを実行しているコンピューターに Mobility Service を展開する
> [!NOTE]
> この記事では、以下を前提としています。
> 1. 構成サーバーの IP アドレスは 192.168.3.121 である
> 2. セキュリティで保護されたネットワーク ファイル共有は \\\ContosoSecureFS\MobilityServiceInstallers である

### <a name="step-1-prepare-for-deployment"></a>手順 1: デプロイの準備をする
1. ネットワーク共有にフォルダーを作成し、**MobSvcWindows** という名前を付けます。
2. 構成サーバーにログインし、管理者権限のコマンド プロンプトを開きます。
3. パスフレーズ ファイルを生成するには、次のコマンドを実行します。

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
6. MobSvc.passphrase ファイルをネットワーク共有上の MobSvcWindows フォルダーにコピーします。
5. 次のコマンドを実行して構成サーバーでインストーラーのリポジトリを参照します。

  `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`
6. **Microsoft ASR\_UA\_*バージョン*\_Windows\_GA\_*日付*\_Release.exe** をネットワーク共有上の **MobSvcWindows * * フォルダーにコピーします。
7. 以下のコードをコピーして、**install.bat** という名前で **MobSvcWindows** フォルダーに保存します。
> [!NOTE]
> 以下のスクリプトの [CSIP] プレース ホルダーを、お使いの構成サーバーの IP アドレスの実際の値にかならず置き換えてください。

  [!INCLUDE [site-recovery-sccm-windows-script](../../includes/site-recovery-sccm-windows-script.md)]

### <a name="step-2-create-a-package"></a>手順 2: パッケージを作成する

1. System Center Configuration Manager Console にログインします。
2. **[ソフトウェア ライブラリ]** > **[アプリケーション管理]** > **[パッケージ]** を参照します。
3. **[パッケージ]** を右クリックして **[パッケージの作成]** を選択します。
4. 名前、説明、製造元、言語、バージョンの値を指定します。
5. **[このパッケージにソース ファイルを含める]** チェック ボックスをオンします。
6. **[参照]** ボタンをクリックして、インストーラーが格納されているネットワーク共有 (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows) を選択します。

  ![sccm パッケージの作成](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package.png)

7. **[作成するプログラムの種類の選択]** ページで、**[標準プログラム]** を選択して **[次へ]** をクリックします。

  ![sccm パッケージの作成](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)
8. **[この標準プログラムに関する情報の指定]** ページで、次の入力を指定して **[次へ]** をクリックします。 (その他の入力は既定値のまま残すことができます)

  ![sccm パッケージ プロパティ](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties.png)   
| **パラメーター名** | **値** |
|--|--|
| 名前 | Microsoft Azure Mobility Service のインストール (Windows) |
| コマンド ライン | install.bat |
| プログラムの実行条件 | ユーザーがログオンしているかどうか |
9. 次のページで、ターゲットのオペレーティング システムを選択します。 Mobility Service は、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 のみにインストールできます。
  ![sccm パッケージ プロパティ ページ&2;](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2.png)   
10. ウィザードを完了するには、[次へ] を&2; 回クリックします。
> [!NOTE]
> スクリプトでは、Mobility Service エージェントの新規インストールとインストール済みエージェントのアップグレードまたは更新の両方をサポートします。

### <a name="step-3-deploy-the-package"></a>手順 3: パッケージを展開する
1. SCCM コンソールで、パッケージを右クリックして **[コンテンツの配布]** を選択します。
  ![sccm パッケージの配布](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)
2. パッケージのコピー先とする **[[配布ポイント]](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** を選択します。
3. ウィザードを完了すると、指定した配布ポイントへのパッケージのレプリケートが開始されます。
4. パッケージの配布が完了したら、パッケージを右クリックして **[展開]** を選択します。
  ![sccm パッケージの展開](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)
5. 展開のターゲット コレクションとして前提条件のセクションで作成した Widows Server デバイスのコレクションを選択します。

  ![sccm のターゲット コレクションの選択](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection.png)
6. **[コンテンツの配布先の指定]** ページで**配布ポイント**を選択します。
7. **[このソフトウェアの展開を制御する設定の指定]** ページで、必要に応じて目的が選択されていることを確認します。

  ![sccm の展開と目的の選択](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)
8. **[この展開のスケジュールを指定します]** でスケジュールを指定します。 詳細については、[パッケージのスケジュール設定](https://technet.microsoft.com/library/gg682178.aspx)に関するページをお読みください。
9. データ センターのニーズに従って、**[配布ポイント]** ページでプロパティを構成し、ウィザードを完了します。

> [!TIP]
> 不要な再起動を避けるためには、月次のメンテナンス期間中またはソフトウェアの更新期間中にパッケージのインストールをスケジュールします。

展開の進行状況は SCCM コンソールを使用して監視できます。そのためには **[監視]** > **[展開]** > *[パッケージ名]* の順に移動します。
  ![sccm の監視](./media/site-recovery-install-mobility-service-using-sccm/report.PNG)

## <a name="deploy-mobility-service-on-computers-running-linux-operating-systems"></a>Linux オペレーティング システムを実行しているコンピューターに Mobility Service を展開する
> [!NOTE]
> この記事では、以下を前提としています。
> 1. 構成サーバーの IP アドレスは 192.168.3.121 である
> 2. セキュリティで保護されたネットワーク ファイル共有は \\\ContosoSecureFS\MobilityServiceInstallers である

### <a name="step-1-prepare-for-deployment"></a>手順 1: デプロイの準備をする
1. ネットワーク共有にフォルダーを作成し、**MobSvcLinux** という名前を付けます。
2. 構成サーバーにログインし、管理者権限のコマンド プロンプトを開きます。
3. パスフレーズ ファイルを生成するには、次のコマンドを実行します。

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
6. MobSvc.passphrase ファイルをネットワーク共有上の MobSvcWindows フォルダーにコピーします。
5. 次のコマンドを実行して構成サーバーでインストーラーのリポジトリを参照します。

  `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`
6. 次のファイルをネットワーク共有上の **MobSvcLinux** フォルダーにコピーします。
  * Microsoft-ASR\_UA\_*バージョン*\_OEL-64\_GA\_*日付*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*バージョン*\_RHEL6-64\_GA\_*日付*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*バージョン*\_RHEL7-64\_GA\_*日付*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*バージョン*\_SLES11-SP3-64\_GA\_*日付*\_Release.tar.gz

7. 以下のコードをコピーして、**install_linux.sh** という名前で **MobSvcLinux** フォルダーに保存します。
> [!NOTE]
> 以下のスクリプトの [CSIP] プレース ホルダーを、お使いの構成サーバーの IP アドレスの実際の値にかならず置き換えてください。

  [!INCLUDE [site-recovery-sccm-linux-script](../../includes/site-recovery-sccm-linux-script.md)]

### <a name="step-2-create-a-package"></a>手順 2: パッケージを作成する

1. System Center Configuration Manager Console にログインします。
2. **[ソフトウェア ライブラリ]** > **[アプリケーション管理]** > **[パッケージ]** を参照します。
3. **[パッケージ]** を右クリックして **[パッケージの作成]** を選択します。
4. 名前、説明、製造元、言語、バージョンの値を指定します。
5. **[このパッケージにソース ファイルを含める]** チェック ボックスをオンします。
6. **[参照]** ボタンをクリックして、インストーラーが格納されているネットワーク共有 (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux) を選択します。

  ![sccm パッケージの作成](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package-linux.png)

7. **[作成するプログラムの種類の選択]** ページで、**[標準プログラム]** を選択して **[次へ]** をクリックします。

  ![sccm パッケージの作成](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)
8. **[この標準プログラムに関する情報の指定]** ページで、次の入力を指定して **[次へ]** をクリックします。 (その他の入力は既定値のまま残すことができます)

  ![sccm パッケージ プロパティ](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-linux.png)   
| **パラメーター名** | **値** |
|--|--|
| 名前 | Microsoft Azure Mobility Service のインストール (Linux) |
| コマンド ライン | ./install_linux.sh |
| プログラムの実行条件 | ユーザーがログオンしているかどうか |

9. 次のページで **[任意のプラットフォームで実行可能]** を選択します。
  ![sccm のパッケージ プロパティ ページ&2;](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2-linux.png)   

10. ウィザードを完了するには、**[次へ]** を&2; 回クリックします。
> [!NOTE]
> スクリプトでは、Mobility Service エージェントの新規インストールとインストール済みエージェントのアップグレードまたは更新の両方をサポートします。

### <a name="step-3-deploy-the-package"></a>手順 3: パッケージを展開する
1. SCCM コンソールで、パッケージを右クリックして **[コンテンツの配布]** を選択します。
  ![sccm パッケージの配布](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)
2. パッケージのコピー先とする **[[配布ポイント]](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** を選択します。
3. ウィザードを完了すると、指定した配布ポイントへのパッケージのレプリケートが開始されます。
4. パッケージの配布が完了したら、パッケージを右クリックして **[展開]** を選択します。
  ![sccm パッケージの展開](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)
5. 展開のターゲット コレクションとして前提条件のセクションで作成した Linux サーバー デバイスのコレクションを選択します。

  ![sccm のターゲット コレクションの選択](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection-linux.png)
6. **[コンテンツの配布先の指定]** ページで**配布ポイント**を選択します。
7. **[このソフトウェアの展開を制御する設定の指定]** ページで、必要に応じて目的が選択されていることを確認します。

  ![sccm の展開と目的の選択](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)
8. **[この展開のスケジュールを指定します]** でスケジュールを指定します。 詳細については、[パッケージのスケジュール設定](https://technet.microsoft.com/library/gg682178.aspx)に関するページをお読みください。
9. データ センターのニーズに従って、**[配布ポイント]** ページでプロパティを構成し、ウィザードを完了します。

構成したスケジュールに従って、Linux サーバーのデバイス コレクションに Mobility Service がインストールされます。

## <a name="other-methods-to-install-mobility-services"></a>Mobility Service をインストールするその他の方法
Mobility Service をインストールする他の方法の詳細についてお読みください。
* [GUI を使用する手動インストール](http://aka.ms/mobsvcmanualinstall)
* [コマンド ラインを使用する手動インストール](http://aka.ms/mobsvcmanualinstallcli)
* [構成サーバーを使用するプッシュ インストール](http://aka.ms/pushinstall)
* [Azure Automation および Desired State Configuration を使用する自動インストール](http://aka.ms/mobsvcdscinstall)

## <a name="next-steps"></a>次のステップ
仮想マシンの[保護を有効にする](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-vmware-to-azure#step-6-replicate-applications)準備をします。



<!--HONumber=Jan17_HO2-->



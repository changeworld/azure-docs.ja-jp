---
title: "VMware から Azure への Azure Site Recovery のトラブルシューティング | Microsoft Docs"
description: "Azure 仮想マシンのレプリケート時に発生するエラーのトラブルシューティングを行います"
services: site-recovery
documentationcenter: 
author: asgang
manager: srinathv
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/24/2017
ms.author: asgang
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: d4d6d273001642ebb8c960333a1c01977e863dbc
ms.contentlocale: ja-jp
ms.lasthandoff: 09/07/2017

---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>モビリティ サービスのプッシュ インストールに関する問題のトラブルシューティング

この記事では、保護を有効にするためにソース サーバーに Azure Site Recovery モビリティ サービスをインストールするときに発生する可能性がある一般的な問題について説明します。

## <a name="error-code-95107-protection-could-not-be-enabled"></a>(エラー コード 95107) 保護を有効にできませんでした
**エラー コード** | **考えられる原因** | **エラー固有の推奨事項**
--- | --- | ---
95107 </br>**メッセージ:** ソース マシンに対するモビリティ サービスのプッシュ インストールが、エラー コード **EP0858** で失敗しました。 <br> モビリティ サービスをインストールするために指定した資格情報が正しくないか、ユーザー アカウントに十分な権限がありません。 | ソース マシンにモビリティ サービスをインストールするために指定したユーザー資格情報が正しくありません。 | 構成サーバー上のソース マシンに対して指定したユーザー資格情報が正しいことを確認します。 <br> ユーザー資格情報を追加/編集するには、構成サーバーに移動して、**[Cspsconfigtool]** > **[アカウントの管理]** の順に選択します。 </br> さらに、次の前提条件を確認して、プッシュ インストールを正常に完了します。

## <a name="error-code-95015-protection-could-not-be-enabled"></a>(エラー コード 95015) 保護を有効にできませんでした
**エラー コード** | **考えられる原因** | **エラー固有の推奨事項**
--- | --- | ---
95105 </br>**メッセージ:** ソース マシンに対するモビリティ サービスのプッシュ インストールが、エラー コード **EP0856** で失敗しました。 <br> **[ファイルとプリンターの共有]** がソース マシンで許可されていないか、プロセス サーバーとソース マシン間のネットワーク接続に問題があります。| **[ファイルとプリンターの共有]** が有効になっていません。 | Windows ファイアウォールでソース マシンの **[ファイルとプリンターの共有]** を許可します。 ソース マシンで、**[Windows ファイアウォール]** > **[ファイアウォールを介したアプリまたは機能を許可する]** の順に移動し、すべてのプロファイルで **[ファイルとプリンターの共有]** を選択します。 </br> さらに、次の前提条件を確認して、プッシュ インストールを正常に完了します。

## <a name="error-code-95117-protection-could-not-be-enabled"></a>(エラー コード 95117) 保護を有効にできませんでした
**エラー コード** | **考えられる原因** | **エラー固有の推奨事項**
--- | --- | ---
95117 </br>**メッセージ:** ソース マシンに対するモビリティ サービスのプッシュ インストールが、エラー コード **EP0865** で失敗しました。 <br> ソース マシンが実行されていないか、プロセス サーバーとソース マシン間のネットワーク接続に問題があります。 | プロセス サーバーとソース サーバー間のネットワーク接続の問題です。 | プロセス サーバーとソース サーバー間の接続を確認します。 </br> さらに、次の前提条件を確認して、プッシュ インストールを正常に完了します。

## <a name="error-code-95103-protection-could-not-be-enabled"></a>(エラー コード 95103) 保護を有効にできませんでした
**エラー コード** | **考えられる原因** | **エラー固有の推奨事項**
--- | --- | ---
95103 </br>**メッセージ:** ソース マシンに対するモビリティ サービスのプッシュ インストールが、エラー コード **EP0854** で失敗しました。 <br> Windows Management Instrumentation (WMI) がソース マシンで許可されていないか、プロセス サーバーとソース マシン間のネットワーク接続に問題があります。| WMI が Windows ファイアウォールでブロックされています。 | Windows ファイアウォールで WMI を許可します。 **[Windows ファイアウォール]** > **[ファイアウォールを介したアプリまたは機能を許可]** の順に選択し、すべてのプロファイルで **[WMI]** を選択します。 </br> さらに、次の前提条件を確認して、プッシュ インストールを正常に完了します。

## <a name="check-push-installation-logs-for-errors"></a>エラーのプッシュ インストール ログのチェック

構成/プロセス サーバーで、<Microsoft Azure Site Recovery Install Location>\home\svsystems\pushinstallsvc\ にある PushinstallService ファイルに移動して、問題の原因を理解します。 以下に示すトラブルシューティングの手順を使って問題を解決します。</br>

![プッシュ インストールのログ](./media/site-recovery-protection-common-errors/pushinstalllogs.png)

## <a name="push-installation-prerequisites-for-windows"></a>Windows のプッシュ インストールの前提条件
### <a name="ensure-that-file-and-printer-sharing-is-enabled"></a>**[ファイルとプリンターの共有]** が有効になっていることを確認する
Windows ファイアウォールのソース マシン上で、**[ファイルとプリンターの共有]** および **[Windows Management Instrumentation]** を許可します。 </br>
#### <a name="if-the-source-machine-is-domain-joined-br"></a>ソース マシンがドメインに参加している場合 </br>
グループ ポリシー管理コンソールを使ってファイアウォール設定を構成します。

1. Azure Active Directory ドメイン コンピューターに管理者としてサインインします。 グループ ポリシー管理コンソールを開きます (GPMC.MSC を **[スタート]** > **[ファイル名を指定して実行]** から実行)。</br>

2. グループ ポリシー管理コンソールがインストールされていない場合は、「[GPMC をインストールする](https://technet.microsoft.com/library/cc725932.aspx)」をご覧ください。 </br>

3. グループ ポリシー管理コンソールのツリーで、フォレストおよびドメインの **[グループ ポリシー オブジェクト]** をダブルクリックします。 **[既定のドメイン ポリシー]** に移動します。 </br>

    ![既定のドメイン ポリシー](./media/site-recovery-protection-common-errors/gpmc1.png) </br>
</br>
4. **[既定のドメイン ポリシー]** を右クリックし、 > **[編集]** をクリックします。 新しい**グループ ポリシー管理エディター** ウィンドウが開きます。 </br>

    ![グループ ポリシー管理エディター](./media/site-recovery-protection-common-errors/gpmc2.png) </br>
</br>
5. **グループ ポリシー管理エディター**で、**[コンピューターの構成]** > **[ポリシー]** > **[管理用テンプレート]** > **[ネットワーク]** > **[ネットワーク接続]** > **[Windows ファイアウォール]** の順に移動します。 </br>

    ![Windows ファイアウォール](./media/site-recovery-protection-common-errors/gpmc3.png) </br>
</br>
6. **[ドメイン プロファイル]** と **[標準プロファイル]** の次の設定を有効にします。 </br>

    a. **[Windows ファイアウォール: 着信ファイルとプリンターの共有の例外を許可する]** をダブルクリックします。 **[有効]** を選択し、**[OK]** を選択します。 </br>

    b. **[Windows ファイアウォール: 着信リモート管理の例外を許可する]** をダブルクリックします。 **[有効]** を選択し、**[OK]** を選択します。 </br>

    ![ドメイン プロファイル](./media/site-recovery-protection-common-errors/gpmc4.png) </br>
</br>

#### <a name="if-the-source-machine-isnt-domain-joined-and-part-of-a-workgroup-br"></a>ソース マシンがドメインに参加せずワークグループの一部ではない場合 </br>
リモート マシン上でファイアウォール設定を次のように構成します (ワークグループに対応)。

1. ソース マシンに移動します。</br>

2. **[Windows ファイアウォール]** > **[ファイアウォールを介したアプリまたは機能を許可]** の順に移動し、すべてのプロファイルで **[ファイルとプリンターの共有]** を選択します。 </br>

3. **[Windows ファイアウォール]** > **[ファイアウォールを介したアプリまたは機能を許可]** の順に選択し、すべてのプロファイルで **[WMI]** を選択します。 </br>

#### <a name="disable-remote-user-account-control"></a>リモート ユーザー アカウント制御を無効にする
モビリティ サービスをプッシュするには、レジストリ キーを使ってユーザー アカウント制御を無効にします。

1. **[スタート]** > **[ファイル名を指定して実行]** を選択し、「*regedit*」と入力して、**Enter** キーを押します。

2. レジストリ サブキー "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" を見つけて選択します。

3. LocalAccountTokenFilterPolicy レジストリ エントリが存在しない場合は、以降の手順に従います。

    a. **[編集]** メニューの **[新規]** で **[DWORD 値]** を選択します。

    b. 「*LocalAccountTokenFilterPolicy*」と入力して、**Enter** キーを押します。

    c. **LocalAccountTokenFilterPolicy** を右クリックして、**[変更]** を選択します。

    d. **[値]** データ ボックスに「*1*」と入力し、**[OK]** を選択します。

4. レジストリ エディターを閉じます。


## <a name="push-installation-prerequisites-for-linux"></a>Linux でのプッシュ インストールの前提条件

1. ソース Linux サーバーにルート ユーザーを作成します  (このアカウントは、プッシュ インストールと更新にのみ使用します)。</br>

2. ソース Linux サーバーの /etc/hosts ファイルに、ローカル ホスト名を、すべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが存在することを確認します。 </br>

3. 最新の openssh、openssh-server、および openssl パッケージがソース Linux サーバーにインストールされていることを確認します。 ssh ポート 22 が有効であり、実行中になっていることを確認します。 </br>

4. エージェントの古いエントリがソース サーバー上に既に存在するかどうかを確認し、古いエージェントをアンインストールし、サーバーを再起動します。 エージェントを再インストールします。 </br>

#### <a name="enable-sftp-subsystem-and-password-authentication-in-the-sshdconfig-file"></a>sshd_config ファイルで SFTP サブシステムとパスワード認証を有効にする
1. ソース サーバーに root としてサインインします。 </br>

2. /etc/ssh/sshd_config ファイルで、"PasswordAuthentication" で始まる行を見つけます。 </br>

3. この行のコメントを解除し、値 "no" を "yes" に変更します。 </br>

    ![PasswordAuthentication](./media/site-recovery-protection-common-errors/linux1.png)

4. "Subsystem" で始まる行を見つけ、その行のコメントを解除します。 </br>

    ![Subsystem](./media/site-recovery-protection-common-errors/linux2.png)

5. 変更を保存し、sshd サービスを再起動します。 </br>


## <a name="push-installation-checks-on-the-configurationprocess-server"></a>構成/プロセス サーバーでのプッシュ インストールのチェック
#### <a name="validate-credentials-for-discovery-and-installation"></a>以下の手順に従って、検出とインストールのための資格情報を検証します。

1. 構成サーバーから、Cspsconfigtool を開始します。</br>

    ![WMItestConnect5](./media/site-recovery-protection-common-errors/wmitestconnect5.png) </br>

2. 保護に使用されるアカウントに、ソース マシンの管理者権限があることを確認します。 </br>

#### <a name="check-connectivity-between-the-process-server-and-the-source-server"></a>プロセス サーバーとソース サーバー間の接続を確認する
1. プロセス サーバーがインターネットに接続していることを確認します。

2. wbemtest.exe を使って WMI 接続を検証します。 </br>

    a. プロセス サーバーで、**[スタート]** > **[ファイル名を指定して実行]** を選択し、 > 「**wbemtest.exe**」と入力します。 次のような **[Windows Management Instrumentation テスト]** ウィンドウが開きます。</br>

      ![WMI テスト](./media/site-recovery-protection-common-errors/wmitestconnect1.png) </br>
   </br>
    b. **[接続]** を選択し、ソース サーバーの IP アドレスを **[名前空間]** に入力します。

    c. **[ユーザー]** 名と **[パスワード]** を入力します  (ソース マシンがドメインに参加している場合は、*domainName\username* としてユーザー名と共にドメイン名を指定します。 ソース マシンがワークグループ内にある場合は、ユーザー名のみを指定します)。

    d. **[認証レベル]** で **[パケットのプライバシー]** を選択します。 </br>

      ![パケットのプライバシー](./media/site-recovery-protection-common-errors/wmitestconnect2.png) </br>
   </br>
    e. **[接続]**を選択します。 これで、WMI 接続に指定されたデータが表示され、**[Windows Management Instrumentation テスト]** ウィンドウには次のように表示されます。 </br>

      ![WMI テスト データ](./media/site-recovery-protection-common-errors/wmitestconnect3.png) </br>
   </br>
      WMI 接続が成功しない場合、エラー メッセージが表示されます。 次のスクリーンショットは、Windows ファイアウォールによって許可されたアプリで **WMI/リモート管理**が有効になっていない場合に、試行が失敗に終わっている状態を示しています。 </br>

    ![WMI テスト エラー メッセージ](./media/site-recovery-protection-common-errors/wmitestconnect4.png) </br>
   </br>

3. WMI の状態と接続を確認します。</br>

    a. 構成/プロセス サーバーで、**[スタート]** > **[ファイル名を指定して実行]** を選択し、 > 「**wmimgmt.msc**」と入力し、 > **[操作]** > **[他の操作]** > **[Connect to another computer (source machine)]\(別のコンピュータに接続 (ソース マシン)\)** を選択します。 </br>

    b. 保護に使われるアカウントの資格情報を入力し、接続に問題がないかを確認します。 </br>

#### <a name="verify-that-network-shared-folders-of-the-source-machine-are-accessible-from-the-process-server-remotely-by-using-specified-credentials"></a>指定された資格情報を使って、プロセス サーバーからソース マシンのネットワーク共有フォルダーにリモートでアクセスできることを確認します。

  1. プロセス サーバー マシンにサインインし、エクスプローラーを開きます。 アドレス バーに「*\\\source-machine-ip\C$*」と入力します。 **[Enter]** を選択します。 </br>

      ![エクスプローラー](./media/site-recovery-protection-common-errors/fileshare1.png) </br>

  2. 資格情報の入力を求められます。 ユーザー名とパスワードを入力し、**[OK]** を選択します。</br>

      * ソース マシンがドメインに参加している場合は、*domainName\username* としてユーザー名と共にドメイン名を入力します。</br>

      * ソース マシンがワークグループ内にある場合は、ユーザー名のみを入力します。 </br>

      ![資格情報ダイアログ ボックス](./media/site-recovery-protection-common-errors/fileshare2.png) </br>

  3. 接続に成功した場合は、プロセス サーバーからソース マシンのフォルダーをリモートで表示できます。 </br>

      ![フォルダー ビュー](./media/site-recovery-protection-common-errors/fileshare3.png) </br>

> [!NOTE] 
> 接続に失敗した場合は、すべての前提条件を満たしているかどうか確認します。
>

**Windows Management Instrumentation** を開きたくない場合は、ソース マシンにモビリティ サービスを手動でインストールすることもできます。</br> 

詳しくは、「[GUI を使用して、モビリティ サービスを手動でインストールする](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui)」および[構成マネージャーを使ったインストールのガイダンスに関する記事](site-recovery-install-mobility-service-using-sccm.md)をご覧ください。 </br>

## <a name="next-steps"></a>次のステップ
- [VMware 仮想マシンのレプリケーションを有効にする](vmware-walkthrough-enable-replication.md)


---
title: ディザスター リカバリーのためのレプリケーションを有効にする際のモビリティ サービスのプッシュ インストールに関する問題のトラブルシューティング | Microsoft Docs
description: ディザスター リカバリーのためのレプリケーションを有効にする際のモビリティ サービスのインストールに関するエラーのトラブルシューティング
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 12/12/2018
ms.openlocfilehash: 748f4e56b4b7fa52928f8f6507960ec35b5fe6e5
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314399"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>モビリティ サービスのプッシュ インストールに関する問題のトラブルシューティング

モビリティ サービスのインストールは、レプリケーションを有効にする際の重要な手順です。 前提条件を満たしていて、サポート対象の構成を使用していれば、この手順は成功します。 モビリティ サービスのインストール時に直面する最も一般的な問題の原因は次のとおりです。

* 資格情報/特権のエラー
* ログイン エラー
* 接続エラー
* サポート対象外のオペレーティング システム
* VSS インストール エラー

レプリケーションを有効にすると、Azure Site Recovery はモビリティ サービス エージェントを仮想マシンにプッシュ インストールしようとします。 その一環として、構成サーバーは仮想マシンとの接続およびエージェントのコピーを試行します。 インストールを成功させるには、以下に示す段階的なトラブルシューティング ガイダンスに従ってください。

## <a name="credentials-check-errorid-95107--95108"></a>資格情報チェック (ErrorID: 95107 および 95108)

* レプリケーションを有効にする際に選択したユーザー アカウントが**有効かつ正確**なものであることを確認します。
* Azure Site Recovery には、プッシュ インストールを実行するための **ROOT** アカウント、または**管理者特権**を持つユーザー アカウントが必要です。 それ以外の場合、プッシュ インストールはソース マシン上でブロックされます。
  * Windows (**エラー 95107**) の場合は、ソース マシンに対する管理用のアクセス権 (ローカルまたはドメイン) がユーザー アカウントにあることを確認します。
  * ドメイン アカウントを使用していない場合、ローカル コンピューターでリモート ユーザー アクセス制御を無効にする必要があります。
    * リモート ユーザー アクセス制御を無効にするには、HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System レジストリ キーに新しい DWORD である LocalAccountTokenFilterPolicy を追加します。 値を 1 に設定します。 この手順を実行するには、コマンド プロンプトで次のコマンドを実行します。

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Linux (**エラー 95108**) でモビリティ エージェントのインストールを成功させるには、ルート アカウントを選択する必要があります。 さらに、SFTP サービスが実行中である必要があります。 sshd_config ファイルで SFTP サブシステムとパスワード認証を有効にするには、次の手順を実行します。
    1. root としてサインインします。
    2. /etc/ssh/sshd_config ファイルに移動し、PasswordAuthentication で始まる行を見つけます。
    3. この行のコメントを解除し、値を yes に変更します。
    4. Subsystem で始まる行を見つけ、その行のコメントを解除します。
    5. sshd サービスを再起動します。

選択したユーザー アカウントの資格情報を変更する場合は、[ここ](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)に示す手順に従ってください。

## <a name="insufficient-privileges-failure-errorid-95517"></a>特権不足エラー (ErrorID: 95517)

モビリティ エージェントのインストールを選択したユーザーに管理者特権がない場合、ソース マシンへのモビリティ エージェント ソフトウェアのコピーは、構成サーバー/スケールアウト プロセス サーバーによって許可されません。 そのため、このエラーはアクセス拒否エラーの結果です。 ユーザー アカウントに管理者特権があることを確認します。

選択したユーザー アカウントの資格情報を変更する場合は、[ここ](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)に示す手順に従ってください。

## <a name="insufficient-privileges-failure-errorid-95518"></a>特権不足エラー (ErrorID: 95518)

ソース マシンへのログインを試行しているときに、プライマリ ドメインとワークステーション間でドメインの信頼関係を確立できなかった場合、モビリティ エージェントのインストールは、エラー ID 95518 で失敗します。 そのため、モビリティ エージェントをインストールするために使用されるユーザー アカウントに、ソース マシンのプライマリ ドメイン経由でログインするための管理者特権があることを確認します。

選択したユーザー アカウントの資格情報を変更する場合は、[ここ](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)に示す手順に従ってください。

## <a name="login-failure-errorid-95519"></a>ログイン エラー (ErrorID: 95519)

レプリケーションを有効にする際に選択したユーザー アカウントが無効になっています。 ユーザー アカウントを有効にするには、[こちらの記事](https://aka.ms/enable_login_user)を参照するか、次のコマンドのテキスト *username* を実際のユーザー名に置き換えて実行してください。
`net user 'username' /active:yes`

## <a name="login-failure-errorid-95520"></a>ログイン エラー (ErrorID: 95520)

コンピューターにアクセスするための再試行が複数回失敗すると、ユーザー アカウントがロックされます。 このエラーは以下が原因である可能性があります。

* 構成のセットアップ時に指定された資格情報が正しくない、または
* レプリケーションを有効にする際に選択したユーザー アカウントが間違っている

そのため、選択されている資格情報を[こちら](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)の手順に従って変更し、しばらくしてから操作を再試行してください。

## <a name="login-failure-errorid-95521"></a>ログイン エラー (ErrorID: 95521)

このエラーは、ソース マシン上でログオン サーバーを利用できない場合に発生します。 ログオン サーバーを利用できないと、ログイン要求エラーが発生します。その結果、モビリティ エージェントをインストールできません。 ログインを成功させるには、ソース マシン上でログオン サーバーが利用可能であり、ログオン サービスが開始されていることを確認します。 詳細な手順については、[こちら](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available)をクリックしてください。

## <a name="login-failure-errorid-95522"></a>ログイン エラー (ErrorID: 95522)

ソース マシン上でログイン サービスが実行されていないため、ログイン要求エラーが発生しています。 そのため、モビリティ エージェントをインストールできません。 問題を解決してログインを成功させるには、ソース マシン上でログオン サービスが実行されていることを確認します。 ログオン サービスを開始するには、コマンド プロンプトから "net start Logon" コマンドを実行するか、タスク マネージャーから "NetLogon" サービスを開始します。

## <a name="connectivity-failure-errorid-95117--97118"></a>**接続エラー (ErrorID: 95117 および 97118)**

構成サーバー/スケールアウト プロセス サーバーは、モビリティ エージェントをインストールするためにソース VM への接続を試みます。 このエラーは、ネットワーク接続の問題が原因でソース マシンに到達できない場合に発生します。 解決するには、以下を行います。

* 構成サーバーからソース マシンに ping を実行できることを確認します。 レプリケーションを有効にする際にスケールアウト プロセス サーバーを選択した場合は、プロセス サーバーからソース マシンに ping を実行できることを確認します。
  * ソース サーバー マシンのコマンド ラインから、Telnet を使用して、次のように https ポート (135) を指定して構成サーバー/スケールアウト プロセス サーバーを ping し、ネットワーク接続の問題であるか、ファイアウォールのポート ブロックの問題であるかを確認します。

     `telnet <CS/ scale-out PS IP address> <135>`
* また、**Linux VM** の場合は次のことを確認します。
  * 最新の openssh、openssh-server、および openssl パッケージがインストールされているかどうかを確認します。
  * SSH (Secure Shell) が有効になっており、ポート 22 で実行中であることを確認します。
  * SFTP サービスが実行中である必要があります。 sshd_config ファイルで SFTP サブシステムとパスワード認証を有効にするには、次の手順を実行します。
    * root としてサインインします。
    * /etc/ssh/sshd_config ファイルに移動し、PasswordAuthentication で始まる行を見つけます。
    * この行のコメントを解除し、値を yes に変更します。
    * Subsystem で始まる行を見つけ、その行のコメントを解除します。
    * sshd サービスを再起動します。
* 一定の期間が経過した後に適切な応答がない場合は、接続の試行が失敗した可能性があります。または、接続されたホストが応答しなかったため接続を確立できませんでした。
* 接続/ネットワーク/ドメインに関連する問題である可能性があります。 また、DNS 名解決の問題または TCP ポート消費の問題が原因である可能性もあります。 ドメインにそのような既知の問題があるかどうかを確認してください。

## <a name="connectivity-failure-errorid-95523"></a>接続エラー (ErrorID: 95523)

このエラーは、その中にソース マシンが存在するネットワークが見つからない、削除されている、またはもう利用できない場合に発生します。 このエラーを解決する唯一の方法は、そのネットワークが存在することを確認することです。

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>ファイルとプリンターの共有サービス チェック (ErrorID: 95105 および 95106)

接続チェックが完了したら、ファイルとプリンターの共有サービスが仮想マシンで有効になっているかどうかを確認します。 ソース マシン上にモビリティ エージェントをコピーするには、これらの設定が必要です。

**Windows 2008 R2 およびそれ以前のバージョン**の場合は、次の手順を実行します。

* Windows ファイアウォールを介してファイルとプリンターの共有を有効にする
  * コントロール パネルを開きます。[システムとセキュリティ]、[Windows ファイアウォール] の順に選択し、左側のウィンドウで [詳細設定] をクリックし、コンソール ツリーの [受信の規則] をクリックします。
  * [ファイルとプリンターの共有 (NB セッション受信)] 規則と [ファイルとプリンターの共有 (SMB 受信)] 規則を探します。 各規則を右クリックして、**[規則の有効化]** をクリックします。
* グループ ポリシーを使用してファイル共有を有効にする
  * [スタート] メニューに移動し、「gpmc.msc」と入力して検索します。
  * ナビゲーション ウィンドウで、[ローカル コンピューター ポリシー]、[ユーザーの構成]、[管理用テンプレート]、[Windows コンポーネント]、および [ネットワーク共有] の各フォルダーを順に開きます。
  * 詳細ウィンドウで、**[ユーザーがプロファイル内のファイルを共有できないようにします]** をダブルクリックします。 グループ ポリシー設定を無効にして、ユーザーがファイルを共有できるようにするには、[無効] をクリックします。 [OK] をクリックして変更を保存します。 詳細については、 [ここ](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))をクリックしてください。

**より新しいバージョン**の場合は、[ここ](vmware-azure-install-mobility-service.md)に記載されている手順に従ってファイルとプリンターの共有を有効にします。

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Windows Management Instrumentation (WMI) の構成チェック (エラー コード:95103)

ファイルとプリンターのサービスのチェックが完了したら、ファイアウォール経由のプライベート、パブリック、およびドメイン プロファイル用の WMI サービスを有効にします。 ソース マシン上でリモート実行を完了するには、これらの設定が必要です。 有効にするには、

* コントロール パネルに移動し、[セキュリティ] をクリックし、[Windows ファイアウォール] をクリックします。
* [設定の変更] をクリックし、[例外] タブをクリックします。
* [例外] ウィンドウで、Windows Management Instrumentation (WMI) のチェック ボックスをオンにして、ファイアウォール経由の WMI トラフィックを有効にします。 

また、コマンド プロンプトで、ファイアウォール経由の WMI トラフィックを有効にすることもできます。 次のコマンドを使用します。`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
WMI のトラブルシューティングに関するその他の記事を次に示します。

* [基本的な WMI テスト](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI のトラブルシューティング](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [WMI スクリプトと WMI サービスに関する問題のトラブルシューティング](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>サポート対象外のオペレーティング システム

問題が発生するもう 1 つの最も一般的な理由は、サポート対象外のオペレーティング システムです。 モビリティ サービスのインストールを成功させるには、サポート対象のオペレーティング システム/カーネルのバージョンを使用してください。

Azure Site Recovery でサポートされているオペレーティング システムについては、[サポート マトリックス ドキュメント](vmware-physical-azure-support-matrix.md#replicated-machines)を参照してください。

## <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>ブートおよびシステムのパーティション/ボリュームが同じディスクではありません (ErrorID: 95309)

9.20 より前のバージョンでは、異なるディスク上のブートおよびシステムのパーティション/ボリュームは、サポートされる構成ではありませんでした。 [9.20 以降のバージョン](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)から、この構成がサポートされています。 このサポートを得るために、最新バージョンを使用してください。

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>複数のディスク上のシステム パーティション (ErrorID: 95313)

9.20 より前のバージョンでは、複数のディスク上に存在するルート パーティションまたはボリュームは、サポートされる構成ではありませんでした。 [9.20 以降のバージョン](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)から、この構成がサポートされています。 このサポートを得るために、最新バージョンを使用してください。

## <a name="lvm-support-from-920-version"></a>9.20 以降のバージョンでの LVM のサポート

9.20 より前のバージョンでは、データ ディスク用の LVM のみがサポートされていました。 /boot はディスク パーティション上にあり、LVM ボリュームではないことが必要です。

[9.20 以降のバージョン](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)から、[LVM 上の OS ディスク](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)がサポートされています。 このサポートを得るために、最新バージョンを使用してください。

## <a name="insufficient-space-errorid-95524"></a>空き領域不足 (ErrorID: 95524)

ソース マシン上にモビリティ エージェントをコピーする場合は、少なくとも 100 MB の空き領域が必要です。 そのため、ソース マシン上に必要な空き領域があることを確認し、操作を再試行してください。

## <a name="vss-installation-failures"></a>VSS インストール エラー

VSS のインストールは、モビリティ エージェントのインストールの一部です。 このサービスは、アプリケーション整合性復旧ポイントを生成するプロセスで使用されます。 VSS のインストール中のエラーは、複数の理由で発生することがあります。 正確なエラーを特定するには、**c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log** を参照します。 次のセクションでは、いくつかの一般的なエラーと解決手順を示します。

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS エラー -2147023170 [0x800706BE] - 終了コード 511

この問題は、ウイルス対策ソフトウェアによって Azure Site Recovery サービスの操作がブロックされているときに、もっともよく発生します。 これを解決するには、次のようにします。

1. [こちら](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)で示されているすべてのフォルダーを除外します。
2. ウイルス対策プロバイダーによって公開されているガイドラインに従って、Windows で DLL の登録をブロック解除します。

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS エラー 7 [0x7] - 終了コード 511

これはランタイム エラーであり、VSS をインストールするのに十分なメモリがないことが原因です。 この操作が正常に完了するためには、ディスク領域を増やします。

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS エラー -2147023824 [0x80070430] - 終了コード 517

このエラーは、Azure Site Recovery VSS プロバイダー サービスが[削除対象としてマークされている](https://msdn.microsoft.com/en-us/library/ms838153.aspx)と発生します。 次のコマンド ラインを実行し、ソース マシンに VSS を手動でインストールしてみてください

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS エラー -2147023841 [0x8007041F] - 終了コード 512

このエラーは、Azure Site Recovery VSS プロバイダー サービスのデータベースが[ロックされている](https://msdn.microsoft.com/en-us/library/ms833798.aspx)場合に発生します。次のコマンド ラインを実行し、ソース マシンに VSS を手動でインストールしてみてください

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-exit-code-806"></a>VSS 終了コード 806

このエラーは、インストールに使用されているユーザー アカウントに CSScript コマンドを実行するアクセス許可がない場合に発生します。 スクリプトを実行するユーザー アカウントに必要なアクセス許可を提供し、操作を再試行してください。

### <a name="other-vss-errors"></a>その他の VSS エラー

次のコマンド ラインを実行し、ソース マシンに VSS プロバイダー サービスを手動でインストールしてみてください

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="next-steps"></a>次の手順

VMware VM のディザスター リカバリーを設定する[方法を確認する](vmware-azure-tutorial.md)

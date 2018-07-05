---
title: Azure 仮想マシンのシリアル コンソール | Microsoft Docs
description: Azure 仮想マシンの双方向シリアル コンソール。
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 8e9e98a0c9f1db805d62cdee038fb4754ff047e2
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2018
ms.locfileid: "36960442"
---
# <a name="virtual-machine-serial-console-preview"></a>仮想マシンのシリアル コンソール (プレビュー) 


Azure の仮想マシンのシリアル コンソールでは、Linux および Windows 仮想マシンのテキスト ベースのコンソールにアクセスできます。 このシリアル接続は、仮想マシンの COM1 シリアル ポートに接続し、仮想マシンへのアクセスを提供します。仮想マシンのネットワーク/オペレーティング システムの状態とは無関係です。 現在、仮想マシンのシリアル コンソールへのアクセスは Azure Portal からのみ可能であり、仮想マシンへの VM 共同作成者以上のアクセス権を持つユーザーだけが使用できます。 

> [!Note] 
> プレビューは、使用条件に同意することを条件に使用することができます。 詳細については、「[Microsoft Azure プレビューの追加使用条件] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。現在、このサービスは**パブリック プレビュー**段階にあり、仮想マシンのシリアル コンソールへのアクセスは全世界の Azure リージョンで提供されています。 現時点では、シリアル コンソールは、Azure Government、Azure Germany、Azure China Cloud では利用できません。

 

## <a name="prerequisites"></a>前提条件 

* リソース管理デプロイ モデルを使用している必要があります。 クラシック デプロイはサポートされていません。 
* 仮想マシンの[ブート診断](boot-diagnostics.md)が有効になっている必要があります。 
* シリアル コンソールを使用するアカウントには、VM の[共同作成者ロール](../../role-based-access-control/built-in-roles.md)と[ブート診断](boot-diagnostics.md)ストレージ アカウントが必要です。 

## <a name="open-the-serial-console"></a>シリアル コンソールを開く
仮想マシンのシリアル コンソールには、[Azure Portal](https://portal.azure.com) からのみアクセスできます。 ポータルを使用して仮想マシンのシリアル コンソールにアクセスする手順を次に示します。 

  1. Azure ポータルを開きます
  2. 左側のメニューで [Virtual Machines] を選択します。
  3. 一覧で VM をクリックします。 VM の概要ページが開きます。
  4. 下へスクロールして [サポート + トラブルシューティング] セクションを表示し、[シリアル コンソール (プレビュー)] をクリックします。 シリアル コンソールで新しいウィンドウが開き、接続が開始されます。

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

### <a name="disable-feature"></a>機能を無効にする
特定の VM のブート診断設定を無効にすることで、その VM のシリアル コンソール機能を非アクティブ化できます。

## <a name="serial-console-security"></a>シリアル コンソールのセキュリティ 

### <a name="access-security"></a>アクセス セキュリティ 
シリアル コンソールへのアクセスは、仮想マシンへの [VM 共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)以上のアクセス権を持つユーザーに制限されます。 シリアル コンソールには [Azure Portal](https://portal.azure.com) からアクセスするため、AAD テナントに Multi-Factor Authentication が必要な場合は、シリアル コンソールへのアクセスにも MFA が必要になります。

### <a name="channel-security"></a>チャネル セキュリティ
やり取りされるすべてのデータがネットワーク上で暗号化されます。

### <a name="audit-logs"></a>監査ログ
現在、シリアル コンソールへのすべてのアクセスが、仮想マシンの[ブート診断](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics)ログに記録されます。 これらのログへのアクセスは、Azure 仮想マシン管理者が所有し、制御します。  

>[!CAUTION] 
コンソールのアクセス パスワードはログに記録されませんが、コンソール内で実行されるコマンドにパスワード、シークレット、ユーザー名、またはその他の形式の個人を特定できる情報 (PII) が含まれていたり、出力されたりした場合、それらの情報は、シリアル コンソールのスクロールバック機能の実装の一部として、表示される他のすべてのテキストと共に仮想マシンのブート診断ログに書き込まれます。 これらのログは循環型であり、診断ストレージ アカウントに対する読み取りアクセス許可を持つユーザーだけがアクセスできます。ただし、シークレットや PII が含まれている可能性のあるものにはリモート デスクトップを使用するというベスト プラクティスに従うことをお勧めします。 

### <a name="concurrent-usage"></a>同時使用
ユーザーがシリアル コンソールに接続しているときに、別のユーザーがその同じ仮想マシンへのアクセスを要求し、その要求が成功した場合、最初のユーザーが立ち上がって物理コンソールから離れ、新しいユーザーがそこに座るのと同様に、最初のユーザーが切断され、2 番目のユーザーが接続されます。

>[!CAUTION] 
これは、切断されたユーザーはログアウトされないことを意味します。 (SIGHUP または同様のメカニズムを使用して) 切断時に強制的にログアウトする機能は、まだロードマップにあります。 Windows では SAC で自動タイムアウトが有効になっていますが、Linux ではターミナルのタイムアウト設定を構成できます。 


## <a name="accessing-serial-console-for-windows"></a>Windows 用シリアル コンソールへのアクセス 
Azure の新しい Windows Server イメージでは、既定で [Special Administrative Console](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) が有効になります。 SAC は Windows のサーバー バージョンではサポートされていますが、クライアント バージョン (Windows 10、Windows 8、Windows 7 など) では使用できません。 Feb2018 以下のイメージを使用して作成した Windows 仮想マシンのシリアル コンソールを有効にするには、次の手順を実行します。 

1. リモート デスクトップを介して Windows 仮想マシンに接続します。
2. 管理コマンド プロンプトで次のコマンドを実行します。 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. システムを再起動して、SAC コンソールを有効にします。

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

必要に応じて、SAC をオフラインでも有効にすることができます。 

1. SAC を構成する Windows ディスクをデータ ディスクとして既存の VM に接続します。 
2. 管理コマンド プロンプトで次のコマンドを実行します。 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>SAC が有効になっているかどうかを知る方法 

[SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) が有効になっていない場合、シリアル コンソールに SAC プロンプトは表示されません。 VM の正常性情報が表示される場合もありますが、それ以外は空白になります。  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>シリアル コンソールに表示するブート メニューの有効化 

Windows ブート ローダーのプロンプトを有効にしてシリアル コンソールに表示する必要がある場合は、Windows ブート ローダーに次のオプションを追加できます。

1. リモート デスクトップを介して Windows 仮想マシンに接続します。
2. 管理コマンド プロンプトで次のコマンドを実行します。 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. システムを再起動して、ブート メニューを有効にします。

> [!NOTE] 
> 現時点では、ファンクション キーのサポートは有効になっていません。高度なブート オプションが必要な場合は、bcdedit /set {current} onetimeadvancedoptions on を使用します。詳細については、[bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) に関する記事をご覧ください。

## <a name="windows-commands---cmd"></a>Windows コマンド - CMD 

このセクションには、VM にアクセスするために SAC の使用が必要になる可能性があるシナリオにおいて、一般的なタスクを実行するためのコマンド例が含まれています。たとえば、RDP 接続のエラーをトラブルシューティングする必要がある場合などです。

SAC は Windows Server 2003 以降のすべてのバージョンの Windows に含まれていますが、既定では無効になっています。 SAC は、`sacdrv.sys` カーネル ドライバー、`Special Administration Console Helper` サービス (`sacsvr`)、`sacsess.exe` プロセスに依存しています。 詳しくは、[緊急管理サービスのツールと設定](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10))に関する記事をご覧ください。

SAC を使用すると、シリアル ポート経由で実行中の OS に接続できます。 SAC から CMD を起動すると、実行中の OS 内で、`sacsess.exe` によって `cmd.exe` が起動されます。 シリアル コンソール機能を使用して SAC に接続するのと同時に、VM に RDP で接続すると、タスク マネージャーでこのことを確認できます。 SAC 経由でアクセスする CMD は、RDP 接続の際に使用する `cmd.exe` と同じものです。 まったく同じコマンドとツールが利用可能です。CMD インスタンスから PowerShell を起動することもできます。 SAC と Windows 回復環境 (WinRE) の主な違いは、SAC では実行中の 0S を管理できるのに対し、WinRE では別の最小限の OS が起動されるということです。 Azure VM では WinRE へのアクセスはサポートされていませんが、シリアル コンソール機能を使用すると、SAC 経由で Azure VM を管理できます。

SAC は 80 x 24 のスクリーン バッファーに制限されるうえ、スクロール バックもできないため、コマンドに `| more` を追加して、一度に 1 ページずつ出力を表示します。 `<spacebar>` で次のページを表示し、`<enter>` で次の行を表示します。  

`SHIFT+INSERT` は、シリアル コンソール ウィンドウ用の貼り付けのショートカットです。

SAC はスクリーン バッファーが制限されているため、長いコマンドを入力する場合は、ローカルのテキスト エディターに入力してから SAC に貼り付けた方が簡単な場合があります。

### <a name="view-and-edit-windows-registry-settings"></a>Windows レジストリ設定の表示と編集
#### <a name="verify-rdp-is-enabled"></a>RDP が有効になっていることを確認する
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

2 番目のキー (\Policies 下) が存在するのは、関連するグループ ポリシー設定を構成する場合のみです。

#### <a name="enable-rdp"></a>RDP を有効にする
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

2 番目のキー (\Policies 下) が必要になるのは、関連するグループ ポリシー設定が構成されている場合のみです。 これをグループ ポリシーで構成する場合、値が書き換えられるのは次にグループ ポリシーを更新するときです。

### <a name="manage-windows-services"></a>Windows サービスの管理

#### <a name="view-service-state"></a>サービスの状態の表示
`sc query termservice`
####  <a name="view-service-logon-account"></a>サービスのログオン アカウントの表示
`sc qc termservice`
#### <a name="set-service-logon-account"></a>サービスのログオン アカウントの設定 
`sc config termservice obj= "NT Authority\NetworkService"`

等号の後にはスペースが必要です。
#### <a name="set-service-start-type"></a>サービス開始の種類の設定
`sc config termservice start= demand` 

等号の後にはスペースが必要です。 開始の値には、`boot`、`system`、`auto`、`demand`、`disabled`、`delayed-auto` などを使用できます。
#### <a name="set-service-dependencies"></a>サービスの依存関係の設定
`sc config termservice depend= RPCSS`

等号の後にはスペースが必要です。
#### <a name="start-service"></a>サービスの開始
`net start termservice`

or

`sc start termservice`
#### <a name="stop-service"></a>サービスの停止
`net stop termservice`

or

`sc stop termservice`
### <a name="manage-networking-features"></a>ネットワーク機能の管理
#### <a name="show-nic-properties"></a>NIC プロパティの表示
`netsh interface show interface` 
#### <a name="show-ip-properties"></a>IP プロパティの表示
`netsh interface ip show config`
#### <a name="show-ipsec-configuration"></a>IPSec 構成の表示
`netsh nap client show configuration`  
#### <a name="enable-nic"></a>NIC の有効化
`netsh interface set interface name="<interface name>" admin=enabled`
#### <a name="set-nic-to-use-dhcp"></a>DHCP を使用するように NIC を設定する
`netsh interface ip set address name="<interface name>" source=dhcp`

IP アドレスを取得するために DHCP を使用するように、ゲスト OS で Azure VM を常に構成する必要があります。 Azure の静的 IP 設定では、VM に静的 IP を指定するために引き続き DHCP が使用されます。
#### <a name="ping"></a>ping
`ping 8.8.8.8` 
#### <a name="port-ping"></a>ポートの ping  
Telnet クライアントのインストール

`dism /online /Enable-Feature /FeatureName:TelnetClient`

接続をテストする

`telnet bing.com 80`

Telnet クライアントの削除

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Windows で利用できる既定の方法に制限されている場合は、PowerShell を使用してポートの接続をテストする方がよい場合があります。 例については、以下の PowerShell に関するセクションをご覧ください。
#### <a name="test-dns-name-resolution"></a>DNS 名前解決のテスト
`nslookup bing.com`
#### <a name="show-windows-firewall-rule"></a>Windows ファイアウォール規則の表示
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
#### <a name="disable-windows-firewall"></a>Windows ファイアウォールの無効化
`netsh advfirewall set allprofiles state off`

トラブルシューティング時にこのコマンドを使用して、Windows ファイアウォールを一時的に無効にすることができます。 ファイアウォールは、次の再起動時か、以下のコマンドを使用した場合に有効になります。 Windows ファイアウォールを無効にするために Windows ファイアウォール サービス (MPSSVC) を停止したり、ベース フィルター エンジン (BFE) サービスを停止したりしないでください。 MPSSVC や BFE を停止すると、すべての接続がブロックされます。
#### <a name="enable-windows-firewall"></a>Windows ファイアウォールの有効化
`netsh advfirewall set allprofiles state on`
### <a name="manage-users-and-groups"></a>ユーザーとグループの管理
#### <a name="create-local-user-account"></a>ローカル ユーザー アカウントの作成
`net user /add <username> <password>`
#### <a name="add-local-user-to-local-group"></a>ローカル グループにローカル ユーザーを追加する
`net localgroup Administrators <username> /add`
#### <a name="verify-user-account-is-enabled"></a>ユーザー アカウントが有効になっていることを確認する
`net user <username> | find /i "active"`

一般化されたイメージから作成した Azure VM では、ローカル管理者アカウントの名前が、VM のプロビジョニング時に指定する名前に変更されます。 したがって、通常は `Administrator` ではありません。
#### <a name="enable-user-account"></a>ユーザー アカウントの有効化
`net user <username> /active:yes`  
#### <a name="view-user-account-properties"></a>ユーザー アカウント プロパティの表示
`net user <username>`

ローカル管理者アカウントからの関連する行の例:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

#### <a name="view-local-groups"></a>ローカル グループの表示
`net localgroup`
### <a name="manage-the-windows-event-log"></a>Windows イベント ログの管理
#### <a name="query-event-log-errors"></a>イベント ログ エラーの照会
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

`/c:10` を変更して返すイベントの数を指定するか、これを移動してフィルターに一致するイベントをすべて返すようにします。
#### <a name="query-event-log-by-event-id"></a>イベント ID でイベント ログを照会する
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>イベント ID とプロバイダーでイベント ログを照会する
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>過去 24 時間のイベント ID とプロバイダーでイベント ログを照会する
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

`604800000` を使用すると、24 時間の代わりに 7 日間を指定できます。
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>過去 7 日間のイベント ID、プロバイダー、EventData でイベント ログを照会する
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>インストールしたアプリケーションの表示または削除
#### <a name="list-installed-applications"></a>インストールしたアプリケーションの一覧表示
`wmic product get Name,InstallDate | sort /r | more`

最近のインストールを確認しやすくするために、`sort /r` によってインストールの日付で降順に並べ替えます。 `<spacebar>` で出力の次のページに進み、`<enter>` で 1 行進みます。
#### <a name="uninstall-an-application"></a>アプリケーションのアンインストール
`wmic path win32_product where name="<name>" call uninstall`

`<name>` を、上記のコマンドで返される、削除するアプリケーションの名前に置き換えます。

### <a name="file-system-management"></a>ファイル システムの管理
#### <a name="get-file-version"></a>ファイル バージョンの取得
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

この例では、仮想 NIC ドライバーのファイル バージョンが返されます。それが netvsc.sys、netvsc63.sys、netvsc60.sys のいずれであるかは、Windows のバージョンによって異なります。
#### <a name="scan-for-system-file-corruption"></a>システム ファイルの破損のスキャン
`sfc /scannow`

「[Repair a Windows Image](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image)」(Windows イメージの修復) もご覧ください。
#### <a name="scan-for-system-file-corruption"></a>システム ファイルの破損のスキャン
`dism /online /cleanup-image /scanhealth`

「[Repair a Windows Image](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image)」(Windows イメージの修復) もご覧ください。
#### <a name="export-file-permissions-to-text-file"></a>ファイルのアクセス許可をテキスト ファイルにエクスポートする
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
#### <a name="save-file-permissions-to-acl-file"></a>ファイルのアクセス許可を ACL ファイルに保存する
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
#### <a name="restore-file-permissions-from-acl-file"></a>ACL ファイルからファイルのアクセス許可を復元する
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

`/restore` を使用する際のパスには、`/save` を使用する際に指定したフォルダーの親フォルダーを指定する必要があります。 この例では、上記の `/save` の例で指定した `\MachineKeys` フォルダーの親は、`\RSA` です。
#### <a name="take-ntfs-ownership-of-a-folder"></a>フォルダーの NTFS 所有権を取得する
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
#### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>再帰的にフォルダーに NTFS アクセス許可を付与する
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
### <a name="manage-devices"></a>デバイスの管理
#### <a name="remove-non-present-pnp-devices"></a>存在しない PNP デバイスの削除
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
### <a name="manage-group-policy"></a>グループ ポリシーの管理
#### <a name="force-group-policy-update"></a>グループ ポリシーを強制的に更新する
`gpupdate /force /wait:-1`
### <a name="miscellaneous-tasks"></a>その他のタスク
#### <a name="show-os-version"></a>OS バージョンの表示
`ver`

or 

`wmic os get caption,version,buildnumber /format:list`

or 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
#### <a name="view-os-install-date"></a>OS のインストール日の表示
`systeminfo | find /i "original"`

or 

`wmic os get installdate`
#### <a name="view-last-boot-time"></a>最終ブート時刻の表示
`systeminfo | find /i "system boot time"`
#### <a name="view-time-zone"></a>タイム ゾーンの表示
`systeminfo | find /i "time zone"`

or

`wmic timezone get caption,standardname /format:list`
#### <a name="restart-windows"></a>Windows の再起動
`shutdown /r /t 0`

`/f` を追加すると、ユーザーへの警告なしで実行中のアプリケーションを強制的に閉じます。
#### <a name="detect-safe-mode-boot"></a>セーフ モードでのブートの検出
`bcdedit /enum | find /i "safeboot"` 

## <a name="windows-commands---powershell"></a>Windows コマンド - PowerShell

SAC で PowerShell を実行するには、コマンド プロンプトで次のように入力します。

`powershell <enter>`

>[!CAUTION]
他の PowerShell コマンドを実行する前に、PowerShell セッションから PSReadLine モジュールを削除してください。 SAC の PowerShell セッションで PSReadLine が実行されていると、クリップボードから貼り付けたテキストに余分な文字が追加されるという既知の問題があります。

最初に、PSReadLine が読み込まれているかどうかを確認します。 Windows Server 2016、Windows 10、およびそれ以降のバージョンの Windows では、既定で読み込まれています。 以前のバージョンの Windows では、手動でインストールしない限り PSReadLine は存在しません。 

このコマンドが出力なしでプロンプトに戻る場合は、モジュールが読み込まれておらず、通常どおり引き続き SAC で PowerShell セッションを使用できます。

`get-module psreadline`

上記のコマンドで PSReadLine モジュールのバージョンが返される場合は、次のコマンドを実行してモジュールをアンロードします。 このコマンドは、モジュールを削除したりアンインストールしたりするのではなく、現在の PowerShell セッションからアンロードするのみです。

`remove-module psreadline`

### <a name="view-and-edit-windows-registry-settings"></a>Windows レジストリ設定の表示と編集
#### <a name="verify-rdp-is-enabled"></a>RDP が有効になっていることを確認する
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

2 番目のキー (\Policies 下) が存在するのは、関連するグループ ポリシー設定を構成する場合のみです。
#### <a name="enable-rdp"></a>RDP を有効にする
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

2 番目のキー (\Policies 下) が必要になるのは、関連するグループ ポリシー設定が構成されている場合のみです。 これをグループ ポリシーで構成する場合、値が書き換えられるのは次にグループ ポリシーを更新するときです。
### <a name="manage-windows-services"></a>Windows サービスの管理
#### <a name="view-service-details"></a>サービスの詳細の表示
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` を使用できますが、サービスのログオン アカウントは含まれません。 それには `Get-WmiObject win32-service` を使用します。
#### <a name="set-service-logon-account"></a>サービスのログオン アカウントの設定
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

`NT AUTHORITY\LocalService`、`NT AUTHORITY\NetworkService`、`LocalSystem` 以外のサービス アカウントを使用する場合は、アカウント名の後の最後の引数 (8 番目) に、アカウントのパスワードを指定します。
#### <a name="set-service-startup-type"></a>サービスのスタートアップの種類を設定する
`set-service termservice -startuptype Manual`

`Set-service` には、スタートアップの種類として `Automatic`、`Manual`、または `Disabled` を指定できます。
#### <a name="set-service-dependencies"></a>サービスの依存関係の設定
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
#### <a name="start-service"></a>サービスの開始
`start-service termservice`
#### <a name="stop-service"></a>サービスの停止
`stop-service termservice`
### <a name="manage-networking-features"></a>ネットワーク機能の管理
#### <a name="show-nic-properties"></a>NIC プロパティの表示
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

or 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

2012 以降では `Get-NetAdapter` を使用できます。2008R2 では `Get-WmiObject` を使用します。
#### <a name="show-ip-properties"></a>IP プロパティの表示
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
#### <a name="enable-nic"></a>NIC の有効化
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

or

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

2012 以降では `Get-NetAdapter` を使用できます。2008R2 では `Get-WmiObject` を使用します。
#### <a name="set-nic-to-use-dhcp"></a>DHCP を使用するように NIC を設定する
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

2012 以降では `Get-NetAdapter` を使用できます。 2008R2 では `Get-WmiObject` を使用します。 IP アドレスを取得するために DHCP を使用するように、ゲスト OS で Azure VM を常に構成する必要があります。 Azure の静的 IP 設定では、VM に IP を指定するために引き続き DHCP が使用されます。
#### <a name="ping"></a>ping
`test-netconnection`

or

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

パラメーターなしで `Test-Netconnection` を実行すると、`internetbeacon.msedge.net` への ping を試みます。 これは 2012 以降で使用可能です。 2008R2 では、2 番目の例のように `Get-WmiObject` を使用します。
#### <a name="port-ping"></a>ポートの ping
`test-netconnection -ComputerName bing.com -Port 80`

or

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

2012 以降では `Test-NetConnection` を使用できます。 2008R2 では `Net.Sockets.TcpClient` を使用します。
#### <a name="test-dns-name-resolution"></a>DNS 名前解決のテスト
`resolve-dnsname bing.com` 

or 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

2012 以降では `Resolve-DnsName` を使用できます。 2008R2 では `System.Net.DNS` を使用します。
#### <a name="show-windows-firewall-rule-by-name"></a>Windows ファイアウォール規則を名前で表示する
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
#### <a name="show-windows-firewall-rule-by-port"></a>Windows ファイアウォール規則をポートで表示する
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

or

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

2012 以降では `Get-NetFirewallPortFilter` を使用できます。 2008R2 では `hnetcfg.fwpolicy2` COM オブジェクトを使用します。 
#### <a name="disable-windows-firewall"></a>Windows ファイアウォールの無効化
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

2012 以降では `Set-NetFirewallProfile` を使用できます。 2008R2 では、上記の CMD に関するセクションに示されているように、`netsh advfirewall` を使用します。
### <a name="manage-users-and-groups"></a>ユーザーとグループの管理
#### <a name="create-local-user-account"></a>ローカル ユーザー アカウントの作成
`new-localuser <name>`
#### <a name="verify-user-account-is-enabled"></a>ユーザー アカウントが有効になっていることを確認する
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

or 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

2012 以降では `Get-LocalUser` を使用できます。 2008R2 では `Get-WmiObject` を使用します。 この例では、組み込みのローカル管理者アカウントが表示されます。それは常に `S-1-5-21-*-500` という SID を持ちます。 一般化されたイメージから作成した Azure VM では、ローカル管理者アカウントの名前が、VM のプロビジョニング時に指定する名前に変更されます。 したがって、通常は `Administrator` ではありません。
#### <a name="add-local-user-to-local-group"></a>ローカル グループにローカル ユーザーを追加する
`add-localgroupmember -group Administrators -member <username>`
#### <a name="enable-local-user-account"></a>ローカル ユーザー アカウントの有効化
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

この例では、組み込みのローカル管理者アカウントが有効化されます。それは常に `S-1-5-21-*-500` という SID を持ちます。 一般化されたイメージから作成した Azure VM では、ローカル管理者アカウントの名前が、VM のプロビジョニング時に指定する名前に変更されます。 したがって、通常は `Administrator` ではありません。
#### <a name="view-user-account-properties"></a>ユーザー アカウント プロパティの表示
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

or 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

2012 以降では `Get-LocalUser` を使用できます。 2008R2 では `Get-WmiObject` を使用します。 この例では、組み込みのローカル管理者アカウントが表示されます。それは常に `S-1-5-21-*-500` という SID を持ちます。
#### <a name="view-local-groups"></a>ローカル グループの表示
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

2012 以降では `Get-LocalUser` を使用できます。 2008R2 では `Get-WmiObject` を使用します。
### <a name="manage-the-windows-event-log"></a>Windows イベント ログの管理
#### <a name="query-event-log-errors"></a>イベント ログ エラーの照会
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

`/c:10` を変更して返すイベントの数を指定するか、これを移動してフィルターに一致するイベントをすべて返すようにします。
#### <a name="query-event-log-by-event-id"></a>イベント ID でイベント ログを照会する
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>イベント ID とプロバイダーでイベント ログを照会する
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>過去 24 時間のイベント ID とプロバイダーでイベント ログを照会する
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

`604800000` を使用すると、24 時間の代わりに 7 日間を指定できます。 |
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>過去 7 日間のイベント ID、プロバイダー、EventData でイベント ログを照会する
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>インストールしたアプリケーションの表示または削除
#### <a name="list-installed-software"></a>インストールされているソフトウェアを一覧表示する
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
#### <a name="uninstall-software"></a>ソフトウェアのアンインストール
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
### <a name="file-system-management"></a>ファイル システムの管理
#### <a name="get-file-version"></a>ファイル バージョンの取得
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

この例では、仮想 NIC ドライバーのファイル バージョンが返されます。その名前が netvsc.sys、netvsc63.sys、netvsc60.sys のいずれであるかは、Windows のバージョンによって異なります。
#### <a name="download-and-extract-file"></a>ファイルのダウンロードと抽出
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

この例では、`c:\bin` フォルダーを作成し、一連の Sysinternals ツールを `c:\bin` にダウンロードし、抽出します。
### <a name="miscellaneous-tasks"></a>その他のタスク
#### <a name="show-os-version"></a>OS バージョンの表示
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
#### <a name="view-os-install-date"></a>OS のインストール日の表示
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
#### <a name="view-last-boot-time"></a>最終ブート時刻の表示
`(get-wmiobject win32_operatingsystem).lastbootuptime`
#### <a name="view-windows-uptime"></a>Windows のアップタイムの表示
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

`<days>:<hours>:<minutes>:<seconds>:<milliseconds>` の形式でアップタイムを返します。たとえば `49:16:48:00.00` です。 
#### <a name="restart-windows"></a>Windows の再起動
`restart-computer`

`-force` を追加すると、ユーザーへの警告なしで実行中のアプリケーションを強制的に閉じます。
### <a name="instance-metadata"></a>インスタンス メタデータ

Azure VM 内から Azure インスタンス メタデータを照会して、osType、Location、vmSize、vmId、name、resourceGroupName、subscriptionId、privateIpAddress、publicIpAddress などの詳細を表示できます。

インスタンス メタデータを照会するには、ゲストのネットワーク接続が正常である必要があります。Azure ホストを通じてインスタンス メタデータ サービスに REST 呼び出しを実行する必要があるためです。 したがって、インスタンス メタデータの照会が可能であることは、ゲストがネットワーク経由で Azure でホストされるサービスに通信できることを意味します。

詳細については、「[Azure Instance Metadata Service](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)」をご覧ください。

#### <a name="instance-metadata"></a>インスタンス メタデータ
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
#### <a name="os-type-instance-metadata"></a>OS の種類 (インスタンス メタデータ)
`$im.Compute.osType`
#### <a name="location-instance-metadata"></a>位置情報 (インスタンス メタデータ)
`$im.Compute.Location`
#### <a name="size-instance-metadata"></a>サイズ (インスタンス メタデータ)
`$im.Compute.vmSize`
#### <a name="vm-id-instance-metadata"></a>VM ID (インスタンス メタデータ)
`$im.Compute.vmId`
#### <a name="vm-name-instance-metadata"></a>VM 名 (インスタンス メタデータ)
`$im.Compute.name`
#### <a name="resource-group-name-instance-metadata"></a>リソース グループ名 (インスタンス メタデータ)
`$im.Compute.resourceGroupName`
#### <a name="subscription-id-instance-metadata"></a>サブスクリプション ID (インスタンス メタデータ)
`$im.Compute.subscriptionId`
#### <a name="tags-instance-metadata"></a>タグ (インスタンス メタデータ)
`$im.Compute.tags`
#### <a name="placement-group-id-instance-metadata"></a>配置グループ ID (インスタンス メタデータ)
`$im.Compute.placementGroupId`
#### <a name="platform-fault-domain-instance-metadata"></a>プラットフォームの障害ドメイン (インスタンス メタデータ)
`$im.Compute.platformFaultDomain`
#### <a name="platform-update-domain-instance-metadata"></a>プラットフォームの更新ドメイン (インスタンス メタデータ)
`$im.Compute.platformUpdateDomain`
#### <a name="ipv4-private-ip-address-instance-metadata"></a>IPv4 プライベート IP アドレス (インスタンス メタデータ)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
#### <a name="ipv4-public-ip-address-instance-metadata"></a>IPv4 パブリック IP アドレス (インスタンス メタデータ)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
#### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>IPv4 サブネット アドレス/プレフィックス (インスタンス メタデータ)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
#### <a name="ipv6-ip-address-instance-metadata"></a>IPv6 IP アドレス (インスタンス メタデータ)
`$im.network.interface.ipv6.ipAddress`
#### <a name="mac-address-instance-metadata"></a>MAC アドレス (インスタンス メタデータ)
`$im.network.interface.macAddress`

## <a name="errors"></a>Errors
ほとんどのエラーは実際には一時的なものであり、接続の再試行によってこれらに対処します。 次の表にエラーと対応策を示します。 

エラー                            |   対応策 
:---------------------------------|:--------------------------------------------|
Unable to retrieve boot diagnostics settings for '<VMNAME>' \('<VMNAME>' のブート診断設定を取得できません。\) To use the serial console, ensure that boot diagnostics is enabled for this VM. (シリアル コンソールを使用するには、この VM のブート診断が有効になっていることを確認してください。) | VM の[ブート診断](boot-diagnostics.md)が有効になっていることを確認します。 
The VM is in a stopped deallocated state. (VM は停止済み (割り当て解除) 状態です。) Start the VM and retry the serial console connection. (VM を起動し、シリアル コンソール接続を再試行してください。) | シリアル コンソールにアクセスするには、仮想マシンが起動済み状態である必要があります。
You do not have the required permissions to use this VM serial console.\(この VM のシリアル コンソールを使用するために必要なアクセス許可がありません。\) Ensure you have at least VM Contributor role permissions.\(少なくとも VM 共同作成者ロールのアクセス許可があることを確認してください。\)| シリアル コンソールにアクセスするには、特定のアクセス許可が必要です。 詳細については、[アクセス要件](#prerequisites)をご覧ください。
Unable to determine the resource group for the boot diagnostics storage account '<STORAGEACCOUNTNAME>'. (ブート診断ストレージ アカウント '<STORAGEACCOUNTNAME>' のリソース グループを特定できません。) Verify that boot diagnostics is enabled for this VM and you have access to this storage account.\(この VM のブート診断が有効になっており、このストレージ アカウントにアクセスできることを確認してください。\) | シリアル コンソールにアクセスするには、特定のアクセス許可が必要です。 詳細については、[アクセス要件](#prerequisites)をご覧ください。

## <a name="known-issues"></a>既知の問題 
シリアル コンソールへのアクセスは現在プレビュー段階にあるため、Microsoft は既知の問題の解決に取り組んでいます。これらの問題と対応策を次に示します。 

問題                           |   対応策 
:---------------------------------|:--------------------------------------------|
仮想マシン スケール セット インスタンスのシリアル コンソールに関するオプションがない | プレビュー時点では、仮想マシン スケール セット インスタンスのシリアル コンソールへのアクセスはサポートされていません。
接続バナーの後に Enter キーを押しても、ログイン プロンプトが表示されない | [Enter キーを押しても何も実行されない](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)問題に関するページをご覧ください。
Windows VM に接続したときに、正常性情報だけが表示される| [Windows 正常性シグナル](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)に関するページをご覧ください。
カーネル デバッグを有効にすると、SAC プロンプトで入力できない | VM に RDP で接続して、管理者特権でのコマンド プロンプトから `bcdedit /debug {current} off` を実行します。 RDP で接続できない場合は、代わりに OS ディスクを別の Azure VM に接続して、データ ディスクとしてアタッチされている間に `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` を使用して変更し、ディスクを戻すこともできます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問 
**Q.フィードバックを送信するにはどうすればよいですか?**

A. https://aka.ms/serialconsolefeedback にアクセスして、問題点としてフィードバックをお送りください。 (あまりお勧めしませんが) azserialhelp@microsoft.com、または http://feedback.azure.com の仮想マシン カテゴリでフィードバックをお送りいただくこともできます。 

**Q."既存のコンソールに、要求された OS の種類 'Linux' と競合する OS の種類 'Windows' があります" というエラーが表示されます。**

A. これは修正すべき既知の問題です。[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) を bash モードで開いて再試行してください。

**Q.シリアル コンソールにアクセスできません。サポート ケースはどこで提出できますか?**

A. このプレビュー機能は、Azure プレビューの使用条件の対象となります。 このサポートは、前述のチャネルを介して適切に処理されます。 

## <a name="next-steps"></a>次の手順
* シリアル コンソールは、[Linux](../linux/serial-console.md) VM でも使用できます。
* [bootdiagnostics](boot-diagnostics.md) の詳細を確認します。

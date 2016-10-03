<properties
	pageTitle="Windows VHD の Azure へのアップロードの準備 | Microsoft Azure"
	description="Azure へのアップロード前に Windows VHD を準備するために推奨されるヒントを紹介します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="genlin"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/18/2016"
	ms.author="glimoli;genli"/>

# Windows VHD の Azure へのアップロードの準備
Windows VM をオンプレミスから Azure にアップロードするには、仮想ハード ディスク (VHD) を正しく準備する必要があります。VHD を Azure にアップロードする前に完了しておくことをお勧めするいくつかの手順があります。この記事では、Microsoft Azure にアップロードする Windows VHD を準備する方法と、[Sysprep を使用するタイミングおよび使用方法](#step23)について説明します。

## 仮想ディスクの準備

>[AZURE.NOTE] 新しい VHDX 形式は、Azure ではサポートされていません。VHD は動的ではなく、固定サイズである必要があります。必要に応じて、以下の VHDX またはダイナミック ディスクからの変換の詳細をご覧ください。VHD のサイズの上限は、1,023 GB です。

Windows VHD がローカル サーバーで正しく動作していることを確認します。Azure に変換またはアップロードする前に VM 自体に発生しているすべてのエラーを解決します。

仮想ディスクを Azure に必要な形式に変換する必要がある場合は、次のセクションに記載されているいずれかのメソッドを使用します。仮想ディスクの変換処理や Sysprep を実行する前に、VM のバックアップを取ってください。

### Hyper-V マネージャーを使用した変換
- Hyper-V マネージャーを開いて、左側のローカル コンピューターを選択します。その上にあるメニューで、**[アクション]**、**[ディスクの編集]** の順にクリックします。
	- **[仮想ハード ディスクの場所]** 画面でお使いの仮想ディスクを参照し、選択します。
	- 次の画面で **[変換]** を選択します。
		- VHDX から変換する場合は、**[VHD]** を選択し、**[次へ]** をクリックします。
		- ダイナミック ディスクから変換する場合は、**[固定サイズ]** を選択し、**[次へ]** をクリックします。

	- **新しい VHD ファイルのパス**を参照して選択します。
	- **[完了]** をクリックして閉じます。

### PowerShell を使用した変換
仮想ディスクを変換するには、[Convert-VHD PowerShell コマンドレット](http://technet.microsoft.com/library/hh848454.aspx)を使用します。次の例では、VHDX から VHD に、また、動的から固定サイズに変換します。

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

### VMware VMDK ディスク フォーマットからの変換
[VMDK ファイル形式](https://en.wikipedia.org/wiki/VMDK)の Windows VM イメージがある場合は、[Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) を使用して VHD に変換できます。詳細については、ブログ記事「[VMware VMDK から Hyper-V VHD への変換方法](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)」を参照してください。

## アップロード用の Windows 構成の準備

> [AZURE.NOTE] 次のすべてのコマンドを[管理者特権](https://technet.microsoft.com/library/cc947813.aspx)で実行します。

1. ルーティング テーブルの静的な固定ルートを削除します。

	- ルート テーブルを表示するには、`route print` を実行します。
	- **[Persistence Routes (固定ルート)]** セクションを確認します。固定ルートがある場合は、[ルートの削除](https://technet.microsoft.com/library/cc739598.apx)を使ってルートを削除します。

2. WinHTTP プロキシを削除します。

	```
	netsh winhttp reset proxy
	```

3. ディスク SAN ポリシーを [Onlineall](https://technet.microsoft.com/library/gg252636.aspx) に設定します。

	```
	diskpart san policy=onlineall
	```

4. Windows の世界協定時刻 (UTC) の時間を使用して、Windows タイム (w32time) サービスのスタートアップの種類を**自動**に設定します。

	```
	REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
	sc config w32time start= auto
	```


## Windows サービスの構成
5. 次の各 Windows サービスが **Windows の既定値**に設定されていることを確認します。これらは、次の一覧に記載されているスタートアップの設定で構成されます。次のコマンドを実行して、スタートアップの設定をリセットできます。

	```
	sc config bfe start= auto

	sc config dcomlaunch start= auto

	sc config dhcp start= auto

	sc config dnscache start= auto

	sc config IKEEXT start= auto

	sc config iphlpsvc start= auto

	sc config PolicyAgent start= demand

	sc config LSM start= auto

	sc config netlogon start= demand

	sc config netman start= demand

	sc config NcaSvc start= demand

	sc config netprofm start= demand

	sc config NlaSvc start= auto

	sc config nsi start= auto

	sc config RpcSs start= auto

	sc config RpcEptMapper start= auto

	sc config termService start= demand

	sc config MpsSvc start= auto

	sc config WinHttpAutoProxySvc start= demand

	sc config LanmanWorkstation start= auto

	sc config RemoteRegistry start= auto
	```


## リモート デスクトップ構成の設定
6. リモート デスクトップ プロトコル (RDP) リスナーに関連付けられている自己署名証明書がある場合は削除します。

	```
	REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
	```

	RDP リスナー用の証明書を構成する方法については、「[Listener Certificate Configurations in Windows Server](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/)」 (Windows Server でのリスナー証明書の構成) を参照してください。

7. RDP サービスの [KeepAlive](https://technet.microsoft.com/library/cc957549.aspx) 値を構成します。

	```
	REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f

	REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f

	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
	```

8. RDP サービスの認証モードを構成します。

	```
	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f

	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f

	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
	```

9. 次のサブキーをレジストリに追加して、RDP サービスを有効にします。

	```
	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
	```


## Windows ファイアウォール規則の構成
10. WinRM に 3 つのファイアウォール プロファイル (ドメイン、プライベート、パブリック) の通過を許可し、PowerShell リモート サービスを有効にします。

	```
	Enable-PSRemoting -force
	```

11. 次のゲスト オペレーティング システムのファイアウォール規則を満たしていることを確認します。

	- 受信

	```
	netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (LLMNR-UDP-In)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Datagram-In)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Name-In)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (Pub-WSD-In)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (SSDP-In)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnP-In)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD EventsSecure-In)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes

	netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
	```

	- 受信および送信

	```
	netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes

	netsh advfirewall firewall set rule group="Core Networking" new enable=yes
	```

	- 送信

	```
	netsh advfirewall firewall set rule dir=out name="Network Discovery (LLMNR-UDP-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Datagram-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Name-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=out name="Network Discovery (Pub-WSD-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=out name="Network Discovery (SSDP-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnPHost-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnP-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD Events-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD EventsSecure-Out)" new enable=yes

	netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD-Out)" new enable=yes
	```


## その他の Windows 構成の手順
12. `winmgmt /verifyrepository` を実行して、Windows Management Instrumentation (WMI) リポジトリに一貫性があることを確認します。リポジトリが破損している場合は、[こちらのブログ投稿](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not)をご覧ください。

13. ブート構成データ (BCD) の設定が以下と一致していることを確認します。

	```
	bcdedit /set {bootmgr} integrityservices enable

	bcdedit /set {default} device partition=C:

	bcdedit /set {default} integrityservices enable

	bcdedit /set {default} recoveryenabled Off

	bcdedit /set {default} osdevice partition=C:

	bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
	```

14. TCP パケットを分析するソフトウェアなど、余分な Transport Driver Interface フィルターを削除します。
15. ディスクが正常で一貫性があることを確認するには、`CHKDSK /f` コマンドを実行します。
16.	物理コンポーネントまたはその他の仮想化テクノロジに関連する、すべてのサードパーティ ソフトウェアとドライバーをアンインストールします。
17. サード パーティのアプリケーションでポート 3389 を使用していないことを確認します。このポートは、Azure の RDP サービスに使用します。アプリケーションで使用されているポートは、`netstat -anob` コマンドで確認できます。
18.	アップロードする Windows VHD がドメイン コントローラーの場合は、[追加の手順](https://support.microsoft.com/kb/2904015)に従ってディスクを準備します。
19.	VM を再起動して、Windows がまだ正常で、アクセスできることを RDP 接続を使用して確認します。
20.	現在の管理者パスワードをリセットし、このアカウントを使用して RDP 接続を介して Windows にサインインできることを確認します。このアクセス許可は、"リモート デスクトップ サービスを使ったログオンを許可" ポリシー オブジェクトによって制御されます。このオブジェクトは、Computer Configuration\\Windows Settings\\Security Settings\\Local Policies\\User Rights Assignment にあります。


## Windows 更新プログラムのインストール
22. Windows の最新の更新プログラムをインストールします。これを実行できない場合は、次の更新プログラムがインストールされていることを確認します。

	- [KB3137061](https://support.microsoft.com/kb/3137061) Microsoft Azure VMs don't recover from a network outage and data corruption issues occur (Microsoft Azure VM がネットワークの停止およびデータ破損の問題の発生から回復しない)

	- [KB3115224](https://support.microsoft.com/kb/3115224) Reliability improvements for VMs that are running on a Windows Server 2012 R2 or Windows Server 2012 host (Windows Server 2012 R2 または Windows Server 2012 ホストで実行される VM の信頼性に関する機能強化)

	- [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: 特権の昇格に対処する Microsoft Windows のセキュリティ更新プログラム (2016 年 3 月 8 日)

	- [KB3063075](https://support.microsoft.com/kb/3063075) Many ID 129 events are logged when you run a Windows Server 2012 R2 virtual machine in Microsoft Azure (Windows Server 2012 R2 仮想マシンを Microsoft Azure で実行すると多数の ID 129 イベントがログに記録される)

	- [KB3137061](https://support.microsoft.com/kb/3137061) Microsoft Azure VMs don't recover from a network outage and data corruption issues occur (Microsoft Azure VM がネットワークの停止およびデータ破損の問題の発生から回復しない)

	- [KB3114025](https://support.microsoft.com/kb/3114025) Slow performance when you access Azure files storage from Windows 8.1 or Server 2012 R2 (Windows 8.1 または Server 2012 R2 から Azure ファイル ストレージにアクセスしたときパフォーマンスが低下する)

	- [KB3033930](https://support.microsoft.com/kb/3033930) Hotfix fix increases the 64K limit on RIO buffers per process for Azure service in Windows (修正プログラムにより Windows での Azure サービスのプロセスごとの RIO バッファーの 64K の制限が向上)

	- [KB3004545](https://support.microsoft.com/kb/3004545) You cannot access virtual machines that are hosted on Azure hosting services through a VPN connection in Windows (Windows の VPN 接続を介して Azure ホスティング サービスでホストされている仮想マシンにアクセスできない)

	- [KB3082343](https://support.microsoft.com/kb/3082343) Cross-Premises VPN connectivity is lost when Azure site-to-site VPN tunnels use Windows Server 2012 R2 RRAS (Azure サイト間 VPN トンネルで Windows Server 2012 R2 RRAS を使用したときクロスプレミス VPN 接続が失われる)

	- [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: 特権の昇格に対処する Microsoft Windows のセキュリティ更新プログラム (2016 年 3 月 8 日)

	- [KB3146723](https://support.microsoft.com/kb/3146723) MS16-048: Description of the security update for CSRSS: April 12, 2016 (CSRSS 用のセキュリティ更新プログラムの説明: 2016 年 4 月 12 日)
	- [KB2904100](https://support.microsoft.com/kb/2904100) System freezes during disk I/O in Windows (Windows でディスク I/O 時にシステムがフリーズする) <a id="step23"></a>
23. 複数のマシンをデプロイするためのイメージを作成するには、VHD を Azure にアップロードする前に `sysprep` を実行してイメージを一般化する必要があります。専用 VHD を使用する場合は、`sysprep` を実行する必要はありません。一般化されたイメージを作成する方法については、次の記事を参照してください。

	- [Resource Manager デプロイメント モデルを使用した既存の Azure VM からの VM イメージの作成に関する記事](virtual-machines-windows-capture-image.md)
	- [クラシック デプロイメント モデルを使用した既存の Azure VM からの VM イメージの作成に関する記事](virtual-machines-windows-classic-capture-image.md)
	- [Sysprep Support for Server Roles (Sysprep でのサーバー ロールのサポート)](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)



## 推奨される追加の構成

次の設定は、VHD のアップロードに影響しません。ただし、これらを構成しておくことを強くお勧めします。

- [Azure Virtual Machines エージェント](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)をインストールします。エージェントをインストールしたら、VM 拡張機能を有効にすることができます。VM 拡張機能では、パスワードのリセットや RDP の構成など、VM で使用する重要な機能のほとんどを実装します。

- Dump ログは、Windows のクラッシュの問題をトラブルシューティングするのに役立つ場合があります。Dump ログ コレクションを有効にします。

	```
	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`

	REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f

	REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f

	REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f

	sc config wer start= auto
	```

- VM を Azure に作成したら、システムで定義されたサイズのページファイルをドライブ D: に構成します。

	```
	REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
	```


## 次のステップ

- [Resource Manager デプロイメント向けに Windows VM イメージを Azure にアップロードする](virtual-machines-windows-upload-image.md)

<!---HONumber=AcomDC_0921_2016-->
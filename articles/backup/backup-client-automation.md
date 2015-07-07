<properties
	pageTitle="Windows PowerShell を使用した Windows Server/Client バックアップのデプロイと管理| Microsoft Azure"
	description="Windows PowerShell を使用して Azure Backup をデプロイおよび管理する手順の説明"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/17/2015"
	ms.author="aashishr"/>


# Windows PowerShell を使用して Windows Server/Windows Client 用に Microsoft Azure Backup をデプロイおよび管理する手順
この記事では、Windows PowerShell® コマンドライン インターフェイスを使用して、Windows Server または Windows Client 上に Microsoft Azure Backup をセットアップし、バックアップと回復を管理する方法を示します。

## Windows PowerShell 環境のセットアップ
Windows PowerShell を使用して Microsoft Azure Backup を管理するには、Windows PowerShell の正しいバージョンと環境変数がセットアップされている必要があります。

## セットアップと登録
次のセットアップおよび登録タスクは Windows PowerShell で自動化できます - Microsoft Azure Backup エージェントのインストール - Microsoft Azure Backup サービスへの登録 - ネットワーク

### Microsoft Azure Backup エージェントのインストール
Microsoft Azure Backup エージェントをインストールするには、Windows Server に、インストーラーをダウンロードする必要があります。最新バージョンのインストーラーは、[Microsoft ダウンロード センター](aka.ms/azurebackup_agent)から入手することができます。インストーラーを、*C:\Downloads* などの、簡単にアクセスできる場所に保存します。エージェントをインストールするには、管理者特権の Windows PowerShell コンソールで、次のコマンドを実行します。

```
PS C:> MARSAgentInstaller.exe /q
```

これにより、エージェントはすべて既定のオプションが指定されてインストールされます。インストールは、バックグラウンドで数分かかります。**/nu** オプションを指定しない場合、インストールの最後に **[Windows Update]** ウィンドウが開き、更新プログラムが確認されます。

エージェントが、インストールされているプログラムの一覧に表示されます。インストールされているプログラムの一覧を表示するには、**[コントロール パネル]** > **[プログラム]** > **[プログラムと機能]** の順に移動します。

![インストールされているエージェント](./media/backup-client-automation/installed-agent-listing.png)

#### インストール オプション

コマンドラインで利用可能なすべてのオプションを表示するには、次のコマンドを使用します。

```
PS C:> MARSAgentInstaller.exe /?
```

利用可能なオプションは、次のとおりです。

| オプション | 詳細 | 既定値 |
| ---- | ----- | ----- |
| /q | サイレント インストール | - | | /p:"location" | Microsoft Azure Backup エージェントのインストール フォルダーへのパス | C:\Program Files\Microsoft Azure Recovery Services Agent | | /s:"location" | Microsoft Azure Backup エージェントのキャッシュ フォルダーへのパス | C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch | | /m | Microsoft Update のオプトイン | - | | /nu | インストールの完了後に更新プログラムを確認しない | - | | /d | Microsoft Azure Recovery Services エージェントをアンインストールする | - | | /ph | プロキシ ホストのアドレス | - | | /po | プロキシ ホストのポート番号 | - | | /pu | プロキシ ホストのユーザー名 | - | | /pw | プロキシ パスワード | - |


### Microsoft Azure Backup サービスへの登録
Microsoft Azure Backup サービスに登録する前に、[前提条件](backup-try-azure-backup-in-10-mins.md)が満たされていることを確認します。 - 有効な Azure サブスクリプションがある。 - Microsoft Azure Backup コンテナーが作成されている。 - コンテナーの資格情報がダウンロードされてアクセスしやすい場所 (*C:\Downloads* など) に保管されている。コンテナーの資格情報ファイルは、わかりやすい名前に変更することもできます。

コンテナーへのマシンの登録は、[Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) コマンドレットを使用して実行します。

```
PS C:> Start-OBRegistration -VaultCredentials "C:\Downloads\register.vaultcredentials" -Confirm:$false

CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : test-vault
Region              : Australia East

Machine registration succeeded.
```

> [AZURE.IMPORTANT]コンテナーの資格情報ファイルを指定する際には、相対パスは使用しないでください。このコマンドレットへの入力には、絶対パスを指定する必要があります。


### ネットワーク
プロキシ サーバーを介して Windows コンピューターをインターネットに接続した場合、プロキシ設定もエージェントに指定できます。この例では、プロキシ サーバーがないため、プロキシ関連の情報はないことを明示的に示しています。

特定の曜日セットに対して、帯域幅の使用を、*作業時間の帯域幅*オプションと*非作業時間の帯域幅*オプションで制御することもできます。

プロキシと帯域幅の詳細の設定は、[Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) コマンドレットを使用して実行します。

```
PS C:> Set-OBMachineSetting -NoProxy
Server properties updated successfully.
```

```
PS C:> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

### 暗号化の設定
データの機密性を保護するために、Microsoft Azure Backup に送信されるバックアップ データは暗号化されます。暗号化パスフレーズは、復元時にデータの暗号化を解除するための "パスワード" になります。設定したら、この情報をセキュリティで保護された安全な場所に保管することが重要です。

```
PS C:> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
Server properties updated successfully
```

## Microsoft Azure Backup エージェントのアンインストール
Microsoft Azure Backup エージェントのアンインストールは、次のコマンドを使用して実行できます。

```
PS C:> .\MARSAgentInstaller.exe /d /q
```

エージェント バイナリをコンピューターからアンインストールすると別の結果が生じます: - ファイル フィルターがコンピューターから削除され、変更の追跡が停止されます。 - すべてのポリシー情報がコンピューターから削除されますが、ポリシー情報は引き続きサービスに格納されます。 - すべてのバックアップ スケジュールが削除され、以降のバックアップは実施されません。

ただし、Azure に格納されたデータは、設定した保有ポリシーに基づいて、引き続き維持されます。古いポイントの期限は自動的に切れます。

## リモート管理
Microsoft Azure Backup エージェント、ポリシー、およびデータ ソースに関する管理はすべて、Windows PowerShell を使ってリモートで実行できます。リモートで管理されるコンピューターは、適切に準備されている必要があります。

既定では、WinRM サービスは手動で開始されるように設定されています。スタートアップの種類は*自動*に設定する必要があります。これにより、サービスが開始されます。WinRM サービスが実行していることを確認するには、Status プロパティの値が *[実行中]* になっていることを確認します。

```
PS C:> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...

- Windows PowerShell should be configured for remoting.
```

```
PS C:> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:> Set-ExecutionPolicy unrestricted -force
```

コンピューターを、エージェントのインストールを始め、リモートで管理できるようになりました。たとえば、次のスクリプトは、エージェントをリモート コンピューターにコピーし、インストールします。

```
PS C:> $dloc = "\REMOTESERVER01\c$\Windows\Temp"
PS C:> $agent = "\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:> $args = "/q"
PS C:> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```
## 次のステップ
Azure Backup for Windows Server/Client の詳細については、「[Azure Backup の概要](backup-introduction-to-azure-backup.md)」を参照してください。

<!---HONumber=62-->
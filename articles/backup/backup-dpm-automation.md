<properties
	pageTitle="Microsoft Azure Backup - Windows PowerShell を使用した DPM バックアップのデプロイと管理| Microsoft Azure"
	description="Windows PowerShell を使用して、Data Protection Manager (DPM) 用に Azure Backup をデプロイおよび管理する手順の説明"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/17/2015"
	ms.author="jimpark"/>


# Windows PowerShell を使用して Data Protection Manager (DPM) 用に Microsoft Azure Backup をデプロイおよび管理する手順
この記事では、Windows PowerShell® コマンドライン インターフェイスを使用して、DPM サーバー上に Microsoft Azure Backup をセットアップし、バックアップと回復を管理する方法を示します。

## Windows PowerShell 環境のセットアップ
Windows PowerShell を使用して Microsoft Azure Backup を管理するには、Windows PowerShell の正しいバージョンと環境変数がセットアップされている必要があります。

Windows PowerShell Version 3.0 または 4.0 があることを確認します。Windows PowerShell のバージョンを確認するには、Windows PowerShell/DPM コマンド プロンプトで、次のコマンドを入力します。

```
$PSVersionTable
```

次の種類の情報が表示されます。

| 名前 | 値 |
| ---- | ----- |
| PSVersion | 3.0 |
| WSManStackVersion | 3.0 |
| SerializationVersion | 1.1.0.1 |
| CLRVersion | 4.0.30319.18444 |
| BuildVersion | 6.2.9200.16481 |
| PSCompatibleVersions | {1.0, 2.0, 3.0} |
| PSRemotingProtocolVersion | 2.2 |

**PSVersion** の値が 3.0 または 4.0 であることを確認します。3.0 または 4.0 でない場合は、[Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) または [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855) のダウンロード ページを参照してください。

## セットアップと登録
### DPM サーバーへの Microsoft Azure Backup エージェントのインストール
Microsoft Azure Backup エージェントをインストールするには、Windows サーバーに、インストーラーをダウンロードする必要があります。最新バージョンのインストーラーは、[Microsoft ダウンロード センター](aka.ms/azurebackup_agent)から入手することができます。インストーラーを、*C:* などの、簡単にアクセスできる場所に保存します。

エージェントをインストールするには、管理者特権の DPM Windows PowerShell コンソールで、次のコマンドを実行します。

```
PS C:> MARSAgentInstaller.exe /q
```

これにより、エージェントはすべて既定のオプションが指定されてインストールされます。インストールは、バックグラウンドで数分かかります。*/nu* オプションを指定しない場合、インストールの最後に [Windows Update] ウィンドウが開き、更新プログラムが確認されます。

エージェントが、インストールされているプログラムの一覧に表示されます。インストールされているプログラムの一覧を表示するには、**[コントロール パネル]** > **[プログラム]** > **[プログラムと機能]** の順に移動します。

![インストールされているエージェント](./media/backup-dpm-automation/installed-agent-listing.png)

#### インストール オプション
コマンドラインで利用可能なすべてのオプションを表示するには、次のコマンドを使用します。

```
PS C:> MARSAgentInstaller.exe /?
```

利用可能なオプションは、次のとおりです。

| オプション | 詳細 | 既定値 |
| ---- | ----- | ----- |
| /q | サイレント インストール | - | | /p:"location" | Azure Backup エージェントのインストール フォルダーへのパス | C:\Program Files\Microsoft Azure Recovery Services Agent | | /s:"location" | Microsoft Azure Backup エージェントのキャッシュ フォルダーへのパス | C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch | | /m | Microsoft Update のオプトイン | - | | /nu | インストールの完了後に更新プログラムを確認しない | - | | /d | Microsoft Azure Recovery Services エージェントをアンインストールする | - | | /ph | プロキシ ホストのアドレス | - | | /po | プロキシ ホストのポート番号 | - | | /pu | プロキシ ホストのユーザー名 | - | | /pw | プロキシ パスワード | - |

### Microsoft Azure Backup サービスへの登録
Microsoft Azure Backup サービスに登録する前に、次の前提条件が満たされていることを確認します。 - 有効な Azure サブスクリプションがある。 - Microsoft Azure Backup コンテナーが作成されている。 - コンテナーの資格情報がダウンロードされてアクセスしやすい場所 (*C:\Downloads* など) に保管されている。コンテナーの資格情報ファイルは、わかりやすい名前に変更することもできます。

コンテナーへのマシンの登録は、[Start-DPMCloudRegistration](https://technet.microsoft.com/library/jj612787) コマンドレットを使用して実行します。

```
PS C:> Start-DPMCloudRegistration -DPMServerName "TestingServer" -VaultCredentialsFilePath "C:\DPMTESTVault_Friday, September 5, 2014.VaultCredentials"
```

これにより、"TestingServer" という名前の DPM サーバーは、Microsoft Azure Backup コンテナーに、*C:*. にあるコンテナーの資格情報を使用して登録されます。

> [AZURE.IMPORTANT]コンテナーの資格情報ファイルを指定する際には、相対パスは使用しないでください。このコマンドレットへの入力には、絶対パスを指定する必要があります。

### オンライン バックアップ用の DPM バックアップ オプションの構成 (サブスクリプションの設定)
DPM サーバーをオンライン保護用に登録すると、さまざまなバックアップ オプションを指定する、既定のサブスクリプション設定が設定されます。このサブスクリプション設定を変更したい場合は、既存の設定を取得し、要件に応じて変更する必要があります。

#### DPM サーバーの既存のサブスクリプション設定の取得
DPM オンライン サブスクリプション設定を構成するには、まず既存の設定を、[Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793.aspx) コマンドレットを使用して取得する必要があります。

```
$Setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

このコマンドを実行して、さまざまなオプションの構成に使用できるサブスクリプション設定オブジェクトを取得し、最後に設定を保存する必要があります。新しい DPM サーバーに対してコマンドを実行すると、既定の設定が返されます。

#### サブスクリプション設定の変更
DPM オンライン バックアップ サブスクリプションの次の設定を、[Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) コマンドレットを使用して構成することができます。

##### ステージング領域
DPM サーバーで実行されている Microsoft Azure Backup エージェントには、クラウドから復元されるデータ用の一時的なストレージ (ローカル ステージング領域) が必要です。ステージング領域を構成するには、次のコマンドを使用できます。これにより、**$setting** に対してステージング領域は "C:\StagingArea" に設定されますが、その構成は設定をコミットするまでは DPM サーバーに適用されません。

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -StagingAreaPath "C:\StagingArea"
```

> [AZURE.NOTE]上記のコマンドで指定したフォルダーが既に存在していることを確認します。存在していない場合は、サブスクリプション設定の保存時に、エラー メッセージが表示されます。

##### ネットワーク
DPM マシンからインターネットへの接続は、プロキシ サーバーを経由します。プロキシの設定は、エージェントに提供できます。この例では、プロキシ サーバーがないため、プロキシ関連の情報はないことを明示的に示しています。

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -NoProxy
```

特定の曜日セットに対して、帯域幅の使用を、*作業時間の帯域幅*オプションと*非作業時間の帯域幅*オプションで制御することもできます。この例では調整は設定しません。

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -NoThrottle
```

##### 暗号化の設定
データの機密性を保護するために、Microsoft Azure Backup に送信されるバックアップ データは暗号化されます。暗号化パスフレーズは、復元時にデータの暗号化を解除するための "パスワード" になります。

> [AZURE.IMPORTANT]設定したら、この情報をセキュリティで保護された安全な場所に保管してください。

次の例では、最初のコマンドは、文字列 passphrase123456789 をセキュリティ保護された文字列に変換し、そのセキュリティ保護された文字列を $Passphrase という名前の変数に割り当てます。2 番目のコマンドは、$Passphrase のセキュリティ保護された文字列を、バックアップ暗号化のパスワードとして設定します。

```
PS C:> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force
```

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -EncryptionPassphrase $Passphrase
```

#### サブスクリプション設定の保存
DPM サーバーへの変更は、*–Commit* オプションを使用してコミットする必要があります。

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -Commit
```

> [AZURE.NOTE]コミットされない限り、設定は適用されません。

## Microsoft Azure Backup へのデータの保護
次の手順では、運用サーバーを DPM に追加し、その後、そのデータをディスクに、さらにオンラインで Azure にバックアップして保護する方法を説明します。この例では、ファイルとフォルダーをバックアップする方法を示します。このロジックは、サポートされているすべてのデータ ソースのバックアップに簡単に拡張して適用できます。前提条件として、オンライン保護用の DPM サーバーがセットアップされて登録されている必要があります。

すべての DPM バックアップは、4 つの部分で構成される保護グループ (PG) によって制御されます。

1. **グループ メンバー**は、同じ PG 内で保護するすべての保護可能オブジェクトの一覧です。たとえば、運用 VM とテスト VM がそれぞれ異なるバックアップ要件を持つため、別々の PG で保護したいという場合があります。
2. **データ保護方法**は、データを保護する場所を指定するために使用します。この例では、ディスクでの保護と Azure クラウドへのオンライン保護によってデータを保護します。
3. **バックアップ スケジュール**は、バックアップを実行する必要があるタイミングと、DPM サーバーのコピーと運用サーバーの間でデータを同期すべき頻度を指定します。
4. **保持スケジュール**は、Azure で回復ポイントを保持する期間を指定します。

### 保護グループの作成およびスケジュールされた間隔でのデータのバックアップ
#### DPM サーバーへの運用サーバーの追加
運用サーバー上のデータをバックアップする前に、DPM エージェントが運用サーバーにインストールされており、DPM により管理されていることを確認する必要があります。DPM エージェントをインストールして、適切な DPM サーバーによりバックアップが実行されるように構成するには、[こちら](https://technet.microsoft.com/library/bb870935.aspx)の手順に従ってください。

#### PG の作成
この記事では、バックアップを自動化するため、構成を何も行っていないことを前提としています。まず新しい PG を、[New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722.aspx) コマンドレットを使用して作成します。

```
PS C:> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

PG に変更を加える前に、[Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713.aspx) コマンドレットを使用して PG を変更可能モードにする必要があります。

```
PS C:> $MPG = Get-ModifiableProtectionGroup $PG
```

上記のコマンドレットにより "ProtectGroup01" という名前の PG が作成されますが、バックアップはまだ実行されません。バックアップの対象、バックアップ実行のタイミング、バックアップの格納先を定義する必要があります。

### PG へのグループ メンバーの追加
データソースを PG に追加するには、いくつかの手順を実行する必要があります。

#### バックアップ対象とする運用サーバー (PS) の取得
DPM エージェントがインストールされており、DPM サーバーによって管理されるすべての PS を、[Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600.aspx) コマンドレットを使用して取得します。この例では、フィルター処理をして "productionserver01" という名前を持つ PS のみをバックアップ用に構成します。

```
PS C:> $PS = Get-ProductionServer -DPMServerName "TestingServer" |where {($_.servername) –contains “productionserver01”
```

### PS 上のデータソース一覧を取得する照会の実行
[Get-DPMDatasource](https://technet.microsoft.com/library/hh881605.aspx) コマンドレットを使用して、PS マシンへの照会を実行することにより、DPM がバックアップできるすべてのデータソースの一覧を取得できます。この例では、保護を構成する PS から、データソース "D:\" をフィルター処理で取得することができます。

```
PS C:> $DS = Get-Datasource -ProductionServer $PS -Inquire | where { $_.Name -contains “D:\” }
```

#### 保護対象データソースの追加
[Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx) コマンドレットを使用して、上記の例で作成した PG "ProtectGroup01" にデータソースを追加することができます。

```
PS C:> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

### データ保護方法の選択
データソースを PG に追加したら、保護の方法を指定する必要があります。このケースでは、短期保護はディスクで、長期保護はテープで行うように PG をセットアップします。

```
PS C:> Set-DPMProtectionType -ProtectionGroup $PG -ShortTerm Disk –LongTerm Online
```

### 回復ポイント目標の指定
#### 保有期間の範囲設定
PG の保有期間は、[Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762.aspx) を使用して設定することができます。次のコマンドは、ディスク ベースの回復ポイントの*保有期間*と*同期の頻度*を設定します。

```
PS C:> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequency 360
```

オンライン回復ポイントに対しては、さまざまな GFS スキームの保有期間 (日、週、月、および年の単位の期間など) を構成することができます。この例では、次に示すとおり、さまざまな保有期間を持つオブジェクトを作成し、それらのオブジェクトを使用してオンラインの保有期間を構成します。

```
PS C:> $RRlist = @()
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

#### バックアップ スケジュールの設定
**Set-policyobjective** コマンドレットを使用して保護目標を指定する場合、DPM は既定のスケジュールを自動的に設定します。既定のスケジュールを変更するには、[Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749.aspx) コマンドレットと、それに続いて [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723.aspx) コマンドレットを使用します。

```
PS C:> $onlineSch=Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTermOnline
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
```

#### レプリカの作成
データソースを初めてバックアップする場合、DPM レプリカ ボリューム上に保護対象データソースのコピーを作成する初期レプリカを、DPM により作成する必要があります。レプリカは、[Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715.aspx) コマンドレットを使用して、指定時刻に自動的に作成されるようにスケジュールすることも、手動で作成することもできます。

```
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

#### PG のコミット
最後に、[Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758.aspx) コマンドレットを使用して、DPM が PG 構成ごとにバックアップを実行する前に、変更をコミットする必要があります。

```
PS C:> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## すべてのオンライン回復ポイントの一覧取得
データソースのすべての回復ポイントの一覧を取得するには、[Get DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746.aspx) コマンドレットを使用します。この例では、次の手順を実行します。 - 配列 $PG に格納される、DPM サーバー上のすべての PG を取得する。 - $PG[0] に対応するデータソースを取得する。 - データソースのすべての回復ポイントを取得する。

```
PS C:> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## Azure で保護されているデータの復元
この例では、Hyper-V VM をオンライン回復ポイントから復元する方法を示します。使用されているコマンドは、任意のデータソースの種類に合わせて簡単に拡張できます。

まず [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592.aspx) コマンドレットを使用して、回復オプションを作成する必要があります。次の例では、Hyper-V データソースを別の場所に回復します。

```
PS C:> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “c:\VMRecovery”
```

回復オプションを構成したら、前述の「[すべてのオンライン回復ポイントの一覧取得](#Getting-a-list-of-all-Online-Recovery-Points)」のセクションで取得したオンライン回復ポイントを復元します。

```
PS C:> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints -RecoveryOption $RecoveryOption
```
## 次のステップ
Azure DPM Backup の詳細については、「[Azure DPM Backup の概要](backup-azure-dpm-introduction.md)」を参照してください。

<!---HONumber=62-->
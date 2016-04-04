<properties
   pageTitle="Microsoft Azure のセキュリティと監査ログの管理 | Microsoft Azure"
   description="Azure でホストされているサービスからのセキュリティ ログの生成、収集、および分析の概要について説明します。組織のセキュリティおよびコンプライアンス担当者など、日常的に情報資産の管理に携わる IT プロフェッショナルとセキュリティ アナリストを対象とした内容です。"
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="nayak-mahesh"
   manager="msStevenPo"
   editor=""/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/10/2015"
   ms.author="mnayak;tomsh;terrylan"/>

# Microsoft Azure のセキュリティと監査ログの管理

Azure を使用すると、セキュリティ イベントを生成し、Azure Infrastructure as a Service (IaaS) および Platform as a Service (PaaS) ロールからサブスクリプションの中央ストレージに収集できます。その後、ユーザーは [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) を使用して収集されたイベントを集計し、分析できます。さらに、これらの収集されたイベントをオンプレミスのセキュリティ情報およびイベント管理 (SIEM) システムにエクスポートして継続的に監視できます。

Azure のセキュリティ ログ、分析、および監視のライフサイクルには次のものが含まれます。

- **生成**: イベントを生成するようにアプリケーションとインフラストラクチャをインストルメント化します
- **収集**: ストレージ アカウントのさまざまなセキュリティ ログを収集するように Azure を構成します
- **分析**: HDInsight などの Azure ツールおよびオンプレミスの SIEM システムを使用して、ログを分析し、セキュリティ情報を生成します
- **監視とレポート**: Azure には、継続的な可視性とタイムリーな通知を提供する一元化された監視および分析システムが用意されています

この記事では、ライフサイクルの生成および収集フェーズについて説明します。

## ログの生成
セキュリティ イベントは、仮想マシンの **システム**、**セキュリティ**、および**アプリケーション**の各チャネルの Windows イベント ログで生成されます。データが失われることなくイベントがログに記録されるようにするには、イベント ログのサイズを適切に構成することが重要です。イベント ログのサイズは、監査ポリシーの設定が生成するイベントの数と、定義されているイベント収集ポリシーに基づいて決定します。詳細については、[セキュリティ監査の監視と管理の計画](http://technet.microsoft.com/library/ee513968.aspx#BKMK_4)に関するページを参照してください。

>[AZURE.NOTE] Windows イベント転送 (WEF) または Azure 診断 (「[ログの収集](#log-collection)」セクションを参照) を使用して Cloud Services または Virtual Machines からログを取得する場合、システム停止の影響の可能性を考慮します。たとえば、WEF 環境がしばらくダウンした場合は、時間が長くなっても大丈夫なようにログのサイズが十分に大きいこと、またはログ データ喪失の可能性に備えられていることを、確認する必要があります。

[Azure Virtual Machines Marketplace](https://azure.microsoft.com/marketplace/virtual-machines/#microsoft) から作成されて Azure または仮想マシンにデプロイされる Cloud Services アプリケーションの場合、オペレーティング システム セキュリティ イベントのセットは既定で有効になります。ユーザーは、オペレーティング システムの監査ポリシーをカスタマイズすることによって、監査対象のイベントを追加、削除、または変更できます。詳細については、[セキュリティ ポリシー設定リファレンス](http://technet.microsoft.com/library/jj852210.aspx)に関するページを参照してください。

次の方法を使用して、オペレーティング システム (監査ポリシーの変更など) および Windows コンポーネント (IIS など) から追加のログを生成できます。

- ドメインに参加している Azure 内の仮想マシンにポリシー設定をロールアウトするためのグループ ポリシー
- ポリシー設定をプッシュおよび管理するための Desired State Configuration (DSC)。詳細については、「[Azure PowerShell DSC](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx)」を参照してください。
- Cloud Services に設定をロールアウトするためのサービス デプロイメント ロール スタートアップ コード (PaaS のシナリオ)

Azure ロールのスタートアップ タスクを構成すると、ロールが開始する前にコードを実行できます。ロールのスタートアップ タスクを定義するには、次の例に示すように、サービス定義ファイルのロールの定義に **Startup** 要素を追加します。詳細については、[Azure におけるスタートアップ タスクの実行](http://msdn.microsoft.com/library/azure/hh180155.aspx)に関するページを参照してください。

スタートアップ タスクとして実行するタスク ファイル (以下の例では EnableLogOnAudit.cmd) をビルド パッケージに含める必要があります。Visual Studio を使用している場合は、ファイルをクラウド プロジェクトに追加し、ファイル名を右クリックして **[プロパティ]** をクリックし、**[出力ディレクトリにコピー]** を **[常にコピーする]** に設定します。

    <Startup>
        <Task commandLine="EnableLogOnAudit.cmd" executionContext="elevated" taskType="simple" />
    </Startup>

EnableLogOnAudit.cmd の内容:

    @echo off
    auditpol.exe /set /category:"Logon/Logoff" /success:enable /failure:enable
    Exit /B 0

前の例で使用されている [Auditpol.exe](https://technet.microsoft.com/library/cc731451.aspx) は Windows Server オペレーティング システムに含まれるコマンドライン ツールであり、監査ポリシーの設定を管理できます。

Windows イベント ログを生成するだけでなく、セキュリティ分析および監視に重要なログを生成するように、Windows オペレーティング システムのさまざまなコンポーネントを構成できます。たとえば、インターネット インフォメーション サービス (IIS) のログと http.err ログは Web ロールに対して自動的に生成され、収集対象として構成できます。これらのログは、承認されていないアクセスまたは Web ロールに対する攻撃を識別するために使用できる貴重な情報を提供します。詳細については、「[IIS でのログの構成](http://technet.microsoft.com/library/hh831775.aspx)」および「[IIS の高度なログ: カスタム ログ](http://www.iis.net/learn/extensions/advanced-logging-module/advanced-logging-for-iis-custom-logging)」を参照してください。

Web ロールのサービス定義ファイルにスタートアップ タスクを追加して、Web ロールの IIS ログを変更できます。次の例では、Contoso という名前の Web サイトの HTTP ログを有効にし、IIS が Contoso Web サイトに対するすべての要求をログに記録するよう指定しています。

IIS の構成を更新するタスクを、Web ロールのサービス定義ファイルに含める必要があります。サービス定義ファイルに対する次の変更は、ConfigureIISLogging.cmd という名前のスクリプトを実行することによって IIS ログを構成するスタートアップ タスクを実行します。

    <Startup>
        <Task commandLine="ConfigureIISLogging.cmd" executionContext="elevated" taskType="simple" />
    </Startup>

ConfigureIISLogging.cmd の内容

    @echo off
    appcmd.exe set config "Contoso" -section:system.webServer/httpLogging /dontLog:"True" /commit:apphost
    appcmd.exe set config "Contoso" -section:system.webServer/httpLogging /selectiveLogging:"LogAll" /commit
    Exit /B 0


## <a name="log-collection"></a>ログの収集
Azure の Cloud Services または Virtual Machines からのセキュリティ イベントおよびログの収集は、2 つの主要な方法で行われます。

- Azure 診断は、顧客の Azure ストレージ アカウントでのイベントを収集します。
- Windows イベント転送 (WEF) は、Windows を実行しているコンピューターでのテクノロジです。

これら 2 つのテクノロジの主な相違点を次の表にまとめます。要件およびこれらの主な違いに基づき、適切な方法を選択してログ収集を実装する必要があります。

| Azure 診断 | Windows イベント転送 |
|-----|-----|
|Azure Virtual Machines と Azure Cloud Services をサポートします | ドメインに参加している Azure Virtual Machines のみサポートします |
|Windows イベント ログ、[Windows イベント トレーシング](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) トレース、IIS ログなど、さまざまなログ形式をサポートします。詳細については、「[Azure 診断でサポートされているデータ ソース](#diagnostics)」を参照してください。 |Windows イベント ログのみをサポートします |
|収集されたデータを Azure Storage にプッシュします |収集されたデータを中央のコレクター サーバーに移動します |

##	Windows イベント転送でのセキュリティ イベント データ収集
ドメインに参加している Azure Virtual Machines の場合、オンプレミスのドメイン参加コンピューターと同じ方法でグループ ポリシー設定を使用して WEF を構成できます。詳細については、[ハイブリッド クラウド](http://www.microsoft.com/server-cloud/solutions/hybrid-cloud.aspx)に関するページを参照してください。

この方法を使用すると、IaaS サブスクリプションを購入し、[ExpressRoute](https://azure.microsoft.com/services/expressroute/) またはサイト間 VPN を使用してそれを企業ネットワークに接続した後、Azure 内の仮想マシンを企業ドメインに参加させることができます。その後は、ドメインに参加しているコンピューターから WEF を構成できます。

イベント転送は、ソースとコレクターの 2 つの部分に分かれます。ソースは、セキュリティ ログを生成するコンピューターです。コレクターは、イベント ログを収集して統合する中央のサーバーです。IT 管理者はイベントをサブスクライブすることで、リモート コンピューター (イベント ソース) から転送されるイベントを受信して格納できます。詳細については、「[イベントを転送して収集するようコンピューターを構成する](http://technet.microsoft.com/library/cc748890.aspx)」を参照してください。

収集された Windows イベントは、さらに詳しい分析のために SIEM などのオンプレミスの分析ツールに送信できます。

## Azure 診断でのセキュリティ データの収集
Azure 診断を使用すると、Azure で実行しているクラウド サービスの worker ロールや Web ロールまたは仮想マシンから、診断データを収集できます。Azure 診断は定義済みのゲスト エージェント拡張機能であり、データを収集するには有効化して構成する必要があります。ユーザーのサブスクリプションには、Azure Storage へのデータのプッシュを含めることができます。

データは暗号化されて転送されます (HTTPS を使用)。このドキュメントの例では、Azure 診断 1.2 を使用しています。セキュリティ データを収集するにはバージョン 1.2 以降にアップグレードすることをお勧めします。詳細については、「[Azure 診断を使用したログ データの収集](http://msdn.microsoft.com/library/gg433048.aspx)」を参照してください。

次の図では、Azure 診断を使用したデータ収集とその後の分析および監視に関するデータ フローの概要を示します。

![][1]

Azure 診断は、ユーザーの Cloud Services アプリケーションおよび [Azure Virtual Machines](virtual-machines/virtual-machines-linux-about.md) から Azure Storage にログを移動します。ログの形式に基づき、データは Azure テーブルまたは BLOB に格納されます。[Azure Storage](storage-introduction.md) に収集されたデータは、Azure Storage Client Library を使用してオンプレミスの SIEM システムにダウンロードし、監視および分析できます。

また、HDInsight を使用するとクラウド内のデータをさらに分析できます。Azure 診断を使用するセキュリティ データ収集の例を次に示します。

### Azure 診断を使用した Azure Virtual Machines からのセキュリティ データ収集

次の例では、Azure 診断 1.2 と Azure PowerShell コマンドレットを使用して、仮想マシンからセキュリティ データを収集できるようにしています。データは、仮想マシンからスケジュールされた (構成可能な) 間隔で収集されて、ユーザーのサブスクリプション内の Azure Storage にプッシュされます。このセクションでは、Azure 診断を使用する 2 つのログ収集シナリオについて説明します。

1. 仮想マシンにセキュリティ ログ収集パイプラインの新しいインスタンスを設定します。
2. 仮想マシンの既存のセキュリティ ログ収集パイプラインを新しい構成で更新します。

#### 仮想マシンにセキュリティ ログ収集パイプラインの新しいインスタンスを設定する
この例では、Azure 診断を使用するセキュリティ ログ収集パイプラインの新しいインスタンスを設定し、仮想マシンからログオン失敗イベント (イベント ID 4624 と 4625) を検出します。開発環境から次の手順を実装することも、リモート デスクトップ プロトコル (RDP) 経由でクラウド内のノードへのリモート デスクトップ セッションを使用することもできます。

##### ステップ 1: Azure PowerShell SDK をインストールする
Azure PowerShell SDK は、Azure Virtual Machines に Azure 診断を構成するためのコマンドレットを提供します。必要なコマンドレットは、Azure PowerShell バージョン 0.8.7 以降で使用できます。詳細については、「[Azure PowerShell のインストールと構成の方法](powershell-install-configure.md)」を参照してください。

##### ステップ 2: 構成ファイルを準備する
収集するイベントに基づいて構成ファイルを準備します。次に示すのは、**Security** チャネルから Windows イベントを収集するための Azure 診断構成ファイルの例です。ログオンの成功と失敗のイベントだけを収集するためのフィルターが追加されています。詳細については、「[Azure 診断 1.2 構成スキーマ](http://msdn.microsoft.com/library/azure/dn782207.aspx)」を参照してください。

ストレージ アカウントは、構成ファイルで指定することも、Azure PowerShell コマンドレットを実行して Azure 診断をセットアップするときにパラメーターとして指定することもできます。

    <?xml version="1.0" encoding="utf-8" ?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
            <DiagnosticMonitorConfiguration overallQuotaInMB="10000">
                <WindowsEventLog scheduledTransferPeriod="PT1M">
                    <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
                </WindowsEventLog>
            </DiagnosticMonitorConfiguration>
        </WadCfg>
    </PublicConfig>

以前の内容を XML ファイルとして保存するときは、エンコードを **UTF-8** に設定してください。メモ帳を使用している場合は、[名前を付けて保存] ダイアログ ボックスに使用可能なエンコード オプションが表示されます。次の表では、構成ファイルの重要な属性を示します。

| 属性 | 説明 |
|----- |-----|
| overallQuotaInMB | Azure 診断で使用できるローカル ディスク領域の最大量 (値は構成可能)。 |
| scheduledTransferPeriod | Azure Storage への転送のスケジュール間隔。最も近い分単位に切り上げられます。 |
| Name | WindowsEventLog では、この属性は収集する Windows イベントを記述する XPath クエリです。イベント ID、プロバイダー名、チャネルなどのフィルターを追加することで、データの収集をフィルター処理できます。 |

すべての Windows イベント ログ データは、**WADWindowsEventLogsTable** という名前のテーブルに移動されます。現時点では、Azure 診断はテーブル名の変更をサポートしていません。

##### <a name="step3"></a> ステップ 3: 構成 XML ファイルを検証する
構成 XML ファイルにエラーがないこと、および Azure 診断スキーマと互換性があることを検証するには、次の手順を使用します。

1. スキーマ ファイルをダウンロードするには、次のコマンドを実行し、ファイルを保存します。

    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfigSchema.xsd'

2. スキーマ ファイルをダウンロードした後は、スキーマに対して構成 XML ファイルを検証することができます。Visual Studio を使用してファイルを検証するには:
  - Visual Studio で XML ファイルを開きます。
  - F4 キーを押して **[プロパティ]** を開きます。
  - **[スキーマ]** をクリックし、**[追加]** をクリックして、ダウンロードしたスキーマ ファイル (WadConfigSchema.XSD) を選択し、**[OK]** をクリックします。

3.	**[表示]** メニューの **[エラー リスト]** をクリックし、検証エラーを確認します。

##### <a name="step4"></a> ステップ 4: Azure 診断を構成する
 次の手順を使用して、Azure 診断を有効にし、データの収集を開始します。

 1.	Azure PowerShell を開くには、「**Add-AzureAccount**」と入力して Enter キーを押します。
 2.	Azure のアカウントを使用してサインインします。
 3.	次の PowerShell スクリプトを実行します。storage\_name、key、config\_path、service\_name、vm\_name を忘れずに更新してください。

 ```PowerShell
$storage_name ="<Storage Name>"
$key = "<Storage Key>"
$config_path="<Path Of WAD Config XML>"
$service_name="<Service Name. Usually it is same as VM Name>"
$vm_name="<VM Name>"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
$VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
$VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
$VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM
 ```

##### ステップ 5: イベントを生成する
説明のため、ログオン イベントをいくつか作成し、データが Azure Storage に転送されることを確認します。前のステップ 2 で示したように、XML ファイルはイベント ID 4624 (ログオン成功) およびイベント ID 4625 (ログオン失敗) を **Security** チャネルから収集するように構成されています。

 これらのイベントを生成するには:

1.	仮想マシンに対する RDP セッションを開きます。
2.	正しくない資格情報を入力し、ログオン失敗イベント (イベント ID 4625) を生成します。
3.	ログオンを何回か失敗した後、正しい資格情報を入力してログオン成功イベント (イベント ID 4624) を生成します。

##### ステップ 6: データを表示する
前の手順を完了してから約 5 分後には、XML ファイルの構成に基づいてユーザーのストレージ アカウントへのデータの転送が開始します。複数のツールを使用して Azure Storage のデータを表示できます。詳細については、次を参照してください。

- [サーバー エクスプローラーを使用したストレージ リソースの参照](http://msdn.microsoft.com/library/azure/ff683677.aspx)
- [Azure Storage Explorer 6 Preview 3 (August 2014)](http://azurestorageexplorer.codeplex.com/)

データを表示するには:

1.	Visual Studio (2013、2012、2010 with SP1) で、**[表示]** をクリックし、**[サーバー エクスプローラー]** をクリックします。
2.	ストレージ アカウントに移動します。
3.	**[テーブル]** をクリックし、適切なテーブルをクリックして、仮想マシンから収集されたセキュリティ ログを表示します。![][2]

4.	WADWindowsEventLogsTable という名前のテーブルを右クリックし、**[データの表示]** をクリックして、次に示すようにテーブル ビューを開きます。

![][3]

前のストレージ テーブルでは、**PartitionKey**、**RowKey**、および **Timestamp** はシステム プロパティです。

- **PartitionKey** は秒単位のタイムスタンプであり、テーブルのパーティションを示す一意の識別子です。
- **RowKey** は、パーティションのエンティティを示す一意の識別子です。

**PartitionKey** と **RowKey** の組み合わせにより、テーブル内のすべてのエンティティが一意に識別されます。

- タイムスタンプは日付/時刻の値であり、エンティティが最後に変更されたときを追跡するためにサーバー上に保持されます。

>[AZURE.NOTE] Azure Storage のテーブルの最大行サイズは、1 MB に制限されています。アカウントが 2012 年 6 月より後に作成された場合、ストレージ アカウントは BLOB、キュー、およびテーブルからのデータを最大 200 TB まで格納できます。そのため、BLOB およびキューがストレージ領域を使用しない場合、テーブルのサイズは最大 200 TB まで拡大できます。2012 年 6 月以前に作成されたアカウントには、100 TB の制限があります。

Storage Explorer ではテーブルのデータを編集することもできます。テーブル ビューの特定の行をダブルクリックして、次に示すような [エンティティの編集] ウィンドウを開きます。

![][4]

#### 仮想マシンの既存のセキュリティ ログ収集パイプラインを新しい構成で更新する
このセクションでは、仮想マシンの既存の Azure 診断セキュリティ ログ収集パイプラインを更新し、Windows アプリケーション イベント ログのエラーを検出します。

##### ステップ 1: 対象のイベントを含むように構成ファイルを更新する
前の例で作成した Azure 診断ファイルを、Windows アプリケーション イベント ログ エラーの種類を含むように更新する必要があります。

>[AZURE.NOTE] 既存の Azure 診断の構成設定を、新しい構成ファイルとマージする必要があります。新しいファイルで定義された設定により、既存の構成は上書きされます。

既存の構成設定を取得するには、**Get-AzureVMDiagnosticsExtension** コマンドレットを使用します。既存の構成を取得するサンプルの Azure PowerShell スクリプトを次に示します。

    $service_name="<VM Name>"
    $VM1 = Get-AzureVM -ServiceName $service_name
    $config = Get-AzureVMDiagnosticsExtension -VM $VM1 | Select -Expand PublicConfiguration | % {$_.substring($_.IndexOf(':"')+2,$_.LastIndexOf('",')-$_.IndexOf(':"')-2)}
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($config))
次のように、Windows アプリケーション イベント ログ エラーおよび重大なイベントを収集するように、Azure 診断の構成を更新します。

    <?xml version="1.0" encoding="utf-8" ?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="10000">
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level =2)]]" />
                <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
            </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

前の「[ステップ 3: 構成 XML ファイルを検証する](#step3)」と同じ手順を使用して、構成ファイルを検証します。

##### ステップ 2: 新しい構成ファイルを使用するように Azure 診断を更新する
前の「[ステップ 4: Azure 診断を構成する](#step4)」と同じように、**Set-AzureVMDiagnosticsExtension** および **Update-AzureVM** コマンドレットを使用して構成を更新します。

##### ステップ 3: 構成設定を確認する
次のコマンドを実行して、構成設定が更新されたことを確認します。

    $service_name="<VM Name>"
    $VM1 = Get-AzureVM -ServiceName $service_name
    Get-AzureVMDiagnosticsExtension -VM $VM1

##### ステップ 4: イベントを生成する
この例では、次のコマンドを実行して、種類が**エラー**のアプリケーション イベント ログを生成します。

    eventcreate /t error /id 100 /l application /d "Create event in application log for Demo Purpose"

![][5]

イベント ビューアーを開き、イベントが作成されたことを確認します。

![][6]

##### ステップ 5: データを表示する
Visual Studio でサーバー エクスプローラーを開き、ログ データを表示します。次に示すように、**ContosoDesktop** に **EventID 100** が作成されているはずです。

![][7]

## Azure 診断を使用した Azure Cloud Services からのセキュリティ データ収集

次に、Azure 診断を使用して、前の Virtual Machines (IaaS) セクションと同じ 2 つのログ収集シナリオを今度は Azure Cloud Services から行う場合について説明します。

1.	クラウド サービスでセキュリティ ログ パイプラインの新しいインスタンスを設定します。
2.	クラウド サービスの既存のログ収集パイプラインを新しい構成で更新します。

このセクションの手順は次のとおりです。

1.	クラウド サービスを作成します。
2.	Azure 診断を使用したセキュリティ ログ収集用にクラウド サービスを構成します。
3.	クラウド サービスでセキュリティ イベントの生成と収集を実演します。

    - 特権が昇格されたローカル グループに管理者を追加する
    - 新しいプロセスを作成する
4.	クラウド サービスの既存のログ収集パイプラインを更新します。

    - Auditpol を使用して (ネットワーク セキュリティ イベントの例として) ホスト ファイアウォール イベントの監査を有効にします。
    - 収集するファイアウォール監査データを構成し、ユーザーのストレージ アカウントに収集されたイベントを表示します。
5.	Windows セキュリティ イベントの分布とスパイクの検出を示します。
6.	IIS ログの収集を構成し、データを確認します。

すべてのイベントとログは、Azure のユーザーのストレージ アカウントに収集されます。ユーザーは、イベントを表示し、オンプレミスの SIEM システムにエクスポートできます。また、HDInsight を使用して集計および分析することもできます。

### クラウド サービスにログ収集パイプラインの新しいインスタンスを設定する
この例では、Azure 診断を使用するセキュリティ ログ収集パイプラインの新しいインスタンスをセットアップし、ローカル グループへのユーザーの追加およびクラウド サービス インスタンスでの新規プロセス作成イベントを検出します。

#### ステップ 1: クラウド サービス (Web ロール) を作成してデプロイする

1.	開発コンピューター上で Visual Studio 2013 を起動します。
2.	新しいクラウド サービス プロジェクトを作成します (この例では ContosoWebRole を使用します)。
3.	**ASP.NET** Web ロールを選択します。
4.	**MVC** プロジェクトを選択します。
5.	ソリューション エクスプローラーで、**[ロール]** をクリックし、Web ロール (WebRole1) をダブルクリックして **[プロパティ]** ウィンドウを開きます。
6.	**[構成]** タブで **[診断を有効にする]** チェック ボックスをオフにして、Visual Studio 2013 に付属するバージョンの Azure 診断を無効にします。 ![][8]

7.	ソリューションを構築してエラーが発生しないことを確認します。
8.	WebRole1/Controllers/HomeController.cs ファイルを開きます。
9.	次のメソッドを追加し、サンプルの IIS ログ イベントとして HTTP ステータス コード 500 をサンプル アプリケーションがログに記録できるようにします (これを後の IIS の例で使用します)。

    ```
    public ActionResult StatusCode500()
        {
            throw new InvalidOperationException("Response.StatusCode is 500");
        }
    ```

10.	 クラウド サービス プロジェクトの名前を右クリックし、**[発行]** をクリックします。

#### ステップ 2: 構成ファイルを準備する
次の状況の検出に役立つイベントを追加するように Azure 診断構成ファイルを準備します。

- ローカル グループに新しいユーザーを追加する
- 新しいプロセスを作成する

```
<?xml version="1.0" encoding="UTF-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
</PublicConfig>
```

#### ステップ 3: 構成 XML ファイルを検証する
前の「[ステップ 3: 構成 XML ファイルを検証する](#step3)」と同じ手順を使用して、構成ファイルを検証します。
#### ステップ 4: Azure 診断を構成する
次の Azure PowerShell スクリプトを実行して、Azure 診断を有効にします (storage\_name、key、config\_path、service\_name を忘れずに構成してください)。

    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

サービスの診断構成が最新であることを確認するには、次の Azure PowerShell コマンドを実行します。

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### ステップ 5: イベントを生成する
イベントを生成するには:

1.	クラウド サービス インスタンスへのリモート デスクトップ セッションを開始するには、Visual Studio でサーバー エクスプローラーを開き、ロールのインスタンスを右クリックして、[リモート デスクトップを使用して接続] をクリックします。
2.	管理者特権のコマンド プロンプトを開き、次のコマンドを実行して、仮想マシンにローカル管理者アカウントを作成します。


    net user contosoadmin <enterpassword> /add net localgroup administrators contosoadmin /add

3.	次に示すように、イベント ビューアーを開き、**Security** チャネルを開いて、イベント 4732 が作成されていることを確認します。

![][9]

#### ステップ 6: データを表示する
Azure 診断エージェントがストレージ テーブルにイベントをプッシュするまで、約 5 分間待ちます。

![][10]

プロセス作成イベントを確認するには、メモ帳を開きます。次のように、プロセス作成イベントが Security チャネルに記録されています。

![][11]

次に示すように、ストレージ アカウントでも同じイベントを表示できます。

![][12]

### クラウド サービスの既存のログ収集パイプラインを新しい構成で更新する
このセクションでは、既存の Azure 診断セキュリティ ログ収集パイプラインを更新し、クラウド サービス インスタンスでの Windows ファイアウォール変更イベントを検出します。

ファイアウォールの変更を検出するため、ファイアウォール変更イベントを含むように既存の構成を更新します。

#### ステップ 1: 既存の構成を取得する
>[AZURE.NOTE] 既存の構成は、新しい構成設定で上書きされます。したがって、既存の Azure 診断の構成設定を新しい構成ファイルとマージすることが重要です。

既存の構成設定を取得するには、**Get-AzureServiceDiagnosticsExtension** コマンドレットを使用します。

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### ステップ 2: ファイアウォール イベントを含むように構成 XML を構成する

    <?xml version="1.0" encoding="UTF-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
        <WindowsEventLog scheduledTransferPeriod="PT1M">
            <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            <DataSource name="Security!*[System[(EventID &gt;= 4944 and EventID &lt;= 4958)]]" />
        </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

前の「[ステップ 3: 構成 XML ファイルを検証する](#step3)」と同じ検証プロセスを使用して、XML の内容を検証します。

#### ステップ 3: 新しい構成を使用するように Azure 診断を更新する

次の Azure PowerShell スクリプトを実行し、新しい構成を使用するように Azure 診断を構成します (storage\_name、key、config\_path、service\_name を実際のクラウド サービスの情報に置き換えます)。

    Remove-AzureServiceDiagnosticsExtension -ServiceName <ServiceName> -Role <RoleName>
    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

サービスの診断構成が最新であることを確認するには、次の Azure PowerShell コマンドを実行します。

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### ステップ 4: ファイアウォール イベントを有効にする

1.	クラウド サービス インスタンスへのリモート デスクトップ セッションを開きます。
2.	管理者特権のコマンド プロンプトを開き、次のコマンドを実行します。

    ```
    auditpol.exe /set /category:"Policy Change" /subcategory:"MPSSVC rule-level Policy Change" /success:enable /failure:enable
    ```

#### ステップ 5: イベントを生成する

1.	Windows ファイアウォールを開き、**[受信の規則]** をクリックします。
2.	**[新しい規則の追加]** をクリックし、**[ポート]** をクリックします。
3.	**[ローカル ポート]** フィールドに「**5000**」と入力した後、**[次へ]** を 3 回クリックします。
4.	**[名前]** フィールドに「**Test5000**」と入力し、**[完了]** をクリックします。
5.	次に示すように、イベント ビューアーを開き、**Security** チャネルを開いて、イベント ID 4946 が作成されていることを確認します。

![][13]

#### ステップ 6: データを表示する
Azure 診断エージェントがストレージ テーブルにイベント データをプッシュするまで、約 5 分間待ちます。

![][14]

### セキュリティ イベントの分布とスパイクの検出
イベントがユーザーのストレージ アカウントに転送された後、アプリケーションはストレージ クライアント ライブラリを使用してイベントにアクセスし、イベントの集計を実行できます。テーブル データにアクセスするサンプル コードについては、「[.NET からテーブル ストレージを使用する方法](storage-dotnet-how-to-use-tables.md)」を参照してください。

イベント集計の例を以下に示します。イベント分布のスパイクで異常なアクティビティについて詳細に調査できます。

![][15]

### HDInsight を使用した IIS ログの収集と処理
このセクションでは、Web ロールのインスタンスから IIS ログを収集し、ユーザーのストレージ アカウントの Azure BLOB にログを移動します。

#### ステップ 1: IIS ログの主集を含むように構成ファイルを更新する

    <?xml version="1.0" encoding="UTF-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
        <Directories scheduledTransferPeriod="PT5M">
        <IISLogs containerName="iislogs" />
        </Directories>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
            <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            <DataSource name="Security!*[System[(EventID &gt;= 4944 and EventID &lt;= 4958)]]" />
        </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

前の Azure 診断の構成で、**containerName** はログの移動先であるユーザーのストレージ アカウント内の BLOB コンテナーの名前です。

前の「[ステップ 3: 構成 XML ファイルを検証する](#step3)」と同じ手順を使用して、構成ファイルを検証します。


#### ステップ 2: 新しい構成を使用するように Azure 診断を更新する
次の Azure PowerShell スクリプトを実行し、新しい構成を使用するように Azure 診断を構成します (storage\_name、key、config\_path、service\_name を実際のクラウド サービスの情報に置き換えます)。

    Remove-AzureServiceDiagnosticsExtension -ServiceName <ServiceName> -Role <RoleName>
    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

サービスの診断構成が最新であることを確認するには、次の Azure PowerShell コマンドを実行します。

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### ステップ 3: IIS ログを生成する

1.	Web ブラウザーを開き、クラウド サービスの Web ロールに移動します (例: http://contosowebrole.cloudapp.net/))。
2.	**[バージョン情報]** および **[連絡先]** ページに移動し、何らかのログ イベントを作成します。
3.	ステータス コード 500 を生成するページに移動します (例: http://contosowebrole.cloudapp.net/Home/StatusCode500))。次のようなエラーが表示されます。「クラウド サービスにログ収集パイプラインの新しいインスタンスを設定する」のステップ 1 で **StatusCode500** に対するコードを追加しました。![][16]
4.	クラウド サービス インスタンスへのリモート デスクトップ セッションを開きます。
5.	IIS マネージャーを開きます。
6.	IIS ログは既定で有効になり、すべてのフィールドを含む W3C 形式のファイルを 1 時間ごとに生成するように設定されます。**[参照]** をクリックすると、次に示すように少なくとも 1 つのログ ファイルがあります。 ![][17]

7.	Azure 診断エージェントがログ ファイルを BLOB コンテナーにプッシュするまで、約 5 分間待ちます。データを確認するには、**[サーバー エクスプローラー]**、**[Storage]**、**[ストレージ アカウント]**、**[BLOB]** の順に開きます。ここで示すように、BLOB **iislogs** が作成されています。![][18]

8.	右クリックして **[BLOB コンテナーの表示]** を選択し、BLOB に格納されている IIS ログ ファイルを表示します。 ![][19]
9.	IIS イベントがユーザーのストレージ アカウントに格納された後、HDInsight の分析を利用するアプリケーションを使用してイベントの集計を実行できます。次に示すのは、HTTP ステータス コード 500 を示すイベント集計タスクの折れ線グラフの例です。 ![][20]

## セキュリティ ログの収集に関する推奨事項
セキュリティ ログを収集するときは、次のことをお勧めします。

- 独自のサービス アプリケーション固有の監査ログ イベントを収集します。
- 分析と監視に必要なデータのみを構成します。キャプチャするデータが多すぎると、トラブルシューティングが困難になり、サービスまたはストレージのコストに影響を与えることがあります。
- 既存の Azure 診断の構成設定と行う変更をマージします。既存の構成設定は、新しい構成ファイルで上書きされます。
- **転送期間のスケジュール設定**の間隔を適切に選択します。転送間隔が短いとデータの関連性は増えますが、ストレージのコストと処理のオーバーヘッドが増加します。

>[AZURE.NOTE] 収集されるデータ量に大きな影響を与えるもう 1 つの変数は、ログのレベルです。ログ レベルでログをフィルター処理する方法の例を次に示します。

    System!*[System[(Level =2)]]

ログ レベルは累積されます。フィルターを**警告**に設定すると、**エラー**と**重大**のイベントも収集されます。

- 不要になった診断データを Azure Storage から定期的に消去します。

>[AZURE.NOTE] 診断データの詳細については、「[Azure Storage への診断データの保存と表示](https://msdn.microsoft.com/library/azure/hh411534.aspx)」を参照してください。診断データを格納するコンテナーとテーブルは、他のコンテナーやテーブルと同じであり、他のデータの場合と同じように BLOB やエンティティをそこから削除できます。ストレージ クライアント ライブラリの 1 つを使用してプログラムで、または[ストレージ エクスプローラー クライアント](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)を使用して手動で、診断データを削除できます。

- サービスのデータとセキュリティ ログのデータを異なるストレージ アカウントに格納するのが最善の方法です。このように分離すると、セキュリティ ログ データの保存が運用サービス データのストレージ パフォーマンスに影響しません。
- 組織のコンプライアンス ポリシーとデータ分析および監視の要件に基づいて、ログの保持期間を選択します。

## 別のシステムへのセキュリティ ログのエクスポート
Azure Storage Client Library を使用して BLOB データをダウンロードした後、オンプレミスのシステムにエクスポートして処理できます。BLOB データを管理するサンプル コードについては、「[.NET から BLOB ストレージを使用する方法](storage-dotnet-how-to-use-blobs.md)」を参照してください。

同様に、Azure Storage Client Library を使用して、Azure のテーブルに格納されているセキュリティ データをダウンロードできます。テーブルに格納されているデータにアクセスする方法の詳細については、「[.NET からテーブル ストレージを使用する方法](storage-dotnet-how-to-use-tables.md)」を参照してください。

## Azure Active Directory のレポート
Azure Active Directory (Azure AD) に含まれるセキュリティ、使用状況、および監査ログのレポートを使用すると、Azure AD テナントの整合性とセキュリティに関する詳細がわかります。たとえば、Azure AD には、ユーザーのアクティビティを自動的に分析して異常なアクセスを検出し、ユーザーが見ることのできるレポートを作成する機能があります。

これらのレポートは、[Azure 管理ポータル](https://manage.windowsazure.com/)の **[Active Directory]** > **[ディレクトリ]** で使用できます。これらのレポートの一部は無料で、他は Azure AD Premium Edition の一部として提供されます。Azure AD レポートの詳細については、「[アクセスおよび使用状況レポートの表示](http://msdn.microsoft.com/library/azure/dn283934.aspx)」を参照してください。

## Azure の操作ログ
Azure サブスクリプション リソースに関連する操作のログは、管理ポータルの**操作ログ**機能でも使用できます。

**操作ログ**を表示するには、[Azure 管理ポータル](https://manage.windowsazure.com/)を開き、**[管理サービス]** をクリックして、**[操作ログ]** をクリックします。

## <a name="diagnostics"></a> Azure 診断でサポートされるデータ ソース

| データ ソース | 説明 |
|----- | ----- |
| IIS ログ | IIS Websites に関する情報 |
| Azure 診断インフラストラクチャ ログ | Azure 診断に関する情報 |
| IIS の失敗した要求ログ | IIS Web サイトまたはアプリケーションへの失敗した要求に関する情報 |
| Windows イベント ログ | Windows イベント ログ システムに送信された情報 |
| パフォーマンス カウンター | オペレーティング システムとカスタム パフォーマンス カウンター |
| クラッシュ ダンプ | アプリケーションがクラッシュした場合のプロセスの状態に関する情報 |
| カスタム エラー ログ | アプリケーションまたはサービスで作成されたログ |
| .NET EventSource | .NET EventSource クラスを使用してコードで生成されたイベント |
| マニフェスト ベースの ETW | すべてのプロセスによって生成された Windows イベントのイベント トレース |

## その他のリソース
次のリソースでは、Microsoft Azure およびそれに関連する Microsoft サービスに関する一般情報を提供します。

- [Microsoft Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/)

    セキュリティとプライバシーが Azure の開発に埋め込まれる方法と、Azure が国際および業界固有の標準のセットを満たす方法について説明します

- [Microsoft Azure のホーム ページ](http://www.microsoft.com/windowsazure/)

    Microsoft Azure に関する一般的な情報とリンク

- [Microsoft Azure ドキュメント センター](http://msdn.microsoft.com/windowsazure/default.aspx)

    Azure のサービスと自動化スクリプトのためのガイダンス

- [Microsoft Security Response Center (MSRC)](http://www.microsoft.com/security/msrc/default.aspx)

    MSRC はパートナーおよび世界中のセキュリティ研究者と協力して、セキュリティ インシデントを防止し、Microsoft 製品のセキュリティを強化します

- [Microsoft Security Response Center の電子メール](mailto:secure@microsoft.com)

    Microsoft Azure などの Microsoft のセキュリティ脆弱性を電子メールで報告します

<!--Image references-->
[1]: ./media/azure-security-audit-log-management/sec-security-data-collection-flow.png
[2]: ./media/azure-security-audit-log-management/sec-storage-table-security-log.PNG
[3]: ./media/azure-security-audit-log-management/sec-wad-windows-event-logs-table.png
[4]: ./media/azure-security-audit-log-management/sec-edit-entity.png
[5]: ./media/azure-security-audit-log-management/sec-app-event-log-cmd.png
[6]: ./media/azure-security-audit-log-management/sec-event-viewer.png
[7]: ./media/azure-security-audit-log-management/sec-event-id100.png
[8]: ./media/azure-security-audit-log-management/sec-diagnostics.png
[9]: ./media/azure-security-audit-log-management/sec-event4732.png
[10]: ./media/azure-security-audit-log-management/sec-step6.png
[11]: ./media/azure-security-audit-log-management/sec-process-creation-event.png
[12]: ./media/azure-security-audit-log-management/sec-process-creation-event-storage.png
[13]: ./media/azure-security-audit-log-management/sec-event4946.png
[14]: ./media/azure-security-audit-log-management/sec-event4946-storage.png
[15]: ./media/azure-security-audit-log-management/sec-event-aggregation.png
[16]: ./media/azure-security-audit-log-management/sec-status-code500.png
[17]: ./media/azure-security-audit-log-management/sec-w3c-format.png
[18]: ./media/azure-security-audit-log-management/sec-blob-iis-logs.png
[19]: ./media/azure-security-audit-log-management/sec-view-blob-container.png
[20]: ./media/azure-security-audit-log-management/sec-hdinsight-analysis.png

<!---HONumber=AcomDC_0323_2016-->
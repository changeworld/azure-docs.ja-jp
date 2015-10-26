<properties
   pageTitle="Chocolatey を使用した Azure Automation DSC の継続的なデプロイメント |Microsoft Azure"
   description="Azure Automation DSC と Chocolatey パッケージ マネージャーを使用した DevOps の継続的なデプロイメント。完全な JSON の ARM テンプレートと PowerShell ソースの例です。"
   services="automation"
   documentationCenter=""
   authors="sebastus"
   manager="stevenka"
   editor=""/>

<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="10/07/2015"
   ms.author="golive"/>

# Azure Automation DSC と Chocolatey を使用した IaaS VM への継続的なデプロイメント

DevOps 領域には、継続的な統合パイプラインのさまざまなポイントで役立つ多くのツールがあります。Azure Automation Desired State Configuration (DSC) は、DevOps チームが採用できる新たに追加された待望のオプションです。この記事では、Windows コンピューター用の継続的なデプロイメント (CD) の設定について説明します。必要な数の Windows コンピューターをロール (Web サイトなど) に含め、さらにそこから追加ロールにも含めるために手法を簡単に拡張することができます。

メタ: この記事は 2015 年 9 月に記述されたものです。Visual Studio のバージョンは 2015です。[PowerShell Tools for Visual Studio](http://adamdriscoll.github.io/poshtools/) がインストールされており、構文の強調表示や Intellisense を追加することで PowerShell での作業がより簡単になります。現時点では、Azure Automation DSC はパブリック プレビューの段階であるため、GA 状態への移行時に変更が生じる可能性があります。例の[完全なソース](https://github.com/sebastus/ARM/tree/master/CDIaaSVM)は GitHub にあります。

![IaaS VM の継続的なデプロイメント](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## 概要

ここにはかなり多くのことが示されていますが、幸いにも次の 2 つのメイン プロセスに分けることができます。

  - コードを記述し、テストしてから、メジャーおよびマイナー バージョンのシステム用にインストール パッケージを作成して発行する。 
  - パッケージ内のコードをインストールおよび実行する VM を作成および管理する。  

これらのコア プロセスの両方が実施されていれば、新しいバージョンの作成およびデプロイ時に、特定の VM で実行されているパッケージを短い手順で自動的に更新できるようになります。

## コンポーネントの概要

[apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) などのパッケージ マネージャーは、Linux の世界ではかなりよく知られていますが、Windows の世界ではそれほどでもありません。[Chocolatey](https://chocolatey.org/) はそのようなものであり、適切な概要がトピックの Scott Hanselman の [ブログ](http://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx)に示されています。簡単に言えば、Chocolatey ではコマンドラインを使用して、パッケージの中央リポジトリから Windows システムにパッケージをインストールすることができます。ユーザーは独自のリポジトリを作成してそれを管理でき、Chocolatey を使用して、指定した任意の数のリポジトリからパッケージをインストールすることができます。

Desired State Configuration (DSC) ([概要](https://technet.microsoft.com/JA-JP/library/dn249912.aspx)) は、マシンに必要な構成を宣言できるようにする PowerShell ツールです。たとえば、Chocolatey をインストールする必要がある、IIS をインストールする必要がある、ポート 80 を開く必要がある、バージョン 1.0.0 の自分の Web サイトをインストールする必要がある、などと示すことができます。 DSC Local Configuration Manager (LCM) により、その構成が実装されます。DSC プル サーバーは、マシンの構成リポジトリを保持します。各マシンの LCM は、その構成が格納されている構成と一致するかどうかを定期的に確認します。状態をレポートしたり、格納されている構成に合わせてマシンを元に戻すように試みたりすることもできます。プル サーバーに格納されている構成を編集することで、マシンやマシン セットを変更された構成に合わせることができます。

Azure Automation は、Runbook、およびノード、資格情報、リソースなどの他の資産を使用して、さまざまなタスクを自動化できる Microsoft Azure の管理されたサービスです。Azure Automation DSC はこのオートメーション機能を拡張して、PowerShell DSC ツールを組み込みます。こちらの適切な[概要](automation-dsc-overview.md)を参照してください。

DSC リソースは、ネットワーク、Active Directory、または SQL Server の管理などの特定の機能を備えたコードのモジュールです。Chocolatey DSC リソースは、(特に) NuGet サーバーへのアクセス、パッケージのダウンロード、パッケージのインストールなどの方法を認識しています。[PowerShell ギャラリー](http://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)には、その他の多くの DSC リソースがあります。これらのモジュールは、(ユーザーによって) Azure Automation DSC プル サーバーにインストールされるため、ユーザーの構成で使用できます。

ARM テンプレートでは、インフラストラクチャ (ネットワーク、サブネット、ネットワークのセキュリティおよびルーティング、Load Balancer、NIC、VM など) を宣言的な方法で生成できます。この[記事](../resource-manager-deployment-model.md)では、ARM デプロイメント モデル (宣言型) と、Azure サービス管理 (ASM、つまり、クラシック) デプロイメント モデル (命令型) が比較されています。また、別の[記事](../virtual-machines\virtual-machines-azurerm-versus-azuresm.md)では、コア リソース プロバイダー、コンピューティング、ストレージおよびネットワークについて記載されています。

ARM テンプレートの 1 つの主要機能は、プロビジョニングされている VM に VM 拡張機能をインストールする機能です。VM 拡張機能には、カスタム スクリプトの実行、ウイルス対策ソフトウェアのインストール、DSC 構成スクリプトの実行などの特定の機能があります。VM 拡張機能には他にも多くの種類があります。

## ダイアグラムの概説

上から順に、コードの記述、ビルド、テスト、インストール パッケージの作成が示されています。Chocolatey は、MSI、MSU、ZIP など、さまざまな種類のインストール パッケージを処理することができます。Chocolatey のネイティブ機能が適切でない場合は、PowerShell の全機能を使用して実際にインストールを行うことができます。到達可能な場所 (パッケージ リポジトリ) にパッケージを配置します。ここでは、自分の Azure BLOB ストレージ アカウントのパブリック フォルダーを使用しますが、どの場所でかまいません。Chocolatey は、NuGet サーバーや他のいくつかのサーバーとネイティブに動作して、パッケージ メタデータを管理します。[この記事](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed)ではオプションについて説明しています。このサンプルでは NuGet を使用します。Nuspec は、パッケージに関するメタデータです。Nuspec は NuPkg に "コンパイル" され、NuGet サーバーに格納されます。構成が名前でパッケージを要求し、NuGet サーバーを参照すると、Chocolatey DSC リソース (現在 VM 上にある) は自動的にパッケージを取得して、インストールします。特定のバージョンのパッケージを要求することもできます。

図の左下部には、Azure リソース マネージャー (ARM) のテンプレートがあります。この例の VM 拡張機能では、VM をノードとして Azure Automation DSC プル サーバー (つまり、プル サーバー) に登録します。構成はプル サーバーに格納されます。実際には、2 回格納されます。つまり、プレーン テキストとして 1 回格納され、MOF ファイルとして 1 回コンパイルされます (該当するものが認識されている場合)。 ポータルでは、MOF は "ノード構成" (単なる "構成" ではない) を指します。これはノードに関連付けられているアーティファクトであるため、ノードはその構成を認識します。ノードへのノード構成の割り当て方法の詳細については、以下に示します。

この時点で、おそらく、最上部の一部あるいは大部分の作業は既に実行されています。nuspec を作成し、それをコンパイルして NuGet サーバーに格納するのはささいなことです。また、VM の管理も既に行われています。継続的なデプロイメントのための次の手順を実行するには、プル サーバーを (1 回) 設定し、それにノードを (1 回) 登録してから、そこで (最初に) 構成を作成して格納する必要があります。次に、パッケージがアップグレードされ、リポジトリにデプロイされたときに、プル サーバーの構成を更新します (必要に応じて繰り返します)。

ARM テンプレートを使用しなくてもかまいません。プル サーバーと他のすべてのサーバーに VM を登録する際に役立つように設計されている PowerShell コマンドレットがあります。


## 手順 1: プル サーバーとオートメーション アカウントを設定する

以下の認証済み (Add-AzureAccount) の PowerShell コマンドラインを使用します (プル サーバーの設定には数分かかる場合があります)。

    Switch-AzureMode -Name AzureResourceManager 
    Register-AzureProvider –ProviderNamespace Microsoft.Automation 
    Register-AzureProviderFeature -FeatureName dsc -ProviderNamespace Microsoft.Automation 
    New-AzureResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
    New-AzureAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT 

オートメーション アカウントは、東日本、米国東部 2、西ヨーロッパ、東南アジア、米国中南部のいずれかのリージョン (場所ともいう) に配置できます。もちろん、オートメーション リソース グループはオートメーション アカウントと同じリージョンにあるようにします。

## 手順 2: VM 拡張機能を使用して ARM テンプレートに応じて微調整する

(PowerShell DSC VM 拡張機能を使用する) VM 登録の詳細については、この [Azure クイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)を参照してください。この手順では、新しい VM を DSC ノードのリストのプル サーバーに登録します。

## 手順 3: プル サーバーに必要な DSC リソースを追加する

PowerShell ギャラリーは、Azure Automation アカウントに DSC リソースをインストールするためにインストルメント化されています。必要なリソースに移動し、[Azure Automation にデプロイ] ボタンをクリックします。

![PowerShell ギャラリーの例](./media/automation-dsc-cd-chocolatey/cchoco.png)

手動による方法もあります。Windows コンピューター用の PowerShell 統合モジュールのフォルダー構造は、Azure Automation DSC プル サーバーで必要なフォルダー構造とは少し異なります。ユーザーが少し調整する必要があります。しかし、難しくはありません。リソースごとに一度行うだけです (将来的にアップグレードする場合を除く)。

-   次のように、ワークステーションに必要なモジュールをインストールします。
    -   [Windows Management Framework v5](http://aka.ms/wmf5latest) をインストールします。 
    -   `Install-Module  –ModuleName MODULENAME` (PowerShell ギャラリーからモジュールを取得します)。 
-   `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` からモジュール フォルダーを一時フォルダーにコピーします。 
-   メイン フォルダーからサンプルとドキュメントを削除します。 
-   メイン フォルダーを zip します。zip ファイルにはフォルダーとまったく同じ名前を付けます。 
-   zip ファイルを到達可能な http の場所に配置します。 
-   以下の PowerShell を実行します。

        New-AzureAutomationModule ``
            -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT ``
            -Name MODULE-NAME –ContentLink "https://STORAGE-URI/public/MODULE-NAME.zip"
        

ここに含まれる例では、cChoco と xNetworking に対してこれらの手順を実行します。

## 手順 4: プル サーバーにノード構成を追加する

初めて構成をプル サーバーにインポートしてコンパイルすることについて、特筆することはありません。構成が同じであれば、以降のすべてのインポートおよびコンパイルはまったく同じに見えます。パッケージを更新し、実稼働環境にプッシュする必要があるたびに、構成ファイル (新しいバージョンのパッケージを含む) が正しいことを確認してから、この手順を実行します。以下に、構成ファイルと PowerShell を示します。

    Configuration ISVBoxConfig 
    { 
        Import-DscResource -ModuleName cChoco 
        Import-DscResource -ModuleName xNetworking
    
        Node "isvbox" {   <— node must be named, $AllNodes.NodeName won’t work.
    
            cChocoInstaller installChoco 
            { 
                InstallDir = "C:\choco" 
            }
    
            WindowsFeature installIIS 
            { 
                Ensure="Present" 
                Name="Web-Server" 
            }
    
            xFirewall WebFirewallRule 
            { 
                Direction = "Inbound" 
                Name = "Web-Server-TCP-In" 
                DisplayName = "Web Server (TCP-In)" 
                Description = "IIS allow incoming web site traffic." 
                DisplayGroup = "IIS Incoming Traffic" 
                State = "Enabled" 
                Access = "Allow" 
                Protocol = "TCP" 
                LocalPort = "80" 
                Ensure = "Present" 
            }
    
            cChocoPackageInstaller trivialWeb 
            {            
                Name = "trivialweb" 
                Version = "1.0.0" 
                Source = “MY-NUGET-SERVER-ADDRESS” 
                DependsOn = "[cChocoInstaller]installChoco", 
                "[WindowsFeature]installIIS" 
            } 
        }    
    }

    Import-AzureAutomationDscConfiguration ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 ` 
        -Published –Force
    
    $jobData = Start-AzureAutomationDscCompilationJob ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -ConfigurationName ISVBoxConfig 
    
    $compilationJobId = $jobData.Id
    
    Get-AzureAutomationDscCompilationJob ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -Id $compilationJobId

これらの手順を実行すると、"ISVBoxConfig.isvbox" という名前の新しいノード構成が生成されます。名前は、"configurationName.nodeName" としてビルドされます。

## 手順 5: ノードにノード構成を割り当てる

この手順では、プル サーバーを照会し、登録後の新しい VM の ID を取得する必要があります。これを行うコマンドレットは、Get-AzureAutomationDscNode です。動作する可能性のあるフィルターがいくつかありますが、一般的には、名前パターンを持つ VM を取得するフィルターを介してこの出力をパイプします。単一ロールの VM のみを作成する場合は、NodeConfigurationName が ConfigureLCMforAAPull.isvbox であるすべてを取得できます。この例を使用する場合、これが新たに登録される VM の構成名になります。ID のリストがある場合、コマンドレットは以下のようになります。

    Set-AzureAutomationDscNode ` 
        -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -NodeConfigurationName ISVBoxConfig.isvbox -Id $theId

## 手順 6: この PowerShell のすべての配置場所を決める

カスタムの ARM テンプレートを記述する場合は、PowerShell スクリプトを使用します。これは Visual Studio で提供され、テンプレートを呼び出すためのものです。構成のインポート、コンパイル、VM の ID の取得などを行うコマンドレットをこのスクリプトに追加できます。GitHub の例のソースを参照してください。パッケージが更新されるたびに実行するパートについては、必要なコマンドを継続的な統合パイプラインに統合します。

## 手順 7: パッケージ メタデータを作成して管理する

パッケージ リポジトリに格納されているパッケージごとに、それを記述する nuspec が必要です。その nuspec をコンパイルし、NuGet サーバーに格納する必要があります。このプロセスについては、[こちら](http://docs.nuget.org/create/creating-and-publishing-a-package)を参照してください。MyGet.org を NuGet サーバーとして使用できます。このサービスは購入する必要がありますが、スターター SKU は無料です。NuGet.org で、プライベート パッケージの独自の NuGet サーバーをインストールする方法を参照できます。

## メモ

この例では、Azure ギャラリーにある一般的な Windows 2012 R2 イメージから VM を開始します。格納された任意のイメージから開始し、そこから DSC 構成での微調整に進むことができます。

VM でこの手法を使用する際に、ARM テンプレートや VM 拡張機能を使用する必要はありません。また、VM は、CD 管理下の Azure にある必要はありません。必要なのは、Chocolatey をインストールし、プル サーバーの場所を認識するように VM で LCM を構成することだけです。

もちろん、実稼働環境にある VM 上のパッケージを更新する場合は、更新プログラムのインストール時にローテーションからその VM を除外する必要があります。この方法はさまざまです。たとえば、Azure Load Balancer の背後にある VM では、カスタム プローブを追加できます。VM の更新時に、プローブ エンドポイントから 400 が返されるようにします。この変更に必要な微調整は構成内で行うことができます。更新が完了したら、200 が返されるように微調整して元に戻すことができます。

この例の完全なソースは、GitHub の[この Visual Studio プロジェクト](https://github.com/sebastus/ARM/tree/master/CDIaaSVM)にあります。

<!---HONumber=Oct15_HO3-->
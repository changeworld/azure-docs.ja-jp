<properties 
   pageTitle="Azure Automation DSC での構成のコンパイル | Microsoft Azure" 
   description="Desired State Configuration (DSC) 構成をコンパイルする 2 つの方法 (Azure ポータルで行う方法と Windows PowerShell を使用する方法) の概要。" 
   services="automation" 
   documentationCenter="na" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na" 
   ms.date="01/25/2016"
   ms.author="coreyp"/>
   
#Azure Automation DSC での構成のコンパイル#

Azure Automation を使用して、Desired State Configuration (DSC) 構成をコンパイルする方法は、2 とおりあります。Azure ポータルで行う方法と、Windows PowerShell を使用する方法です。次の表は、各方法の特徴を確認し、どちらの方法をどのような場合に使用するかを判断する際に役立ちます。

###Azure プレビュー ポータル###
- 対話型ユーザー インターフェイスを使用する、最も簡単な方法
- フォームに単純なパラメーター値を入力する
- ジョブの状態を簡単に追跡できる
- Azure ログオンを使用してアクセスを認証する

###Windows PowerShell###
- Windows PowerShell コマンドレットを使用してコマンド ラインから呼び出す
- 複数のステップで自動化されたソリューションに含めることができる
- 単純なパラメーター値と複雑なパラメーター値を指定する
- ジョブの状態を追跡できる
- PowerShell コマンドレットをサポートするクライアントが必要
- ConfigurationData を渡す
- 資格情報を使用する構成をコンパイルする

コンパイルの方法を決定したら、以下の各手順に従い、コンパイルを開始できます。

##Azure プレビューを使用した DSC 構成のコンパイル##

1.  Automation アカウントから、**[Configurations]** をクリックします。
2.  構成をクリックし、ブレードを開きます。
3.  **[コンパイル]** をクリックします。
4.  構成にパラメーターが含まれていない場合、コンパイルの実行を確認するメッセージが表示されます。構成にパラメーターが含まれている場合は、**[構成のコンパイル]** ブレードが開き、パラメーター値を入力できます。パラメーターの詳細については、以下の「<a href="#basic-parameters">**基本パラメーター**</a>」セクションを参照してください。
5.  **[コンパイル ジョブ]** ブレードが開き、コンパイル ジョブの状態を追跡できます。また、Azure Automation DSC プル サーバーにノード構成 (MOF 構成ドキュメント) が配置されます。

##Windows PowerShell を使用した DSC 構成のコンパイル##

[`Start-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244118.aspx) を使用して、Windows PowerShell を使用したコンパイルを開始できます。次のサンプル コードは、**SampleConfig** という名前の DSC 構成のコンパイルを開始します。

    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig" 
 
`Start-AzureRmAutomationDscCompilationJob` は、ジョブの状態を追跡するのに使用できるコンパイル ジョブ オブジェクトを返します。このコンパイル ジョブ オブジェクトを、[`Get-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244120.aspx) と共に使用してコンパイル ジョブの状態を確認したり、[`Get-AzureRmAutomationDscCompilationJobOutput`](https://msdn.microsoft.com/library/mt244103.aspx) と共に使用してストリーム (出力) を表示したりすることができます。次のサンプル コードでは、**SampleConfig** 構成のコンパイルを開始し、コンパイルが完了するまで待機した後、ストリームを表示します。
    
    $CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
    
    while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)       	
    {
    	$CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    	Start-Sleep -Seconds 3
    }
    
    $CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any 


##基本パラメーター##

DSC 構成のパラメーターの宣言 (パラメーターの種類、プロパティなど) は、Azure Automation Runbook と同じように動作します。Runbook のパラメーターの詳細については、「[Azure Automation での Runbook を開始する](automation-starting-a-runbook.md)」を参照してください。

次の例では、コンパイル時に生成される、**ParametersExample.sample** ノード構成のプロパティの値を指定するために、**FeatureName** と **IsPresent** という 2 つのパラメーターを使用します。

    Configuration ParametersExample
    {
    	param(
    		[Parameter(Mandatory=$true)]
    
    		[string] $FeatureName,
    
    		[Parameter(Mandatory=$true)]
    		[boolean] $IsPresent
    	)
    
    	$EnsureString = "Present"
    	if($IsPresent -eq $false)
    	{
    		$EnsureString = "Absent"
    	}
    
    	Node "sample"
    	{
    		WindowsFeature ($FeatureName + "Feature")
    		{
    			Ensure = $EnsureString
    			Name = $FeatureName
    		}
    	}
    }

Azure Automation DSC ポータルまたは Azure PowerShell で、基本パラメーターを使用する DSC 構成をコンパイルできます。

###ポータル###

ポータルで、**[コンパイル]** をクリックすると、パラメーター値を入力できます。

![alt text](./media/automation-dsc-compile/DSC_compiling_1.png)

###PowerShell###

PowerShell には、[ハッシュテーブル](http://technet.microsoft.com/library/hh847780.aspx)形式のパラメーターが必要です。キーはパラメーター名と一致し、値はパラメーター値と一致します。

    $Parameters = @{
    		"FeatureName" = "Web-Server"
    		"IsPresent" = $False
    }
    
    
    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters 
    

PSCredentials をパラメーターとして渡す方法の詳細については、以下の「<a href="#credential-assets">**資格情報資産**</a>」を参照してください。

##ConfigurationData##

PowerShell DSC の使用時に、**ConfigurationData** によって、環境固有の構成と構造上の構成を分離することができます。**ConfigurationData** の詳細については、[PowerShell DSC で "環境" から "物" を分離する](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx)ことに関する記事を参照してください。

>[AZURE.NOTE] Azure ポータルではなく、Azure PowerShell を使用して Azure Automation DSC でコンパイルする場合に、**ConfigurationData** を使用できます。

次の DSC 構成の例では、**$ConfigurationData** および **$AllNodes** キーワードを介して **ConfigurationData** を使用します。この例では、[**xWebAdministration** モジュール](https://www.powershellgallery.com/packages/xWebAdministration/)も必要になります。

     Configuration ConfigurationDataSample
     {
    	Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite
    
    	Write-Verbose $ConfigurationData.NonNodeData.SomeMessage 
    
    	Node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
    	{
    		xWebsite Site
    		{
    			Name = $Node.SiteName
    			PhysicalPath = $Node.SiteContents
    			Ensure   = "Present"
    		}
    	}
    }

上記の DSC 構成は PowerShell を使用してコンパイルできます。以下の PowerShell では、**ConfigurationDataSample.MyVM1** と **ConfigurationDataSample.MyVM3** という 2 つのノード構成が Azure Automation DSC プル サーバーに追加されます。

    $ConfigData = @{
    	AllNodes = @(
			@{
    			NodeName = "MyVM1"
    			Role = "WebServer"
    		},
    		@{
    			NodeName = "MyVM2"
    			Role = "SQLServer"
    		},
    		@{
    			NodeName = "MyVM3"
    			Role = "WebServer"
    
    		}
    
    	)
    
    	NonNodeData = @{
    		SomeMessage = "I love Azure Automation DSC!"
    
    	}
    
    } 
    
    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ConfigurationDataSample" -ConfigurationData $ConfigData


##資産##

資産の参照方法は、Azure Automation DSC 構成や Runbook と同じです。詳細については、以下を参照してください。

- [証明書](automation-certificates.md)
- [接続](automation-connections.md)
- [資格情報](automation-credentials.md)
- [変数](automation-variables.md)

###資格情報資産###
Azure Automation の DSC 構成では **Get-AzureRmAutomationCredential** を使用して資格情報資産を参照できますが、必要に応じて、パラメーターを使用して資格情報資産を渡すこともできます。構成が **PSCredential** 型のパラメーターを受け取る場合は、パラメーターの値として、PSCredential オブジェクトではなく、Azure Automation 資格情報資産の文字列名を渡す必要があります。バックグラウンドで、この名前の Azure Automation 資格情報資産が取得され、構成に渡されます。

ノード構成 (MOF 構成ドキュメント) で資格情報を安全に保持するには、ノード構成 MOF ファイルで資格情報を暗号化する必要があります。Azure Automation ではさらに、MOF ファイル全体を暗号化します。ただし、現時点では、ノード構成 MOF 作成時に資格情報をプレーンテキストで出力することを許可するように PowerShell DSC に指定する必要があります。PowerShell DSC は、コンパイル ジョブによって生成された MOF ファイル全体を Azure Automation が暗号化することを認識していないためです。

<a href="#configurationdata">**ConfigurationData**</a> を使用して、ノード構成 MOF 作成時に資格情報をプレーンテキストで出力することを許可するように PowerShell DSC に指定することができます。DSC 構成に表示され、資格情報を使用する各ノード ブロックの名前に対して、**ConfigurationData** を使用して `PSDscAllowPlainTextPassword = $true` を渡す必要があります。

次の例は、Automation 資格情報資産を使用する DSC 構成の例です。

    Configuration CredentialSample
    {
       $Cred = Get-AzureRmAutomationCredential -Name "SomeCredentialAsset"
    
    	Node $AllNodes.NodeName
    	{ 
    		File ExampleFile
    		{ 
    			SourcePath = "\\Server\share\path\file.ext" 
    			DestinationPath = "C:\destinationPath" 
    			Credential = $Cred 
       		}
    	}
    }

上記の DSC 構成は PowerShell を使用してコンパイルできます。以下の PowerShell では、**CredentialSample.MyVM1** と **CredentialSample.MyVM2** という 2 つのノード構成が Azure Automation DSC プル サーバーに追加されます。


    $ConfigData = @{
    	AllNodes = @(
    		@{
    			NodeName = "*"
    			PSDscAllowPlainTextPassword = $True
    		},
    		@{
    			NodeName = "MyVM1"
    		},
    		@{
    			NodeName = "MyVM2"
    		}
    	)
    }
    
    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "CredentialSample" -ConfigurationData $ConfigData

<!---HONumber=AcomDC_0921_2016-->
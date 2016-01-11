<properties pageTitle="PowerShell を使用して Windows が実行されている Azure Virtual Machine で診断を有効にする | Microsoft Azure" description="PowerShell を使用して Windows を実行している Azure Virtual Machine で診断を有効にする方法について説明します" services="virtual-machines" documentationCenter="" authors="sbtron" manager="" editor="""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="saurabh"/>


# PowerShell を使用して Windows が実行されている Azure Virtual Machine で診断を有効にする

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure 診断拡張機能を使用して、Windows を実行している Azure Virtual Machine からアプリケーション ログやパフォーマンス カウンターなどの診断データを収集できます。この記事では、PowerShell を使用して Virtual Machine の Azure 診断拡張機能を有効にする方法について説明します。この記事で求められる前提条件については、[Azure PowerShell のインストールおよび構成方法](powershell-install-configure.md)に関するページを参照してください。

## リソース マネージャー デプロイメント モデルを使用して Virtual Machine で Azure 診断拡張機能を有効にする

リソース マネージャー デプロイメント モデルで、リソース マネージャー テンプレートに拡張機能構成を追加して、Windows 仮想マシンを作成するときに診断拡張機能を有効にすることができます。「[Azure リソース マネージャー テンプレートを使用して監視および診断を含む Windows 仮想マシンを登録する](virtual-machines-extensions-diagnostics-windows-template.md)」を参照してください。

リソース マネージャー デプロイメント モデルを使用して作成した既存の仮想マシンで Azure 診断拡張機能を有効にするには、以下のように [Set-AzureRMVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt603499.aspx) PowerShell コマンドレットを使用できます。


	$vm_resourcegroup = "myvmresourcegroup"
	$vm_name = "myvm"
	$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"
	
	Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path 


*$diagnosticsconfig\_path* は、以下の[サンプル](#sample-diagnostics-configuration)のように、xml に診断構成を含むファイルのパスです。

診断構成ファイルにストレージ アカウント名を設定した **StorageAccount** を指定すると、*Set-AzureRMVMDiagnosticsExtension* スクリプトによって、そのストレージ アカウントに診断データを送信するように診断拡張機能が自動的に設定されます。拡張機能が動作するには、ストレージ アカウントが仮想マシンと同じサブスクリプションに属する必要があります。

診断構成で **StorageAccount** を指定しなかった場合、*StorageAccountName* パラメーターをコマンドレットに渡す必要があります。*StorageAccountName* パラメーターを指定すると、診断構成ファイルに指定されたストレージ アカウントではなく、常にパラメーターに指定されたストレージ アカウントがコマンドレットに使用されます。

診断ストレージ アカウントが仮想マシンと異なるサブスクリプションの場合、コマンドレットに対して明示的に *StorageAccountName* および *StorageAccountKey* パラメーターを渡す必要があります。診断拡張機能が有効で、診断ストレージ アカウントが同じサブスクリプションに属する場合、コマンドレットによってキー値が自動的にクエリされ、設定されるので、*StorageAccountKey* パラメーターは不要です。ただし、診断ストレージ アカウントが別のサブスクリプションに属する場合、コマンドレットからキーを自動的に取得できない可能性があるので、*StorageAccountKey* パラメーターで明示的にキーを指定する必要があります。

	Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key	

VM で Azure 診断拡張機能を有効にすると、[Get-AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603678.aspx) コマンドレットを使用して現在の設定を取得できます。

	Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

コマンドレットから返される *PublicSettings* には、base64 でエンコードされた形式の xml 構成が含まれます。xml を読み取るには、デコードする必要があります。
	
	$publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
	$encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
	$xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
	Write-Host $xmlconfig
 
[Remove-AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603782.aspx) コマンドレットを使用すると、VM から診断拡張機能を削除できます。
  
## クラシック デプロイメント モデルを使用して Virtual Machine で Azure 診断拡張機能を有効にする

[Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) コマンドレットを使用すると、クラシック デプロイメント モデルを使用して作成した仮想マシンで Azure 診断拡張機能を有効にすることができます。次の例は、Azure 診断拡張機能を有効にしたクラシック デプロイメント モデルを使用して、新しい仮想マシンを作成する方法を示しています。

	$VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
	$VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
	$VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
	New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

既存の仮想マシン (クラシック) に Azure 診断拡張機能を有効にするには、まず [Get-AzureVM](https://msdn.microsoft.com/library/mt589152.aspx) コマンドレットを使用して仮想マシン構成を取得します。次に、[Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) コマンドレットを使用して、Azure 診断拡張機能を含めるように仮想マシン構成を更新します。最後に、[Update-AzureVM](https://msdn.microsoft.com/library/mt589121.aspx) を使用して、更新された構成を仮想マシンに適用します。

	$VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
	$VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
	Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## サンプルの診断構成

上記のスクリプトを使用した診断パブリック構成には、以下の xml を使用できます。このサンプル構成では、多様なパフォーマンス カウンターを診断ストレージ アカウントに転送します。また、Windows イベント ログのアプリケーション、セキュリティ、およびシステム チャネルのエラーと、診断インフラストラクチャ ログのエラーも転送されます。

以下を含めるように構成を更新する必要があります。

- 仮想マシンのリソース ID を使用して、**Metrics** 要素の *resourceID* 属性を更新する必要があります。 
	- リソース ID は、"/subscriptions/{*VM があるサブスクリプションのサブスクリプション ID*}/resourceGroups/{*VM のリソース グループ名*}/providers/Microsoft.Compute/virtualMachines/{*VM 名*}" というパターンを使用して構築できます。 
	- たとえば、VM が実行されているサブスクリプションのサブスクリプション ID が **11111111-1111-1111-1111-111111111111**、リソース グループのリソース グループ名は **MyResourceGroup**、VM 名が **MyWindowsVM** の場合、*resourceID* は次の値になります。
	 
		```
		<Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
		```
	- パフォーマンス カウンターとメトリックの構成に基づいてメトリックを生成する方法については、[ストレージの WAD メトリック テーブル](virtual-machines-extensions-diagnostics-windows-template.md#wadmetrics-tables-in-storage)の項を参照してください。

- 診断ストレージ アカウントの名前を使用して、**StorageAccount** 要素を更新する必要があります。
 
	```
	<?xml version="1.0" encoding="utf-8"?>
	<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <WadCfg>
	      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
	        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
	        <PerformanceCounters scheduledTransferPeriod="PT1M">
	      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="CPU utilization" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="CPU privileged time" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="CPU user time" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
	        <annotation displayName="CPU frequency" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Processes" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Threads" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Handles" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Memory usage" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory available" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory committed" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory commit limit" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory paged pool" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
	        <annotation displayName="Memory non-paged pool" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk active time" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk active read time" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk active write time" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
	        <annotation displayName="Disk operations" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
	        <annotation displayName="Disk read operations" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
	        <annotation displayName="Disk write operations" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
	        <annotation displayName="Disk speed" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
	        <annotation displayName="Disk read speed" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
	        <annotation displayName="Disk write speed" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk average queue length" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk average read queue length" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk average write queue length" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
	        <annotation displayName="Disk free space (percentage)" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	      <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
	        <annotation displayName="Disk free space (MB)" locale="en-us"/>
	      </PerformanceCounterConfiguration>
	    </PerformanceCounters>
		<Metrics resourceId="(Update with resource ID for the VM)" >
	        <MetricAggregation scheduledTransferPeriod="PT1H"/>
	        <MetricAggregation scheduledTransferPeriod="PT1M"/>
	    </Metrics>
	    <WindowsEventLog scheduledTransferPeriod="PT1M">
	      <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
	      <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
	      <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
	    </WindowsEventLog>
	      </DiagnosticMonitorConfiguration>
	    </WadCfg>
	    <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
	</PublicConfig>
	```

## 次のステップ 
- Azure 診断と他の手法を使用した問題のトラブルシューティングに関するその他のガイダンスについては、「[Azure Cloud Services および Virtual Machines の診断機能](cloud-services-dotnet-diagnostics.md)」を参照してください。
- [診断構成スキーマ](https://msdn.microsoft.com/library/azure/mt634524.aspx)に関するページでは、診断拡張機能の各種 xml 構成オプションについて説明しています。

<!---HONumber=AcomDC_1223_2015-->
<properties 
	pageTitle="PowerShell を使用した Azure Cloud Services での診断の有効化 | Microsoft Azure" 
	description="PowerShell を使用して Cloud Services の診断を有効にする方法について説明します。" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="sbtron" 
	manager="" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="saurabh"/>


# PowerShell を使用した Azure Cloud Services での診断の有効化

Azure 診断拡張機能を使用して、Cloud Service からアプリケーション ログやパフォーマンス カウンターなどの診断データを収集できます。この記事では、PowerShell を使用して Cloud Service の Azure 診断拡張機能を有効にする方法について説明します。この記事で求められる前提条件については、[Azure PowerShell のインストールおよび構成方法](powershell-install-configure.md)に関するページを参照してください。

## Cloud Service のデプロイの一環としての診断拡張機能の有効化

この方法は、診断拡張機能を有効にすることができる、継続的インテグレーションの種類のシナリオに適しています。*ExtensionConfiguration* パラメーターを [New-AzureDeployment](https://msdn.microsoft.com/library/azure/mt589089.aspx) コマンドレットに渡すことで、Cloud Service のデプロイの一環として診断拡張機能を有効にできます。*ExtensionConfiguration* パラメーターは、[New-AzureServiceDiagnosticsExtensionConfig](https://msdn.microsoft.com/library/azure/mt589168.aspx) コマンドレットを使用して作成できるさまざまな診断構成を受け取ります。

次の例は、診断構成がそれぞれ異なる WebRole と WorkerRole を含む Cloud Service の診断を有効にする方法を示しています。

	$service_name = "MyService"
	$service_package = "CloudService.cspkg"
	$service_config = "ServiceConfiguration.Cloud.cscfg"
	$diagnostics_storagename = "myservicediagnostics"
	$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
	$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

	$primary_storagekey = (Get-AzureStorageKey -StorageAccountName "$diagnostics_storagename").Primary
	$storage_context = New-AzureStorageContext -StorageAccountName $diagnostics_storagename -StorageAccountKey $primary_storagekey

	$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -Storage_context $storageContext -DiagnosticsConfigurationPath $webrole_diagconfigpath
	$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -StorageContext $storage_context -DiagnosticsConfigurationPath $workerrole_diagconfigpath
	  
	 
	New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig) 



## 既存の Cloud Service での診断拡張機能の有効化

[Set-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589140.aspx) コマンドレットを使用して、既に実行されている Cloud Service で診断を有効にできます。


	$service_name = "MyService"
	$diagnostics_storagename = "myservicediagnostics"
	$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
	$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
	$primary_storagekey = (Get-AzureStorageKey -StorageAccountName "$diagnostics_storagename").Primary
	$storage_context = New-AzureStorageContext -StorageAccountName $diagnostics_storagename -StorageAccountKey $primary_storagekey
 
	Set-AzureServiceDiagnosticsExtension -StorageContext $storage_context -DiagnosticsConfigurationPath $webrole_diagconfigpath -ServiceName $service_name -Slot Production -Role "WebRole" 
	Set-AzureServiceDiagnosticsExtension -StorageContext $storage_context -DiagnosticsConfigurationPath $workerrole_diagconfigpath -ServiceName $service_name -Slot Production -Role "WorkerRole"
 

## 診断拡張機能の現在の構成の取得
Cloud Service の現在の診断構成を取得するには、[Get-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589204.aspx) コマンドレットを使用します。
	
	Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"

## 診断拡張機能の削除
Cloud Service で診断を無効にするには、[Remove-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589183.aspx) コマンドレットを使用します。

	Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"

*Role* パラメーターを指定せずに *Set-AzureServiceDiagnosticsExtension* または *New-AzureServiceDiagnosticsExtensionConfig* を使用して診断拡張機能を有効にした場合は、*Role* パラメーターを指定せずに *Remove-AzureServiceDiagnosticsExtension* を使用して拡張機能を削除できます。拡張機能を有効にするときに *Role* パラメーターを使用した場合は、拡張機能を削除するときにもこのパラメーターを使用する必要があります。

個々のロールから診断拡張機能を削除するには、次のコマンドを実行します。

	Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"


## 次のステップ

- Azure 診断と他の手法を使用した問題のトラブルシューティングに関するその他のガイダンスについては、「[Azure のクラウド サービスおよび仮想マシンの診断機能](cloud-services-dotnet-diagnostics.md)」をご覧ください。
- [診断構成スキーマ](https://msdn.microsoft.com/library/azure/dn782207.aspx)に関するページでは、診断拡張機能の各種 xml 構成オプションについて説明しています。
- Virtual Machines の診断拡張機能を有効にする方法については、「[Create a Windows Virtual machine with monitoring and diagnostics using Azure Resource Manager Template (Azure リソース マネージャー テンプレートを使用した監視および診断機能を備えた Windows 仮想マシンの作成)](virtual-machines-extensions-diagnostics-windows-template.md)」をご覧ください。  

<!---HONumber=Nov15_HO4-->
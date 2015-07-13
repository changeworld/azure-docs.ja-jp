<properties 
	pageTitle="弾力性データベース ジョブ ツールのアンインストール方法" 
	description="弾力性データベース ジョブ ツールのアンインストール方法" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="sidneyh"/>

# 弾力性データベース ジョブ コンポーネントのインストール方法

弾力性データベース ジョブ サービスのインストール中にエラーが発生した場合は、サービスのリソース グループを削除します。

## サービス コンポーネントのアンインストール

1. [Azure プレビュー ポータル](https://ms.portal.azure.com/)を開きます。
2. 弾力性ジョブを含むサブスクリプションに移動します。
3. **[参照]** をクリックして、**[リソース グループ]** をクリックします。
4. "__ElasticDatabaseJob" という名前のリソース グループを選択します。
5. リソース グループを削除します。

この PowerShell スクリプトを使用することもできます。

1. [Microsoft Azure PowerShell ウィンドウ](../powershell-install-configure.md) を起動します。 
2. PowerShell SDK 0.8.10 以降のバージョンを使用していることを確認します。
3. スクリプトを実行します。

		$ResourceGroupName = "__ElasticDatabaseJob"
		Switch-AzureMode AzureResourceManager
		
		$resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
		if(!$resourceGroup)
		{
		    Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job is uninstalled."
		    return
		}
		
		Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
		Remove-AzureResourceGroup -Name $ResourceGroupName -Force
		Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job is now uninstalled."

## 次のステップ

弾力性データベース ジョブを再インストールするには、「[弾力性データベース ジョブ サービスのインストール](sql-database-elastic-jobs-service-installation.md)」をご覧ください。

弾力性ジョブ サービスの概要については、「[弾力性データベース ジョブの概要](sql-database-elastic-jobs-overview.md)」をご覧ください。

<!--Image references-->
[1]: ./media/sql-database-elastic-job-uninstall/
 

<!---HONumber=62-->
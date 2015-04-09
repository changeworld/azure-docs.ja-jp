<properties 
 pageTitle="Azure PowerShell を使用した Azure Redis Cache の管理" 
 description="Azure PowerShell を使用して Azure Redis Cache の管理タスクを実行する方法について説明します。" 
 services="redis-cache" 
   documentationCenter="" 
   authors="Rick-Anderson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="all"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="multiple" 
   ms.date="03/06/2015"
   ms.author="riande"/>

# Azure PowerShell を使用した Azure Redis Cache の管理

このチュートリアルでは、Azure Redis Cache を作成、更新、および削除するためのクイック スタートについて説明しています。

## 前提条件 ##

リソース マネージャーで Windows PowerShell を使用するには、以下が必要です。

- Windows PowerShell、Version 3.0 または 4.0。Windows PowerShell のバージョンを確認するには、「`$PSVersionTable`」と入力し、`PSVersion` の値が 3.0 または 4.0 であることを確認します。互換性のあるバージョンをインストールするには、「[Windows Management Framework 3.0 ](http://www.microsoft.com/download/details.aspx?id=34595)」または「[Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)」を参照してください。
	
- Azure PowerShell バージョン 0.8.0 以降。最新バージョンをインストールして、Azure サブスクリプションに関連付けるには、[Microsoft Azure PowerShell のインストールおよび構成方法](powershell-install-configure.md)に関するページを参照してください。

このチュートリアルは、Windows PowerShell を初めて使うユーザーを対象としています。Windows PowerShell の詳細については、[Windows PowerShell の概要](http://technet.microsoft.com/library/hh857337.aspx)に関するページを参照してください。

このチュートリアルに表示されているすべてのコマンドレットの詳細なヘルプを取得するには、Get-Help コマンドレットを使用します。 

	Get-Help <cmdlet-name> -Detailed

たとえば、Add-AzureAccount コマンドレットについてのヘルプを確認するには、次のように入力します。

	Get-Help Add-AzureAccount -Detailed

## Redis Cache 用の簡単な Azure PowerShell スクリプト  ##

次のスクリプトでは、Azure Redis Cache を作成、更新、および削除する方法を示します。

		# Redis cache operations require mode set to AzureResourceManager.
		Switch-AzureMode AzureResourceManager
		$VerbosePreference = "Continue" 
		
		# Create a new cache.
		$cacheName = "MovieCache91117"
		$location = "West US"
		$resourceGroupName = "Default-Web-WestUS"
		
		$movieCache = New-AzureRedisCache -Location $location -Name $cacheName  -ResourceGroupName $resourceGroupName -Size 250MB -Sku Basic
		
		# Wait until the Cache is provisioned.
		
		for ($i = 0; $i -le 60; $i++)
		{
		    Start-Sleep -s 30
			$cacheGet = Get-AzureRedisCache -ResourceGroupName $resourceGroupName -Name $cacheName
		    if ([string]::Compare("succeeded", $cacheGet[0].ProvisioningState, $True) -eq 0)
		    {       
		        break
		    }
		    If($i -eq 60)
		    {
		        exit
		    }
		}
		
		# Update the access keys
		
		Write-Verbose "PrimaryKey: $($movieCache.PrimaryKey)"
		New-AzureRedisCacheKey -KeyType "Primary" -Name $cacheName  -ResourceGroupName $resourceGroupName -Force
		$cacheKeys = Get-AzureRedisCacheKey -ResourceGroupName $resourceGroupName  -Name $cacheName         
		Write-Verbose "PrimaryKey: $($cacheKeys.PrimaryKey)"
		
		# Use Set-AzureRedisCache to set Redis cache updatable parameters.
		# Set the memory policy to Least Recently Used.
		
		Set-AzureRedisCache -MaxMemoryPolicy AllKeysLRU -Name $cacheName -ResourceGroupName $resourceGroupName
		
		# Delete the cache.
		
		Remove-AzureRedisCache -Name $movieCache.Name -ResourceGroupName $movieCache.ResourceGroupName  -Force 

## 次のステップ
Microsoft Azure PowerShell の使用の詳細については、次を参照してください。
 
- [Azure Resource Manager コマンドレット](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409):AzureResourceManager モジュールのコマンドレットを使用する方法について説明します。
- [リソース グループを使用した Azure リソースの管理](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups):Azure 管理ポータルでリソース グループを作成および管理する方法について説明します。
- [Azure のブログ](http://blogs.msdn.com/windowsazure): Azure の新機能について説明します。
- [Windows PowerShell のブログ](http://blogs.msdn.com/powershell): Windows PowerShell の新機能について説明します。
- ["Hey, Scripting Guy!"ブログ](http://blogs.technet.com/b/heyscriptingguy/): 実際のヒントとテクニックを Windows PowerShell コミュニティから取得します。




<!--HONumber=49-->
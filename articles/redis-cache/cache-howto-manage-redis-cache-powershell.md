<properties
 pageTitle="Azure PowerShell を使用した Azure Redis Cache の管理 | Microsoft Azure"
 description="Azure PowerShell を使用して Azure Redis Cache の管理タスクを実行する方法について説明します。"
 services="redis-cache"
   documentationCenter=""
   authors="Rick-Anderson"
   manager="wpickett"
   editor="v-lincan"/>

<tags
   ms.service="cache"
   ms.devlang="all"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="multiple"
   ms.date="12/02/2015"
   ms.author="riande"/>

# Azure PowerShell を使用した Azure Redis Cache の管理

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [Azure CLI](cache-manage-cli.md)

このトピックでは、Azure Redis Cache を作成、更新、削除する方法を説明します。

## 前提条件 ##

Azure リソース マネージャーで Windows PowerShell を使用するには、以下が必要です。

- Windows PowerShell バージョン 3.0 または 4.0。Windows PowerShell のバージョンを調べるには、`$PSVersionTable` と入力して、`PSVersion` の値が 3.0 か 4.0 かを確認します。互換バージョンをインストールするには、「[Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595)」または「[Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)」をご覧ください。

- Azure PowerShell Version 0.8.0 以降。最新バージョンをインストールして、Azure サブスクリプションに関連付けるには、「[How to install and configure Azure PowerShell (Azure PowerShell のインストールと構成の方法)](../powershell-install-configure.md)」をご覧ください。

このチュートリアルは、Windows PowerShell を初めて使うユーザーを対象としています。Windows PowerShell の詳細については、「[Getting Started with Windows PowerShell (Windows PowerShell の概要)](http://technet.microsoft.com/library/hh857337.aspx)」を参照してください。

このチュートリアルに表示されているコマンドレットの詳しいヘルプを確認には、Get-Help コマンドレットを使用します。

	Get-Help <cmdlet-name> -Detailed

たとえば、Add-AzureAccount コマンドレットについてのヘルプを確認するには、次のように入力します。

	Get-Help Add-AzureAccount -Detailed

## Redis Cache 用の簡単な Azure PowerShell スクリプト  ##

次のスクリプトでは、Azure Redis Cache を作成、更新、および削除する方法を示します。

		
		$VerbosePreference = "Continue"

    	# Create a new cache with date string to make name unique.
		$cacheName = "MovieCache" + $(Get-Date -Format ('ddhhmm'))
		$location = "West US"
		$resourceGroupName = "Default-Web-WestUS"
		
		$movieCache = New-AzureRedisCache -Location $location -Name $cacheName  -ResourceGroupName $resourceGroupName -Size 250MB -Sku Basic
		
		# Wait until the Cache service is provisioned.
		
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
		
		# Update the access keys.
		
		Write-Verbose "PrimaryKey: $($movieCache.PrimaryKey)"
		New-AzureRedisCacheKey -KeyType "Primary" -Name $cacheName  -ResourceGroupName $resourceGroupName -Force
		$cacheKeys = Get-AzureRedisCacheKey -ResourceGroupName $resourceGroupName  -Name $cacheName
		Write-Verbose "PrimaryKey: $($cacheKeys.PrimaryKey)"
		
		# Use Set-AzureRedisCache to set Redis cache updatable parameters.
		# Set the memory policy to Least Recently Used.
		
		Set-AzureRedisCache -Name $cacheName -ResourceGroupName $resourceGroupName -RedisConfiguration @{"maxmemory-policy" = "AllKeys-LRU"}
		
		# Delete the cache.
		
		Remove-AzureRedisCache -Name $movieCache.Name -ResourceGroupName $movieCache.ResourceGroupName  -Force

## 次のステップ

Azure での Windows PowerShell の使用の詳細については、次のリソースを参照してください。

- [MSDN 上の Azure Redis Cache コマンドレットのドキュメント](https://msdn.microsoft.com/library/azure/mt634513.aspx)
- [Azure リソース マネージャー コマンドレットに関するページ](http://go.microsoft.com/fwlink/?LinkID=394765): AzureResourceManager モジュールのコマンドレットを使用する方法について説明します。
- [リソース グループを使用した Azure リソースの管理](../azure-portal/resource-group-portal.md): Azure プレビュー ポータルでリソース グループを作成して管理する方法について説明します。
- [Azure blog (Azure のブログ)](http://blogs.msdn.com/windowsazure): Azure の新機能について説明します。
- [Windows PowerShell blog (Windows PowerShell ブログ)](http://blogs.msdn.com/powershell): Windows PowerShell の新機能について説明します。
- ["Hey, Scripting Guy!" ブログ](http://blogs.technet.com/b/heyscriptingguy/): 実践で使えるヒントとテクニックを Windows PowerShell コミュニティから得られます。

<!---HONumber=AcomDC_1203_2015-->
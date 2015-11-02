<properties
   pageTitle="Azure PowerShell コマンドを使用して空のクラウド サービス コンテナーを作成する方法"
   description="この記事では、PowerShell スクリプトを使用して、クラウド サービス コンテナーを作成し、クラウド サービスに関連する管理操作を実行する方法について説明します。"
   services="cloud-services"
   documentationCenter=".net"
   authors="cawaMS"
   manager="paulyuk" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na"
   ms.date="10/14/2015"
   ms.author="cawa"/>

# Azure PowerShell コマンドを使用して空のクラウド サービス コンテナーを作成する方法
1. [Azure PowerShell のダウンロード ページ](http://go.microsoft.com/?linkid=9811175&clcid=0x409)から Microsoft Azure PowerShell コマンドレットをインストールします。Azure PowerShell コマンドレットをインストールして、Azure サブスクリプションに接続する手順の詳細については、「[Azure PowerShell をインストールして構成する方法](../powershell-install-configure.md)」を参照してください。

2. **New-AzureService** は、空のクラウド サービス コンテナーを作成するためのコマンドレットです。

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

   コマンドレットを呼び出す例: ```
New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
```

3. Azure クラウド サービスを作成する方法については、次を実行します: ```
Get-help New-AzureService
```

4. 次のステップ:

   - クラウド サービス デプロイメントの管理については、[Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx)、[Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx)、[Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx) の各コマンドの説明を参照してください。「[クラウド サービスの構成方法](cloud-services-how-to-configure.md)」も参照してください。

    - Azure にクラウド サービス プロジェクトを発行する方法については、「[Azure でのクラウド サービスの継続的な配信](cloud-services-dotnet-continuous-delivery.md)」の **PublishCloudService.ps1** コード サンプルを参照してください。
 

<!---HONumber=Oct15_HO4-->
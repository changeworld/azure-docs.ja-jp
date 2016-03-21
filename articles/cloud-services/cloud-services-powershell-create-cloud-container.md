<properties
   pageTitle="Azure PowerShell コマンドを使用して空のクラウド サービス コンテナーを作成する方法 | Microsoft Azure"
   description="この記事では、PowerShell スクリプトを使用してクラウド サービス コンテナーを作成し、クラウド サービスに関連する管理操作を実行する方法について説明します。"
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
   ms.date="02/09/2016"
   ms.author="cawa"/>

# Azure PowerShell コマンドを使用して空のクラウド サービス コンテナーを作成する
この記事では、Azure PowerShell コマンドレットを使用して Cloud Services コンテナーを簡単に作成する方法について説明します。次の手順に従ってください。

1. [Azure PowerShell のダウンロード](http://aka.ms/webpi-azps) ページから Microsoft Azure PowerShell コマンドレットをインストールします。
2. PowerShell コマンド プロンプトを開きます。
3. [Add-AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) を使用してサインインします。

    > [AZURE.NOTE] Azure PowerShell コマンドレットをインストールして、Azure サブスクリプションに接続する手順の詳細については、「[Azure PowerShell をインストールして構成する方法](../powershell-install-configure.md)」を参照してください。

4. **New-AzureService** コマンドレットを使用し、空の Azure クラウド サービス コンテナーを作成します。

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

5. この例に従い、コマンドレットを呼び出します。 ```
New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
```

Azure クラウド サービスを作成する方法については、次を実行します: ```
Get-help New-AzureService
```

### 次のステップ

 * クラウド サービス デプロイメントの管理については、[Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx)、[Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx)、[Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx) の各コマンドの説明を参照してください。詳細については、[クラウド サービスを構成する方法](cloud-services-how-to-configure.md)に関するページも参考になります。

 * Azure にクラウド サービス プロジェクトを発行する方法については、「[Azure でのクラウド サービスの継続的な配信](cloud-services-dotnet-continuous-delivery.md)」の **PublishCloudService.ps1** コード サンプルを参照してください。

<!---HONumber=AcomDC_0211_2016-->
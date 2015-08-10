<properties 
	pageTitle="ExpressRoute を操作するためのネットワーク構成の詳細" 
	description="App Service 環境を ExpressRoute 回線に接続された Virtual Networks 内で実行するためのネットワーク構成の詳細です。" 
	services="app-service\web" 
	documentationCenter="" 
	authors="stefsch" 
	manager="nirma" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="stefsch"/>

# ExpressRoute を使用した App Service 環境のネットワーク構成の詳細 

## 概要 ##
顧客は、[Azure ExpressRoute][ExpressRoute] 回線を自分の仮想ネットワーク インフラストラクチャに接続することで、オンプレミスのネットワークを Azure に拡張できます。この[仮想ネットワーク][virtualnetwork] インフラストラクチャのサブネットの中に App Service 環境を作成できます。App Service 環境で実行されるアプリは、ExpressRoute 接続でのみアクセスできる、バックエンド リソースに対する安全な接続を確立できます。

## 必要なネットワーク接続 ##
ExpressRoute に接続された仮想ネットワークでは最初は満たされていない場合がある App Service 環境のネットワーク接続要件があります。

App Service 環境が正常に機能するには、次のすべてを満たす必要があります。


-  App Service 環境と同じリージョンに配置されている Azure Storage リソースと SQL DB リソースの両方に対する発信ネットワーク接続。このネットワーク パスは、社内のプロキシを通過することはできません。それを行うと、発信ネットワーク トラフィックの有効な NAT アドレスが変更される可能性があるためです。Azure Storage エンドポイントと SQL DB エンドポイント向けの App Service 環境の発信ネットワーク トラフィックの NAT アドレスを変更すると、接続エラーが発生します。
-  仮想ネットワークの DNS 構成は、Azure が管理する次のドメイン内のエンドポイントを解決できる必要があります。**.file.core.windows.net\*、**.blob.core.windows.net\*、**.database.windows.net\*。-  仮想ネットワークの DNS 構成は、App Service 環境の作成時だけではなく、App Service 環境の再構成とスケーリングの変更を行っている間も、安定を維持する必要があります。   
-  この[記事][requiredports]の説明に従って、App Service 環境の必要なポートへの着信ネットワーク アクセスを許可する必要があります。

DNS 要件は、仮想ネットワークの有効な DNS 構成を保証することによって満たすことができます。

着信ネットワーク アクセスの要件は、この[記事][requiredports]の説明に従って、必要なアクセスを許可する[ネットワーク セキュリティ グループ][NetworkSecurityGroups]を App Service 環境のサブネットに対して構成することによって満たすことができます。

## App Service 環境の発信ネットワーク接続を有効にする##
既定では、新しく作成された ExpressRoute 回線は、発信インターネット接続を許可する既定のルートをアドバタイズします。この構成によって、App Service環境は、他の Azure エンドポイントに接続できます。

ただし、顧客の一般的な構成では、発信インターネット トラフィックを強制的に顧客のプロキシ/ファイアウォール インフラストラクチャにフローさせる独自の既定のルートを定義しています。このトラフィック フローでは、発信トラフィックはオンプレミスでブロックされるか、Azure エンドポイントではもはや有効ではない、認識できないアドレス セットに NAT 処理されるため、App Service 環境では接続は必ず切断されます。

解決策は、App Service 環境を含むサブネットに、1 つ (以上) のユーザー定義ルート (UDR) を定義することです。UDR は、既定のルートに優先するサブネット固有のルートを定義します。

ユーザー定義ルートの背景情報については、この[概要][UDROverview]を参照してください。

ユーザー定義ルートの作成と成の詳細については、この[ハウツー ガイド][UDRHowTo]を参照してください。

## App Service 環境のサンプル UDR 構成 ##

**前提条件**

1. [Azure ダウンロード ページ][AzureDownloads](2015 年 6 月以降) から最新の Azure Powershell をインストールします。[コマンド ライン ツール] の [Windows Powershell] の中に、最新の Powershell コマンドレットをインストールする [インストール] リンクがあります。

2. App Service 環境が独占的に使用する一意のサブネットを作成することをお勧めします。これにより、サブネットに適用される UDR で、App Service 環境用の発信トラフィックのみが開くことが保証されます。
3. **重要**: App Service 環境は、以下の構成手順が**終了するまで**デプロイしないでください。これにより、App Service 環境をデプロイする前に、発信ネットワーク接続を使用できることを確認できます。

**手順 1: 名前付きのルート テーブルを作成する**

次のスニペットは、Azure の米国西部リージョンに "DirectInternetRouteTable" という名前のルート テーブルを作成します。

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**手順 2: ルーティング テーブルに 1 つ以上のルートを作成する**

発信インターネット アクセスを有効にするために、ルート テーブルに 1 つ以上のルートを追加する必要があります。次の例では、米国西部リージョンで使用される可能性があるすべての Azure アドレスに対応できるルートを追加しています。

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 1' -AddressPrefix 23.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 2' -AddressPrefix 40.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 3' -AddressPrefix 65.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 4' -AddressPrefix 104.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 5' -AddressPrefix 137.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 6' -AddressPrefix 138.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 7' -AddressPrefix 157.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 8' -AddressPrefix 168.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 9' -AddressPrefix 191.0.0.0/8 -NextHopType Internet


Azure で使用される包括的な最新の CIDR 範囲の一覧については、すべての範囲を含む Xml ファイルを [Microsoft ダウンロード センター][DownloadCenterAddressRanges]からダウンロードできます。

**注:** 今後、CIDR の略語 0.0.0.0/0 を *AddressPrefix* パラメーターで使用可能になる予定です。この略語は、"すべてのインターネット アドレス" に相当します。現時点では、開発者は、App Service 環境がデプロイされるリージョンで使用される可能性があるすべての Azure のアドレス範囲をカバーする、CIDR 範囲の幅広いセットを使用する必要があります。

**手順 3: App Service 環境が含まれるサブネットにルート テーブルを関連付ける**

最後の構成手順は、App Service 環境がデプロイされるサブネットにルート テーブルを関連付けることです。次のコマンドは、"DirectInternetRouteTable" を、最終的に App Service 環境を含む "ASESubnet" に関連付けます。

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**手順 4: 最後の手順**

ルート テーブルをサブネットにバインドしたら、まずテストを行って、意図した効果が出ていることを確認することを勧めします。たとえば、仮想マシンをサブネットにデプロイし、以下の点を確認します。


- Azure エンドポイントへの発信トラフィックが ExpressRoute 回線をフローしていない。
- Azure エンドポイントの DNS ルックアップが正しく解決されている。 

上記の手順を確認した後、App Service 環境の作成に進むことができます。

## 使用の開始

App Service 環境の使用を開始するには、「[App Service 環境の概要][IntroToAppServiceEnvironment]」を参照してください。

Azure App Service プラットフォームの詳細については、[Azure App Service][AzureAppService] に関するページを参照してください。

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: http://azure.microsoft.com/downloads/
[DownloadCenterAddressRanges]: http://www.microsoft.com/ja-jp/download/details.aspx?id=41653
[NetworkSecurityGroups]: https://msdn.microsoft.com/library/azure/dn848316.aspx
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
 

<!-- IMAGES -->

<!---HONumber=July15_HO5-->
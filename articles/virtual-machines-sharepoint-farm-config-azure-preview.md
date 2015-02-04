<properties title="SharePoint Server Farm Configuration Details" pageTitle="SharePoint サーバー ファームの構成の詳細" description="SharePoint ファームの既定の構成について説明します。" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="josephd" videoId="" scriptId="" manager="timlt"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-sharepoint" ms.devlang="na" ms.topic="article" ms.date="10/20/2014" ms.author="josephd" />


# SharePoint サーバー ファームの構成の詳細#

SharePoint サーバー ファームは、事前構成された SharePoint Server 2013 ファームを自動で作成する Microsoft Azure プレビュー ポータルの機能です。ファームの構成には次の 2 種類があります。

- 基本
- 高可用性

この後のセクションでは、ファームごとに構成の詳細を示します。

詳細については、「[SharePoint Server Farm (SharePoint サーバー ファーム)](../virtual-machines-sharepoint-farm-azure-preview/)」を参照してください。

## 基本的な SharePoint ファーム##

基本的な SharePoint ファームは、次に示す構成の 3 台の仮想マシンで構成されます。

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_Basic.png) 

構成の詳細は次のとおりです。

-	Azure サブスクリプション:初期構成中に指定されます。
-	Azure ドメイン名 (クラウド サービスとも呼ばれます):個別のドメイン名が仮想マシンごとに自動で作成されます。
-	ストレージ アカウント:初期構成中に指定されます。
-	仮想ネットワーク 	
	-   次のコマンドを入力します。クラウドのみ	
    -	アドレス空間:192.168.16.0/26    

- 仮想マシン
	-	HostNamePrefix-DC (AD DS ドメイン コントローラー)
	-	HostNamePrefix-SQL (SQL Server 2014 サーバー)
	-	HostNamePrefix-SP (SharePoint 2013 サーバー)

- ドメイン コントローラー
	-	ホスト名プレフィックス:初期構成中に指定されます。
	-	サイズ:A1 (既定)
	-	ドメイン名: contoso.com (既定)
	-	ドメイン管理者アカウント名:初期構成中に指定されます。
	-	ドメイン管理者アカウント パスワード:初期構成中に指定されます。

- SQL Server
	-	ホスト名プレフィックス:初期構成中に指定されます。
	-	サイズ:A5 (既定)
	-	データベース アクセス アカウント名:初期構成中に指定されます。
	-	データベース アクセス パスワード:初期構成中に指定されます。
	-	SQL Server サービス アカウント名:初期構成中に指定されます。
	-	SQL Server サービス アカウント パスワード:初期構成中に指定されます。

- SharePoint サーバー
	-	ホスト名プレフィックス:初期構成中に指定されます。
	-	サイズ:A2 (既定)
	-	SharePoint ファーム アカウント名:初期構成中に指定されます。
	-	SharePoint ファーム アカウント パスワード:初期構成中に指定されます。
	-	SharePoint ファーム パスフレーズ:初期構成中に指定されます。


## 高可用性 ##

高可用性 SharePoint ファームは、次に示す構成の 9 台の仮想マシンで構成されます。

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_HighAvail.png)
 
構成の詳細は次のとおりです。

-	Azure サブスクリプション:初期構成中に指定されます。
-	Azure ドメイン名 (クラウド サービスとも呼ばれます):個別のドメイン名が仮想マシンごとに自動で作成されます。
-	ストレージ アカウント:初期構成中に指定されます。
-	仮想ネットワーク	
	-	次のコマンドを入力します。クラウドのみ
	-	アドレス空間:192.168.16.0/26	

-	仮想マシン
	-	HostNamePrefix-DC1 (AD DS ドメイン コントローラー)
	-	HostNamePrefix-DC2 (AD DS ドメイン コントローラー)
	-	HostNamePrefix-SQL1 (SQL Server 2014 サーバー)
	-	HostNamePrefix-SQL2 (SQL Server 2014 サーバー)
	-	HostNamePrefix-SQL0 (SQL Server 2014 サーバー)
	-	HostNamePrefix-WEB1 (SharePoint 2013 サーバー)
	-	HostNamePrefix-WEB2 (SharePoint 2013 サーバー)
	-	HostNamePrefix-APP1 (SharePoint 2013 サーバー)
	-	HostNamePrefix-APP2 (SharePoint 2013 サーバー)

-	ドメイン コントローラー
	-	ホスト名プレフィックス:初期構成中に指定されます。
	-	サイズ:A1 (既定)
	-	ドメイン名: contoso.com (既定)
	-	ドメイン管理者アカウント名:初期構成中に指定されます。
	-	ドメイン管理者アカウント パスワード:初期構成中に指定されます。

-	SQL Server
	-	ホスト名プレフィックス:初期構成中に指定されます。
	-	サイズ:A5 (既定)
	-	データベース アクセス アカウント名:初期構成中に指定されます。
	-	データベース アクセス パスワード:初期構成中に指定されます。
	-	SQL Server サービス アカウント名:初期構成中に指定されます。
	-	SQL Server サービス アカウント パスワード:初期構成中に指定されます。

-	SharePoint サーバー
	-	ホスト名プレフィックス:初期構成中に指定されます。
	-	サイズ:A2 (既定)
	-	SharePoint ファーム アカウント名:初期構成中に指定されます。
	-	SharePoint ファーム アカウント パスワード:初期構成中に指定されます。		
	-	SharePoint ファーム パスフレーズ:初期構成中に指定されます。


<!--HONumber=35.1-->

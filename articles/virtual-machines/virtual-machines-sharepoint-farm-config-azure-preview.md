<properties
	pageTitle="SharePoint サーバー ファームの構成 | Microsoft Azure"
	description="Azure プレビュー ポータルの SharePoint サーバー ファーム機能を使用するときの SharePoint ファームの既定の構成設定について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2015"
	ms.author="josephd"/>


# SharePoint サーバー ファームの構成の詳細

SharePoint サーバー ファームは、事前構成された SharePoint Server 2013 ファームを自動で作成する Azure プレビュー ポータルの機能です。ファームの構成には次の 2 種類があります。

- 基本
- 高可用性

この後のセクションでは、ファームごとに構成の詳細を示します。

詳細については、「[SharePoint Server Farm (SharePoint サーバー ファーム)](virtual-machines-sharepoint-farm-azure-preview.md)」を参照してください。

## 基本的な SharePoint ファーム

この構成では、次の 3 つの仮想マシンの基本的な SharePoint ファームで構成されます。

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_Basic.png)

構成の詳細は次のとおりです。

-	Azure サブスクリプション: 初期構成中に指定されます。
-	Azure ドメイン名 (クラウド サービスとも呼ばれます): 個別のドメイン名が仮想マシンごとに自動で作成されます。
-	ストレージ アカウント: 初期構成中に指定されます。
-	仮想ネットワーク:
	-   種類: クラウドのみ
    -	アドレス空間: 10.0.0.0/26

- 仮想マシン:
	-	*HostNamePrefix*-DC (AD DS ドメイン コントローラー)
	-	*HostNamePrefix*-SQL (SQL Server 2014 サーバー)
	-	*HostNamePrefix*-SP (SharePoint 2013 サーバー)

- ドメイン コントローラー:
	-	仮想マシン イメージ: Windows Server 2012 R2 です。
	-	ホスト名プレフィックス: 初期構成中に指定されます。
	-	サイズ: A1 (既定)。
	-	ドメイン名: contoso.com (既定)。
	-	ドメイン管理者アカウント名: 初期構成中に指定されます。
	-	ドメイン管理者アカウント パスワード: 初期構成中に指定されます。

- データベース サーバー
	-	仮想マシン イメージ: SQL Server 2014 RTM Enterprise on Windows Server 2012 R2 です。
	-	ホスト名プレフィックス: 初期構成中に指定されます。
	-	サイズ: A5 (既定)。
	-	データベース アクセス アカウント名: 初期構成中に指定されます。
	-	データベース アクセス アカウント パスワード: 初期構成中に指定されます。
	-	SQL Server サービス アカウント名: sqlservice (既定)。
	-	SQL Server サービス アカウント パスワード: 初期構成中に指定されます。

- SharePoint サーバー
	-	仮想マシン イメージ: SharePoint Server 2013 試用版です。
	-	ホスト名プレフィックス: 初期構成中に指定されます。
	-	サイズ: A2 (既定)。
	-	SharePoint ファーム アカウント名: sp\_farm (既定)。
	-	SharePoint ファーム アカウント パスワード: 初期構成中に指定されます。
	-	SharePoint ファーム パスフレーズ: 初期構成中に指定されます。


## 高可用性 SharePoint ファーム

高可用性 SharePoint ファームは、次に示す構成の 9 台の仮想マシンで構成されます。

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_HighAvail.png)

構成の詳細は次のとおりです。

-	Azure サブスクリプション: 初期構成中に指定されます。
-	Azure ドメイン名 (クラウド サービスとも呼ばれます): 個別のドメイン名が、上の図に従って作成されます。
-	ストレージ アカウント: 初期構成中に指定されます。
-	仮想ネットワーク:
	-	種類: クラウドのみ
	-	アドレス空間: 10.0.0.0/26

-	仮想マシン:
	-	*HostNamePrefix*-DC1 (AD DS ドメイン コントローラー)
	-	*HostNamePrefix*-DC2 (AD DS ドメイン コントローラー)
	-	*HostNamePrefix*-SQL1 (SQL Server 2014 サーバー)
	-	*HostNamePrefix*-SQL2 (SQL Server 2014 サーバー)
	-	*HostNamePrefix*-SQL0 (Windows Server 2012 R2 サーバー)
	-	*HostNamePrefix*-WEB1 (SharePoint 2013 サーバー)
	-	*HostNamePrefix*-WEB2 (SharePoint 2013 サーバー)
	-	*HostNamePrefix*-APP1 (SharePoint 2013 サーバー)
	-	*HostNamePrefix*-APP2 (SharePoint 2013 サーバー)

-	ドメイン コントローラー:
	-	仮想マシン イメージ: Windows Server 2012 R2 です。
	-	ホスト名プレフィックス: 初期構成中に指定されます。
	-	サイズ: A1 (既定)。
	-	ドメイン名: contoso.com (既定)。
	-	ドメイン管理者アカウント名: 初期構成中に指定されます。
	-	ドメイン管理者アカウント パスワード: 初期構成中に指定されます。

-	データベース サーバー:
	-	仮想マシン イメージ: SQL Server 2014 RTM Enterprise on Windows Server 2012 R2 です。
	-	ホスト名プレフィックス: 初期構成中に指定されます。
	-	サイズ: データベース サーバーは A5 (既定)、ファイル共有監視は A0 (既定)。
	-	データベース アクセス アカウント名: 初期構成中に指定されます。
	-	データベース アクセス アカウント パスワード: 初期構成中に指定されます。
	-	SQL Server サービス アカウント名: sqlservice (既定)。
	-	SQL Server サービス アカウント パスワード: 初期構成中に指定されます。

-	SharePoint サーバー:
	-	仮想マシン イメージ: SharePoint Server 2013 試用版です。
	-	ホスト名プレフィックス: 初期構成中に指定されます。
	-	サイズ: A2 (既定)。
	-	SharePoint ファーム アカウント名: sp\_farm (既定)。
	-	SharePoint ファーム アカウント パスワード: 初期構成中に指定されます。
	-	SharePoint ファーム パスフレーズ: 初期構成中に指定されます。

> [AZURE.NOTE]SharePoint サーバーは、SharePoint Server 2013 試用版のイメージから作成されます。評価期間終了後も継続して仮想マシンを使用するには、インストールを変換して SharePoint Server 2013 の Standard エディションまたは Enterprise エディションの製品版またはボリューム ライセンス キーを使用する必要があります。

## Azure リソース マネージャー

Azure プレビュー ポータルの SharePoint サーバー ファーム機能は、サービス管理内で仮想マシンを作成します。Azure リソース マネージャーで SharePoint Server 2013 ファームを作成するには、「[Azure リソース マネージャーのテンプレートを使用した SharePoint ファームのデプロイ](virtual-machines-workload-template-sharepoint.md)」を参照してください。

## その他のリソース

[SharePoint Server Farm](virtual-machines-sharepoint-farm-azure-preview.md)

[Azure Virtual Machines での SharePoint](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[テスト用のハイブリッド クラウドでの SharePoint イントラネット ファームの設定](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

<!---HONumber=August15_HO9-->
<properties 
	pageTitle="Azure Search インデクサーから Azure の仮想マシンの SQL Server への接続の構成 | Microsoft Azure | インデクサー" 
	description="暗号化された接続を有効にして、Azure Search のインデクサーから Azure の仮想マシン (VM) 上の SQL Server に接続できるようにファイアウォールを構成します。" 
	services="search" 
	documentationCenter="" 
	authors="jack4it" 
	manager="pablocas" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="09/06/2016" 
	ms.author="jackma"/>

# Azure VM での Azure Search インデクサーから SQL Server への接続の構成

「[インデクサーを使用した Azure Search への Azure SQL Database の接続](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md#frequently-asked-questions)」で説明したように、**Azure VM 上の SQL Server** (略して **SQL Azure VM**) に対してインデクサーを作成することは、Azure Search でサポートされています。ただし、最初にセキュリティ関連の 2 つの前提条件に対応する必要があります。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## 暗号化された接続を有効にする

Azure Search には、データベースからデータを読み取るための、セキュリティで保護された接続が必要です。これは、SSL 証明書を構成することによって、SQL Azure VM で、暗号化された接続を有効にする必要があることを意味します。

SQL Server への暗号化接続を有効にする手順については、[データベース エンジンへの暗号化接続の有効化](https://msdn.microsoft.com/library/ms191192.aspx)に関する記事で説明しています。ただし、Azure Search から SQL Azure VM への接続など、公共のインターネット接続の場合は、これを行うために、いくつかの追加要件があります。

### SSL 証明書で FQDN を指定する

SSL 証明書のサブジェクト名は、SQL Azure VM の完全修飾ドメイン名 (つまり **FQDN**) である必要があります。これは、Search サービスのデータ ソースを作成するときにデータベース接続文字列で指定する FQDN と同じです。FQDN の形式は、**Resource Manager** VM の場合は `<your-VM-name>.<region>.cloudapp.azure.com` です。現在も**クラシック** VM を使用している場合、形式は `<your-cloud-service-name.cloudapp.net>` です。SQL Azure VM の FQDN は、[Azure Portal](https://portal.azure.com/) で DNS 名/ラベルとして確認することができます。

### REGEDIT を使用して SSL 証明書を構成する

ドキュメントに記載されているように、SQL Server 構成マネージャーでは、**[証明書]** ドロップダウン リストに FQDN SSL 証明書を表示することができません。これを回避するには、レジストリ キー **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsoft SQL Server[MSSQL13.MSSQLSERVER]\\MSSQLServer\\SuperSocketNetLib\\Certificate** を編集することにより、SSL 証明書を構成する必要があります。*[MSSQL13.MSSQLSERVER]* の部分は、SQL Server のバージョンとインスタンス名によって異なります。このキーは、SQL Azure VM にインストールされている SSL 証明書の**拇印**で更新する必要があります。

### サービス アカウントにアクセス許可を付与する

SQL Server サービス アカウントに SSL 証明書の秘密キーに関する適切なアクセス許可が付与されていることを確認します。この手順を見過ごすと、SQL Server は起動されません。

## Azure Search からの接続を許可するようにファイアウォールを構成する

外部から Azure VM にアクセスできるように、ファイアウォールおよび対応する Azure エンドポイントまたはアクセス制御リスト (ACL) を構成することはめずらしくありません。独自のアプリケーション ロジックから SQL Azure VM に接続できるようにするために、既にこの構成を行っている可能性があります。Azure Search から SQL Azure VM への接続もこれと同じです。構成がまだの場合は、適切なセキュリティ プラクティスを紹介しますので、覚えておいてください。

**Resource Manager** VM を使用している場合は、[Resource Manager を使用した Azure での SQL Server 仮想マシンへの接続](../virtual-machines/virtual-machines-windows-sql-connect.md)に関する記事を参照してください。現在も**クラシック** VM を使用している場合は、[Azure クラシックでの SQL Server 仮想マシンへの接続](../virtual-machines/virtual-machines-windows-classic-sql-connect.md)に関する記事を参照してください。

### Search サービスの IP アドレスへのアクセスを制限する

どちらのデプロイ モデルでも、Azure Search からの接続を構成するときは、SQL Azure VM をすべての接続要求に広く開放するのではなく、ACL で Search サービスの IP アドレスへのアクセスを制限することを強くお勧めします。IP アドレスは、Search サービスの FQDN (`<your-search-service-name>.search.windows.net` など) に ping を実行することで、簡単に確認できます。

### IP アドレス範囲を構成する

Search サービスに検索ユニットが 1 つ (1 つのレプリカと 1 つのパーティション) しかない場合は、日常的なサービスの再起動で IP アドレスが変更されることがあるため注意してください。接続に関する障害を防ぐには、Search サービスがプロビジョニングされている Azure リージョンの IP アドレス範囲を指定する必要があります。Azure リソースへのパブリック IP アドレスの割り当てに使用する IP アドレス範囲のリストは、「[Azure データセンターの IP アドレス範囲](https://www.microsoft.com/download/details.aspx?id=41653)」で公開されています。

### Azure Search ポータルの IP アドレスを含める

さらに、Azure Portal を使用してインデクサーを作成する場合は、作成時に、Azure Search ポータル ロジックから SQL Azure VM にアクセスできることも必要になります。Azure Search ポータルの IP アドレスは、`stamp1.search.ext.azure.com` と `stamp2.search.ext.azure.com` に ping を実行すると確認できます。

## 次のステップ

上記の構成の要件の説明が終了したので、Azure VM 上 の SQL Server を Azure Search インデクサーのデータ ソースとして指定できるようになりました。詳細については、「[インデクサーを使用した Azure Search への Azure SQL Database の接続](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)」を参照してください。

<!---HONumber=AcomDC_0907_2016-->
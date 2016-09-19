<properties
   pageTitle="SQL サーバー ファイアウォールの構成の概要 | Microsoft Azure"
   description="アクセスを管理するために、サーバー レベルおよびデータベース レベルのファイアウォール規則を使用して SQL データベース ファイアウォールを構成する方法について説明します。"
   keywords="データベース ファイアウォール"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor="cgronlun"
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/06/2016"
   ms.author="rickbyh"/>

# Azure SQL Database ファイアウォール規則の構成 - 概要


> [AZURE.SELECTOR]
- [概要](sql-database-firewall-configure.md)
- [Azure ポータル](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL Database は、Azure およびその他のインターネット ベースのアプリケーション用のリレーショナル データベース サービスを提供します。データを保護するため、ファイアウォールは、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーへのすべてのアクセスを遮断します。ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。

ファイアウォールを構成するには、受け入れ可能な IP アドレスの範囲を指定するファイアウォール規則を作成します。サーバーおよびデータベース レベルでは、ファイアウォール規則を作成できます。

- **サーバー レベルのファイアウォール規則:** これらの規則により、クライアントは、Azure SQL サーバー全体、つまり、同じ論理サーバー内のすべてのデータベースにアクセスできるようになります。これらの規則は、**マスター** データベースに保存されます。サーバー レベルのファイアウォール規則を構成するには、ポータルまたは Transact-SQL ステートメントを使用します。
- **データベース レベルのファイアウォール規則:** これらの規則により、クライアントは、Azure SQL Database サーバー内の個々のデータベースにアクセスできるようになります。これらの規則は、データベースごとに作成され、個々のデータベースに格納されます (**master** データベースに対して、データベース レベルのファイアウォール規則を作成できます)。 これらの規則は、同じ論理サーバー内の特定の (セキュリティで保護された) データベースへのアクセスを制限する場合に便利です。データベース レベルのファイアウォール規則は、Transact-SQL ステートメントでのみ構成できます。

**推奨事項:** セキュリティとデータベースの移植性を高めるため、可能な限り、データベース レベルのファイアウォール規則を使用することをお勧めします。アクセス要件が同じデータベースが多数存在し、それぞれのデータベースの設定に時間を費やしたくない場合は、管理者向けのサーバー レベルのファイアウォール規則を使用します。


## ファイアウォールの概要

最初は、Azure SQL サーバーへのすべての Transact-SQL アクセスが、ファイアウォールによってブロックされます。Azure SQL サーバーの使用を開始するには、Azure Portal に移動し、Azure SQL サーバーへのアクセスを有効にする 1 つまたは複数のサーバー レベルのファイアウォール規則を指定する必要があります。ファイアウォール規則を使用すると、インターネットからのアクセスを許可する IP アドレス範囲、および Azure アプリケーションから Azure SQL サーバーへの接続を試みることができるかどうかを指定できます。

Azure SQL サーバーにある 1 つのデータベースにのみ選択的にアクセス許可を付与するには、必要なデータベース用のデータベース レベルの規則を作成する必要があります。データベース ファイアウォール規則に対して、サーバー レベルのファイアウォール規則で指定された IP アドレス範囲外の IP アドレスの範囲を指定し、クライアントの IP アドレス範囲が、データベース レベルの規則で指定された範囲内に確実に収まるようにします。

インターネットおよび Azure からの接続の試行は、次の図に示されるように、Azure SQL サーバーまたは SQL Database に到達する前にファイアウォールを通過する必要があります。

   ![Diagram describing firewall configuration.][1]

## インターネットからの接続

コンピューターがインターネットからデータベース サーバーに接続しようとすると、ファイアウォールは、最初にすべてのファイアウォール規則に対する要求の送信元の IP アドレスを確認します。

- 要求の IP アドレスがサーバー レベルのファイアウォール規則で指定されたいずれかの IP アドレス範囲内にある場合は、Azure SQL Database サーバーへの接続が許可されます。

- 要求の IP アドレスがサーバー レベルのファイアウォール規則で指定されたいずれかの IP アドレス範囲内にない場合は、データベース レベルのファイアウォール規則が確認されます。要求の IP アドレスがデータベース レベルのファイアウォール規則で指定されたいずれかの IP アドレス範囲内にある場合は、該当するデータベース レベルの規則を持つデータベースへの接続のみが許可されます。

- 要求の IP アドレスがサーバー レベルのファイアウォール規則またはデータベース レベルのファイアウォール規則で指定された IP アドレス範囲内にない場合は、接続要求は失敗します。

> [AZURE.NOTE] ローカル コンピューターから Azure SQL Database にアクセスするには、ネットワーク上のファイアウォール、およびローカル コンピューターで、TCP ポート 1433 上の送信が許可されていることを確認します。


## Azure からの接続

Azure からアプリケーションがデータベース サーバーに接続しようとした場合、ファイアウォールは、Azure の接続が許可されていることを確認します。開始アドレスと終了アドレスが 0.0.0.0 であるファイアウォール設定は、これらの接続が許可されていることを示します。接続試行が許可されていない場合、要求は、Azure SQL Database サーバーに到達しません。

> [AZURE.IMPORTANT] このオプションは、ファイアウォールを構成して、他のお客様のサブスクリプションからの接続を含むすべての接続を許可します。このオプションを選択する場合は、ログインおよびユーザーのアクセス許可が、承認されたユーザーのみにアクセスを制限していることを確認してください。

Azure からの接続は、2 つの方法で有効にすることができます。

- [Microsoft Azure Portal](https://portal.azure.com/) では、新しいサーバーを作成するときに、**[Azure サービスにサーバーへのアクセスを許可する]** チェック ボックスをオンにします。

- [クラシック ポータル](http://go.microsoft.com/fwlink/p/?LinkID=161793)では、サーバーの **[構成]** タブの、**[使用できるサービス]** セクションで、**Microsoft Azure サービス**に対し、**[はい]** をクリックします。

## 最初のサーバー レベルのファイアウォール規則の作成

最初のサーバー レベルのファイアウォール設定は、[Azure Portal](https://portal.azure.com/) を使用するか、プログラムで REST API または Azure PowerShell を使用して作成できます。それ以降のサーバー レベルのファイアウォール規則もこれらの方法で作成および管理できます。また、Transact-SQL を介しても実行できます。サーバー レベルのファイアウォール規則の詳細については、[Azure Portal を使用して Azure SQL Database ファイアウォールを構成する方法](sql-database-configure-firewall-settings.md)に関するページをご覧ください。

## データベース レベルのファイアウォール規則の作成

最初のサーバー レベルのファイアウォールを構成した後、特定のデータベースへのアクセスを制限することがあります。サーバー レベルのファイアウォール規則で指定された範囲外にあるデータベース レベルのファイアウォール規則で IP アドレスの範囲を指定した場合、データベース レベルの範囲の IP アドレスを持つクライアントのみがデータベースにアクセスできます。データベースに対し、最大 128 のデータベース レベルのファイアウォール規則を持つことができます。マスターおよびユーザーのデータベースに対するデータベース レベルのファイアウォール規則は、Transact-SQL を介して作成、および管理できます。データベース レベルのファイアウォール規則の構成の詳細については、「[sp\_set\_database\_firewall\_rule (Azure SQL データベース)](https://msdn.microsoft.com/library/dn270010.aspx)」をご覧ください。

## ファイアウォール規則のプログラムによる管理

ファイアウォール規則は、Azure Portal に加え、Transact-SQL、REST API、および Azure PowerShell を使用して、プログラムによって管理することができます。次の表では、各メソッドで使用できるコマンド セットについて説明します。


### Transact-SQL

| カタログ ビューまたはストアド プロシージャ | Level | Description |
|--------------------------------------------------------------------------------------------|-----------|------------------------------------------------------|
| [sys.firewall\_rules](https://msdn.microsoft.com/library/dn269980.aspx) | サーバー | 現在のサーバー レベルのファイアウォール規則を表示 |
| [sp\_set\_firewall\_rule](https://msdn.microsoft.com/library/dn270017.aspx) | サーバー | サーバー レベルのファイアウォール規則を作成または更新 |
| [sp\_delete\_firewall\_rule](https://msdn.microsoft.com/library/dn270024.aspx) | サーバー | サーバー レベルのファイアウォール規則を削除 |
| [sys.database\_firewall\_rules](https://msdn.microsoft.com/library/dn269982.aspx) | データベース | 現在のデータベース レベルのファイアウォール規則を表示 |
| [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) | データベース | データベース レベルのファイアウォール規則を作成または更新 |
| [sp\_delete\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270030.aspx) | データベース | データベース レベルのファイアウォール規則を削除 |

### REST API

| API | Level | Description |
|----------------------|--------|------------------------------------------------------------------|
| [ファイアウォール規則の一覧表示](https://msdn.microsoft.com/library/azure/dn505715.aspx) | サーバー | 現在のサーバー レベルのファイアウォール規則を表示 |
| [ファイアウォール規則の作成](https://msdn.microsoft.com/library/azure/dn505712.aspx) | サーバー | サーバー レベルのファイアウォール規則を作成または更新 |
| [ファイアウォール規則の設定](https://msdn.microsoft.com/library/azure/dn505707.aspx) | サーバー | 既存のサーバー レベルのファイアウォール規則のプロパティを更新 |
| [ファイアウォール規則の削除](https://msdn.microsoft.com/library/azure/dn505706.aspx) | サーバー | サーバー レベルのファイアウォール規則を削除 |


### Azure PowerShell

| コマンドレット | Level | Description |
|-----------------------------------------------------------------------------------------------------|--------|------------------------------------------------------------------|
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx) | サーバー | 現在のサーバー レベルのファイアウォール規則を返す |
| [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx) | サーバー | 新しいサーバー レベルのファイアウォール規則を作成 |
| [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx) | サーバー | 既存のサーバー レベルのファイアウォール規則のプロパティを更新 |
| [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) | サーバー | サーバー レベルのファイアウォール規則を削除 |

> [AZURE.NOTE] ファイアウォール設定に対する変更が有効になるまでに 5 分程度かかる可能性があります。

## データベース ファイアウォールのトラブルシューティング

Microsoft Azure SQL Database サービスへ期待どおりにアクセスできない場合は、次の点を検討してください。

- **ローカル ファイアウォールの構成:** Azure SQL Database へアクセスする前に、TCP ポート 1433 に対し、コンピューターでファイアウォール例外を作成することが必要な場合があります。Azure クラウド境界内で接続を行う場合は、追加のポートをいくつか開かなければならない場合があります。詳細については、「[ADO.NET 4.5 と SQL Database V12 の 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)」の「**SQL Database V12: 外部と内部**」を参照してください。

- **ネットワーク アドレス変換 (NAT):** NAT が原因で、Azure SQL Database に接続するコンピューターで使用される IP アドレスが、コンピューターの IP 構成設定で表示される IP アドレスと異なっている場合があります。Azure に接続するためにコンピューターで使用している IP アドレスを表示するには、ポータルにログインし、データベースをホストしているサーバーの **[構成]** タブに移動します。**[許可された IP アドレス]** セクションに、**現在のクライアント IP アドレス**が表示されます。**[追加]** をクリックして **[使用できる IP アドレス]** に追加し、このコンピューターのサーバーへのアクセスを許可します。

- **許可一覧に変更が反映されない:** Azure SQL Database ファイアウォールの構成に対する変更が反映されるまで最大 5 分間の遅延が発生する場合があります。

- **ログインが許可されない、または正しくないパスワードが使用された:** Azure SQL Database サーバーでは、ログインのアクセス許可がないか、使用したパスワードが正しくない場合、Azure SQL Database サーバーへの接続は拒否されます。ファイアウォール設定の作成は、クライアントに対し、サーバーへの接続を試行する機会のみを提供します。それぞれのクライアントは、必要なセキュリティ資格情報を提供する必要があります。ログインを準備する方法の詳細については、「データベースの管理」、「ログイン」、および「Azure SQL Database 内のユーザー」を参照してください。

- **動的 IP アドレス:** 動的 IP アドレス指定によるインターネット接続を使用しており、ファイアウォールの通過に問題が発生している場合は、次の解決策のいずれかをお試しください。

 - Azure SQL Database サーバーへアクセスするクライアント コンピューターに割り当てられている IP アドレス範囲について、インターネット サービス プロバイダー (ISP) に問い合わせ、ファイアウォール規則として、IP アドレス範囲を追加してください。

 - 動的 IP アドレスの代わりに、静的 IP アドレスを取得し、ファイアウォール規則として、IP アドレス範囲を追加してください。

## 次のステップ

サーバー レベルおよびデータベース レベルのファイアウォール規則を作成する方法については、次の記事をご覧ください。

- [Azure ポータルを使用して Azure SQL Database のサーバー レベルのファイアウォール規則を構成する](sql-database-configure-firewall-settings.md)
- [TSQL を使用して Azure SQL Database ファイアウォールを構成する](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell を使用して Azure SQL Database のサーバー レベルのファイアウォール規則を構成する](sql-database-configure-firewall-settings-powershell.md)
- [REST API を使用して Azure SQL Database ファイアウォールを構成する](sql-database-configure-firewall-settings-rest.md)

データベース作成のチュートリアルについては、「[SQL Database チュートリアル: Azure ポータルを使用して数分で SQL データベースを作成する](sql-database-get-started.md)」を参照してください。オープン ソースまたはサードパーティ製のアプリケーションから Azure SQL Database に接続する方法の詳細については、[SQL Database のクライアント クイック スタート コード サンプル](https://msdn.microsoft.com/library/azure/ee336282.aspx)に関する記事をご覧ください。データベースに移動する方法については、[データベースへのアクセスとログイン セキュリティの管理](https://msdn.microsoft.com/library/azure/ee336235.aspx)に関する記事をご覧ください。



## その他のリソース

- [データベースの保護](sql-database-security.md)
- [SQL Server Database エンジンと Azure SQL Database のセキュリティ センター](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png

<!---HONumber=AcomDC_0907_2016-->
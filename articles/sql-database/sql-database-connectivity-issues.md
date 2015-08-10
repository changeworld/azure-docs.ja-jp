<properties 
	pageTitle="Azure SQL Database 接続の問題" 
	description="SQL Database接続エラーを特定して確認します。" 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="NA" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA" 
	ms.date="07/24/2015" 
	ms.author="sstein"/>


# SQL Database への接続の問題

この記事では、Azure SQL Database に接続できない場合のさまざまなトラブルシューティングの概要について説明します。


## 接続の問題が個々のアプリケーションに特有のものか、単にデータベースに接続できないだけなのかを確認します。

SQL Server Management Studio か SQLCMD を使用します。EXE をデータベースに接続できることを確認します。

- SQL Server Management Studio (SSMS) を使用して SQL Database に接続する方法については、「[SSMSを使用して Azure SQL Database に接続する方法](sql-database-connect-to-database.md)」をご覧ください。
- SQLCM を使用して SQL Database に接続する方法については、「[方法: sqlcmd を使用したAzure SQL Database への接続](https://msdn.microsoft.com/library/azure/ee336280.aspx)」をご覧ください。



## アプリケーションは Azure で実行されている SQL Database に接続しようとしていますか (たとえば、データベースへの接続に失敗しているアプリケーションはクラウド サービスですか、それともWeb アプリケーションですか)。

ファイアウォール ルールが、サーバーとデータベースに対して、すべての Azure サービスを許可していることを確認します。

- ファイアウォール ルールと Azure からの接続を有効にする方法については、「[Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)」をご覧ください。



## アプリケーションは、プライベート ネットワークから SQL Database に接続しようとしていますか。

ファイアウォール ルールが、サーバーとデータベースに対して、特定のネットワークからのアクセスを許可するようになっていることを確認します。

- ファイアウォール ルールの一般的な情報については、「[Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx)」をご覧ください。
- ファイアウォール ルールの設定方法については、「[方法: ファイアウォール設定を構成する (Azure SQL Database)](https://msdn.microsoft.com/library/azure/jj553530.aspx)」をご覧ください。


## ファイアウォール ルールが正しく構成されたら、「[Microsoft Azure SQL Database 接続の問題のトラブルシューティング](https://support2.microsoft.com/common/survey.aspx?scid=sw;en;3844&showpage=1)」をご覧ください。

上記のサポート記事には、次の SQL Database 接続の問題についてヘルプが紹介されています。

- ファイアウォール設定のためにサーバー接続を開くことができない 
- サーバーが見つからなかったか、アクセスできなかった 
- サーバーにログインできない 
- 接続のタイムアウト エラー 
- 一時エラー 
- システム定義の制限に達したための接続終了 
- SQL Server Management Studio (SSMS) からの接続の失敗 


## 追加情報

- SQL Database への接続に関する詳細については、「[プログラムで Azure SQL Database に接続するためのガイドライン](https://msdn.microsoft.com/library/azure/ee336282.aspx)」をご覧ください。   

- 特定の接続エラーの詳細については、「[SQL Database クライアント プログラムのエラー メッセージ](sql-database-develop-error-messages.md#bkmk_connection_errors)」の**一時的な障害や接続が失われるエラー**に関するセクションをご覧ください。

- 接続イベント データは、[**sys.event\_log (Azure SQL Database)**](https://msdn.microsoft.com/library/dn270018.aspx) ビューを使用して、接続イベントを照会して確認できます。

- データベースの接続イベントのメトリックは、クエリを実行してアクセスできる、[**sys.database\_connection\_stats (Azure SQL Database)**](https://msdn.microsoft.com/library/dn269986.aspx) ビューを照会して確認できます。

 

<!---HONumber=July15_HO5-->
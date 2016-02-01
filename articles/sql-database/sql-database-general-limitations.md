<properties 
   pageTitle="Azure SQL Database の一般的な制限事項とガイドライン"
   description="このページでは、Azure SQL Database の一般的な制限事項および相互運用性とサポートの領域について説明します。"
   services="sql-database"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar" />
<tags 
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="01/15/2016"
   ms.author="jroth" />

# Azure SQL Database の一般的な制限事項とガイドライン

このトピックでは、Azure SQL Database の一般的な制限事項とガイドラインについて説明します。クォータ、リソースの管理、およびサポートの詳細については、このトピックの末尾に記載されている[追加のリソース](#additional-guidelines)を参照してください。

## 接続

 - Windows 認証はサポートされません。「[Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)」を参照してください。 

 - Microsoft Azure SQL Database では、表形式データ ストリーム (TDS) プロトコル クライアント バージョン 7.3 以降をサポートしています。

 - TCP/IP 接続のみが許可されます。

 - Microsoft Azure SQL Database には動的ポートがなく、ポート 1433 のみを使用するため、SQL Server 2008 の SQL Server Browser はサポートされません。

## SQL Server エージェント/ジョブ

Microsoft Azure SQL Database では、SQL Server エージェントおよびジョブをサポートしていません。ただし、オンプレミスの SQL Server で SQL Server エージェントを実行し、Microsoft Azure SQL Database に接続することはできます。

## SQL Server 照合順序のサポート

Microsoft Azure SQL Database で使用される既定のデータベース照合順序は **SQL\_LATIN1\_GENERAL\_CP1\_CI\_AS** です。**LATIN1\_GENERAL** は英語 (米国)、**CP1** はコード ページ 1252、**CI** は大文字と小文字の区別なし、**AS** はアクセントの区別ありを表しています。Transact-SQL を使用して、V12 データベースの照合順序を変更できます。照合順序を設定する方法の詳細については、「[COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx)」を参照してください。

## 名前付けに関する要件

セキュリティ上の理由により、特定のユーザー名を使用できません。使用できない名前は次のとおりです。

 - **admin** 
 - **administrator** 
 - **guest** 
 - **root** 
 - **sa** 

すべての新しいオブジェクトの名前は、SQL Server の識別子に関する規則に従う必要があります。詳細については、「[データベース識別子](https://msdn.microsoft.com/library/ms175874.aspx)」を参照してください。

また、ログインおよびユーザー名に \\ 文字を含めることはできません (Windows 認証をサポートしていません)。

## 追加のガイドライン

- この記事で説明した一般的な制限事項のほかにも、SQL Database には、**サービス レベル**に応じて特定のリソースのクォータと制限事項があります。サービス レベルの概要については、「[SQL Database のサービス階層](sql-database-service-tiers.md)」を参照してください。

- SQL Database のその他の制限については、「[Azure SQL Database のリソース制限](sql-database-resource-limits.md)」を参照してください。

- セキュリティ関連のガイドラインについては、「[Azure SQL Database のセキュリティのガイドラインと制限事項](sql-database-security-guidelines.md)」を参照してください。

- 他に関連する領域には、Azure SQL Database とオンプレミスの SQL Server バージョン (SQL Server 2014 など) との互換性があります。Azure SQL Database の最新の V12 バージョンでは、この領域におけるさまざまな機能強化を図っています。詳細については、「[SQL Database V12 の新機能](sql-database-v12-whats-new.md)」を参照してください。

- SQL Database の利用可能なドライバーとサポートの詳細については、「[SQL Database および SQL Server の接続ライブラリ](sql-database-libraries.md)」を参照してください。

<!---HONumber=AcomDC_0121_2016-->
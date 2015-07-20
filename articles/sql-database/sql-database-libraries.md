<properties
	pageTitle="SQL Database および SQL Server の接続ライブラリ"
	description="クライアント プログラムが Azure SQL Database または Microsoft SQL Server に接続する際に使用できる各ドライバーの最小バージョン番号を示します。Microsoft ではなくコミュニティからリリースされているドライバーに関するバージョン情報へのリンクが用意されています。"
	services="sql-database"
	documentationCenter=""
	authors="pehteh"
	manager="jeffreyg"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management" 
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/24/2015"
	ms.author="pehteh"/>


# SQL Database および SQL Server の接続ライブラリ


このトピックでは、クライアント プログラムが Azure SQL Database または Microsoft SQL Server に接続するときに使用できる各ライブラリおよびドライバーの最小バージョン番号を示します。


このトピックは、次の 2 つのセクションに分かれています。


- *Microsoft からリリースされているドライバー ライブラリの表*: Microsoft からリリースされているライブラリの一覧を示します。このセクションの情報は、Microsoft が管理しています。
- *サード パーティ製のライブラリ*: Microsoft ではなくサード パーティによってリリースおよび管理されるライブラリの一覧を示します。**このセクションは、開発者のパブリック コミュニティによって管理されています。Microsoft は、このセクションを管理していません。**


## Microsoft からリリースされているドライバー ライブラリの表


Microsoft からリリースされているライブラリを次の表に示します。**[ライブラリ]** 列には、各ライブラリをダウンロードするためのリンクが用意されています。**[バージョン]** 列には、Azure SQL Database および Microsoft SQL Server とやり取りするうえで推奨される最小バージョンが示されています。


| プラットフォーム | オペレーティング システム | ダウンロード用<br/>ライブラリ | ドライバーの<br/>バージョン | ドライバーの<br/>説明 | 詳細<br/>情報 |
| :--- | :--- | :--- | :--- | :--- | :-- |
| .NET | クロスプラットフォーム (.NET) | [ADO.NET、System.Data.SqlClient](http://www.microsoft.com/download/details.aspx?id=30653) | 4.5 以降 | .NET Framework 用 SQL Server プロバイダー | . |
| PHP | Windows | [PHP for SQL Server](http://www.microsoft.com/download/details.aspx?id=20098) | 2.0 以降 | SQL Server 用 PHP ドライバー | [リンク](http://msdn.microsoft.com/library/dn865013.aspx) |
| Java | Windows | [JDBC for SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) | 2.0 以降 | 標準の JDBC API を介してデータベース接続を提供する Type 4 JDBC ドライバー | [リンク](http://msdn.microsoft.com/library/dn425070.aspx) |
| ODBC | Windows | [ODBC for SQL Server](http://www.microsoft.com/download/details.aspx?id=36434) | 11.0 以降 | Microsoft ODBC Driver for SQL Server | [リンク](http://msdn.microsoft.com/library/jj730308.aspx) |
| ODBC | Suse Linux | [ODBC for SQL Server](http://www.microsoft.com/download/details.aspx?id=34687) | 11.0 以降 | Microsoft ODBC Driver for SQL Server | . |
| ODBC | Redhat Linux | [ODBC for SQL Server](http://www.microsoft.com/download/details.aspx?id=34687) | 11.0 以降 | Microsoft ODBC Driver for SQL Server | . |


### OLEDB for DB2 および SQL Server (DRDA 設計向け)


Microsoft OLE DB Provider for DB2 バージョン 5.0 (Data Provider) では、IBM DB2 データベース向けの分散アプリケーションを作成できます。Data Provider では、分散型リレーショナル データベース アーキテクチャ (DRDA) アプリケーション リクエスターとして機能する DB2 用の Microsoft ネットワーク クライアントと Microsoft SQL Server のデータ アクセス アーキテクチャを活用します。Data Provider は、Microsoft コンポーネント オブジェクト モデル (COM) OLE DB コマンドとデータ型を、DRDA プロトコル コード ポイントとデータ形式に変換します。


詳細については、次を参照してください。


- [Microsoft OLE DB Provider for DB2 バージョン 5.0](http://msdn.microsoft.com/library/dn745875.aspx)
- [Microsoft OLEDB Provider for DB2 v4.0 for Microsoft SQL Server 2012](http://www.microsoft.com/download/details.aspx?id=29100)


## サード パーティ製のライブラリ


> [AZURE.IMPORTANT]サード パーティのライセンス条項に基づいてサード パーティからリリースされているライブラリを次の表に示します。これらのライブラリの使用にあたっては、該当するサード パーティのライセンスを確認しその内容に従う必要があります。これらのライブラリについては、お客様がその使用に伴うリスクを負います。Microsoft は、ユーザーの利便性のためにのみこの情報を提供し、明示的または黙示的にかかわらず、これらの情報についていかなる責任も負わないものとします。いかなる記述も、その種類を問わず、Microsoft による保証を暗示するものではありません。<br/><br/>GitHub.com 上で **Azure** によって所有されている [azure-content](http://github.com/Azure/azure-content/) リポジトリを使ってこの「サード パーティ製のライブラリ」セクションの情報を更新および管理する責任は、開発者のパブリック コミュニティにあります。Microsoft では、このセクションを更新することを開発者に奨励しています。特定のサード パーティ製のライブラリについては社外により専門的な知識を持つ人がいるため、Microsoft の担当者がこのセクション内の情報を管理することは*ありません*。ありがとうございます。


他の会社、コミュニティなど、サード パーティからリリースされているライブラリを次の表に示します。Microsoft からリリースされているライブラリについては、このトピックの前のセクションで紹介しています。


| プラットフォーム | ライブラリ |
| :-- | :-- |
| Python | [pymssql *(org、stable)*](http://pymssql.org/en/stable/)<br/><br/>[pymssql *(org)*](http://pymssql.org/) |
| Node.js | [Tedious *(npmjs)*](http://www.npmjs.com/package/tedious) |
| Node.js | [Node-MSSQL *(github、patriksimek)*](https://github.com/patriksimek/node-mssql)<br/><br/>[Node-MSSQL *(npmjs)*](https://www.npmjs.com/package/node-mssql)<br/><br/>[Node-MSSQL-Connector *(npmjs)*](https://www.npmjs.com/package/node-mssql-connector) |
| Node.js | [Edge.js *(github com、tjanczuk)*](https://github.com/tjanczuk/edge)<br/><br/>[Edge.js *(tjanczuk、github io)*](http://tjanczuk.github.io/edge/) |
| ()。 | [FreeTDS *(org)*](http://www.freetds.org/) |


<!--
https://en.wikipedia.org/wiki/Draft:Microsoft_SQL_Server_Libraries/Drivers
-->

<!---HONumber=July15_HO2-->
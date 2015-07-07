<properties 
	pageTitle="監査と動的データ マスキングのための SQL データベース ダウンレベル クライアントのサポート | Azure" 
	description="監査と動的データ マスキングのための SQL データベース ダウンレベル クライアントのサポート" 
	services="sql-database" 
	documentationCenter="" 
	authors="nadavhelfman" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="nadavhelfman"/>
 
# SQL データベース - 監査と動的データ マスキングのためのダウンレベル クライアントのサポート 


[監査](sql-database-auditing-get-started.md)と[動的データ マスキング](sql-database-dynamic-data-masking-get-started.md)は TDS リダイレクションに対応する SQL クライアントと連動します。

TDS 7.4 を実装するクライアントもリダイレクトをサポートします。この例外には一部のリダイレクション機能に対応していない JDBC 4.0 とリダイレクションが実装されていない Tedious for Node.JS があります。

「ダウンレベル クライアント」、つまり、TDS バージョンが 7.3 以前のクライアントの場合、接続文字列のサーバー FQDN を変更する必要があります。

接続文字列の元のサーバー FQDN: <*server name*>.database.windows.net

接続文字列の変更後のサーバー FQDN: <*server name*>.database.**secure**.windows.net

「ダウンレベル クライアント」には次が含まれます。

- .NET 4.0 以前
- ODBC 10.0 以前
- JDBC 4.0 以前 (JDBC 4.0 は TDS 7.4 対応ですが、一部の TDS リダイレクション機能に対応していません。)
- Tedious (Node.JS 用)

**注記:** 上のサーバー FDQN 変更は SQL サーバー レベル監査ポリシーの適用にも役に立ちます。データベースごとの構成が必要ありません (一時的な軽減)。

 

<!---HONumber=62-->
<properties
   pageTitle="SQL Data Warehouse - データ監査のためのダウンレベル クライアントのサポート | Microsoft Azure"
   description="データ監査のための SQL Data Warehouse のダウンレベル クライアントのサポートについて説明します"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016" 
   ms.author="rortloff;barbkess;sonyama"/>

# SQL Data Warehouse - 監査と動的データ マスキングのためのダウンレベル クライアントのサポート

[監査](sql-data-warehouse-auditing-overview.md)は TDS リダイレクションに対応する SQL クライアントと連動します。

TDS 7.4 を実装するクライアントもリダイレクトをサポートします。この例外には一部のリダイレクション機能に対応していない JDBC 4.0 とリダイレクションが実装されていない Tedious for Node.JS があります。

「ダウンレベル クライアント」、つまり、TDS バージョンが 7.3 以前のクライアントの場合、接続文字列のサーバー FQDN を変更する必要があります。

接続文字列の元のサーバー FQDN: <*server name*>.database.windows.net

接続文字列の変更後のサーバー FQDN: <*server name*>.database.**secure**.windows.net

「ダウンレベル クライアント」には次が含まれます。

- .NET 4.0 以前
- ODBC 10.0 以前
- JDBC (JDBC は TDS 7.4 対応ですが、一部の TDS リダイレクション機能に対応していません。)
- Tedious (Node.JS 用)

**注記:** 上のサーバー FDQN 変更は SQL サーバー レベル監査ポリシーの適用にも役に立ちます。データベースごとの構成が必要ありません (一時的な軽減)。

<!---HONumber=AcomDC_0928_2016-->
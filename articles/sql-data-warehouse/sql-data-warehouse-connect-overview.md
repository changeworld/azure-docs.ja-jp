<properties
   pageTitle="Azure SQL Data Warehouse への接続 | Microsoft Azure"
   description="Azure SQL Data Warehouse に接続するための接続の概要"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/27/2016"
   ms.author="sonyama;barbkess"/>

# Azure SQL Data Warehouse への接続

> [AZURE.SELECTOR]
- [概要](sql-data-warehouse-connect-overview.md)
- [認証](sql-data-warehouse-authentication.md)
- [ドライバー](sql-data-warehouse-connection-strings.md)

Azure SQL Data Warehouse への接続の概要です。

## ポータルからの接続文字列の検索

SQL Data Warehouse は Azure SQL サーバーに関連付けられています。接続するには、サーバーの完全修飾名が必要になります。たとえば、**myserver**.database.windows.net などです。

完全修飾サーバー名を検索するには、次の手順に従います。

1. [Azure ポータル][]にアクセスします。
2. **[SQL データベース]** をクリックし、接続するデータベースをクリックします。この例では、AdventureWorksDW サンプル データベースを使用します。
3. サーバーの完全名を見つけます。

    ![Full server name][1]

## 接続の設定

SQL Data Warehouse では、接続およびオブジェクトの作成中にいくつかの設定が標準化されます。これらをオーバーライドすることはできません。

| データベースの設定 | 値 |
| :--------------------- | :--------------------------- |
| [ANSI\_NULLS][] | ON |
| [QUOTED\_IDENTIFIERS][] | ON |
| [DATEFORMAT][] | mdy |
| [DATEFIRST][] | 7 |

## 接続とクエリの監視

接続が確立してセッションが構築されたら、SQL Data Warehouse にクエリを送信する準備が整ったことになります。セッションとクエリの監視方法については、「[DMV を利用してワークロードを監視する][]」を参照してください。

## 次のステップ

Visual Studio またはその他のアプリケーションを使用してデータ ウェアハウスに対するクエリを開始するには、[Visual Studio を使用したクエリ][]に関するページをご覧ください。

<!--Articles-->
[Visual Studio を使用したクエリ]: ./sql-data-warehouse-query-visual-studio.md
[DMV を利用してワークロードを監視する]: ./sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ANSI\_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED\_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure ポータル]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0831_2016-->
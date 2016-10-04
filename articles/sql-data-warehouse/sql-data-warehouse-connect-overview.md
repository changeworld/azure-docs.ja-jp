<properties
   pageTitle="Azure SQL Data Warehouse への接続 | Microsoft Azure"
   description="Azure SQL Data Warehouse に接続するためのサーバー名と接続文字列を検索する方法"
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
   ms.date="09/26/2016"
   ms.author="sonyama;barbkess"/>

# Azure SQL Data Warehouse への接続

この記事は、初めて SQL Data Warehouse に接続する際に役立つガイドとして書かれています。

## サーバー名を検索する

SQL Data Warehouse に接続するには、まず、サーバー名の検索方法を知る必要があります。たとえば、次の例の場合、サーバー名は sample.database.windows.net です。完全修飾サーバー名を検索するには、次の手順に従います。

1. [Azure ポータル][]にアクセスします。
2. **[SQL Database]** をクリックします。
3. 接続先のデータベースをクリックします。
4. サーバーの完全名を見つけます。

    ![Full server name][1]

## サポートされるドライバーと接続文字列

Azure SQL Data Warehouse では、[ADO.NET][]、[ODBC][]、[PHP][]、[JDBC][] がサポートされています。最新のバージョンとドキュメントを確認するには、左記のドライバーのいずれかをクリックしてください。使用しているドライバーの接続文字列を Azure Portal から自動的に生成するには、上の例にある **[データベース接続文字列の表示]** をクリックします。以下に、各ドライバーの接続文字列の例を示します。

> [AZURE.NOTE] 断続的に切断された場合でも接続を保持できるように、接続のタイムアウトを 300 秒に設定することを検討してください。

### ADO.NET 接続文字列の例

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### ODBC 接続文字列の例

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### PHP 接続文字列の例

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### JDBC 接続文字列の例

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## 接続の設定

SQL Data Warehouse では、接続とオブジェクトの作成中にいくつかの設定が標準化されます。これらの設定をオーバーライドすることはできません。設定には次のものがあります。

| データベースの設定 | 値 |
| :--------------------- | :--------------------------- |
| [ANSI\_NULLS][] | ON |
| [QUOTED\_IDENTIFIERS][] | ON |
| [DATEFORMAT][] | mdy |
| [DATEFIRST][] | 7 |

## 次のステップ

Visual Studio を使用して接続とクエリを行うには、[Visual Studio を使用したクエリ][]に関するページをご覧ください。認証オプションの詳細については、「[Azure SQL Data Warehouse への認証][]」をご覧ください。

<!--Articles-->
[Visual Studio を使用したクエリ]: ./sql-data-warehouse-query-visual-studio.md
[Azure SQL Data Warehouse への認証]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI\_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED\_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure ポータル]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0928_2016-->
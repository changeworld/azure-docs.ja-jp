---
title: "Azure Database for MySQL にアプリケーションを接続する方法 |Microsoft Docs"
description: "このドキュメントでは、Azure Database for MySQL に接続するアプリケーションについて、現在サポートされているすべての接続文字列が一覧表示されます"
services: mysql
author: wuta
ms.author: wuta
editor: jasonh
manager: jhubbard
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 93b74f146cd0a75437e85dbb1c941b29534ffab2
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Azure Database for MySQL にアプリケーションを接続する方法
このドキュメントでは、Azure Database for MySQL でサポートされているすべての接続文字列の種類が、テンプレートおよび例とともに一覧表示されます。 接続文字列のパラメーターと設定は、異なる場合があります。

- 証明書を取得するには、[SSL の構成](./howto-configure-ssl.md)に関するページを参照してください。
- {your_host} = <servername>.mysql.database.azure.com

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={your_username};Pwd={your_password};[SslMode=Required;]
```

たとえば、サーバー名が **wpdemo**、データベース名が **wpdb**、ユーザー名が **WPAdmin**、パスワードが **orcas!2** であるとします。

接続文字列は次のようになります。

```ado.net
Server= "wpdemo.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@wpdemo"; Pwd="orcas!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {your_username}, {your_password}";
```

## <a name="nodejs"></a>Node.JS
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {your_username}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={your_username};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {your_username}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={your_username}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {your_username}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-connection-string-from-portal"></a>ポータルから接続文字列を取得する
[Azure Portal](https://portal.azure.com) で、Azure Database for MySQL に移動し、**[接続文字列]** をクリックして、インスタンスの文字列リストを取得します。![ポータル上の接続文字列](./media/howto-connection-strings/connection-strings-on-portal.png)

この文字列は、ドライバー、サーバー、その他のデータベース接続パラメーターといった詳細情報を提供します。 必要な接続文字列をコピーし、データベース名、パスワードなどの独自のパラメーターを使用して変更します。この文字列を使用して、サーバーに接続できるようになります。

## <a name="next-steps"></a>次のステップ
- 接続ライブラリの詳細については、[接続ライブラリの概念](./concepts-connection-libraries.md)に関するページを参照してください。


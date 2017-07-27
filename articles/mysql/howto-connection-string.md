---
title: "Azure Database for MySQL にアプリケーションを接続する |Microsoft Docs"
description: "このドキュメントでは、Azure Database for MySQL に接続するアプリケーションについて、現在サポートされている接続文字列を示します。ADO.NET (C#)、JDBC、Node.js、ODBC、PHP、Python、および Ruby の接続文字列が含まれます。"
services: mysql
author: mswutao
ms.author: wuta
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 06/12/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 2f40da41bcfda7e35f6fc63ead5d055246ab390c
ms.contentlocale: ja-jp
ms.lasthandoff: 06/30/2017

---

# Azure Database for MySQL にアプリケーションを接続する方法
<a id="how-to-connect-applications-to-azure-database-for-mysql" class="xliff"></a>
このドキュメントでは、Azure Database for MySQL でサポートされているすべての接続文字列の種類を、テンプレートおよび例とともに示します。 実際に使用する接続文字列のパラメーターと設定は異なる可能性があります。

- 証明書を取得するには、[SSL を構成する方法](./howto-configure-ssl.md)に関するページを参照してください。
- {your_host} = <servername>.mysql.database.azure.com
- {your_user}@{servername} = 正しく認証されるためのユーザー ID の形式です。  ユーザー ID だけを使用すると、認証は失敗します。

## ADO.NET
<a id="adonet" class="xliff"></a>
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

この例では、サーバー名は `myserver4demo`、データベース名は `wpdb`、ユーザー名は `WPAdmin`、パスワードは `mypassword!2` です。 接続文字列は次のようになります。

```ado.net
Server= "myserver4demo.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@myserver4demo"; Pwd="mypassword!2"; SslMode=Required;
```

## JDBC
<a id="jdbc" class="xliff"></a>
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {username@servername}, {your_password}";
```

## Node.js
<a id="nodejs" class="xliff"></a>
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {username@servername}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## ODBC
<a id="odbc" class="xliff"></a>
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## PHP
<a id="php" class="xliff"></a>
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {username@servername}, {your_password}, {your_database}, {your_port});
```

## Python
<a id="python" class="xliff"></a>
```python
cnx = mysql.connector.connect(user={username@servername}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## Ruby
<a id="ruby" class="xliff"></a>
```ruby
client = Mysql2::Client.new(username: {username@servername}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## Azure ポータルから接続文字列の詳細を取得する
<a id="get-the-connection-string-details-from-the-azure-portal" class="xliff"></a>
[Azure ポータル](https://portal.azure.com)で、Azure Database for MySQL サーバーに移動し、**[接続文字列]** をクリックして、インスタンスの文字列リストを取得します。![Azure ポータルの [接続文字列] ウィンドウ](./media/howto-connection-strings/connection-strings-on-portal.png)

この文字列は、ドライバー、サーバー、その他のデータベース接続パラメーターといった詳細情報を提供します。 独自のパラメーター (データベース名やパスワードなど) を使用してこれらの例を変更します。 その文字列を使用して、コードやアプリケーションからサーバーに接続できます。

## 次のステップ
<a id="next-steps" class="xliff"></a>
- 接続ライブラリの詳細については、[接続ライブラリの概念](./concepts-connection-libraries.md)に関するページを参照してください。


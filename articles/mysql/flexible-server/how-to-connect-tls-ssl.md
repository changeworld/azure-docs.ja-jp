---
title: Azure Database for MySQL - フレキシブル サーバーでの TLS/SSL を使用した暗号化された接続
description: Azure Database for MySQL - フレキシブル サーバーで TLS/SSL を使用して接続する方法に関する手順および情報。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 24a8dd4d21cb6ab6edeb985db4e6e6a1349a758d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90932506"
---
# <a name="encrypted-connectivity-using-transport-layer-security-tls-12-in-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL - フレキシブル サーバーでのトランスポート層セキュリティ (TLS 1.2) を使用した暗号化された接続

> [!IMPORTANT]
> Azure Database for MySQL フレキシブル サーバーは現在、パブリック プレビュー段階にあります。

Azure Database for MySQL フレキシブル サーバーでは、トランスポート層セキュリティ (TLS) (以前の Secure Sockets Layer (SSL)) を使用した MySQL サービスへのクライアント アプリケーションの接続がサポートされます。 TLS は、データベース サーバーとクライアント アプリケーションの間の暗号化されたネットワーク接続を保証する業界標準のプロトコルです。これを使用することで、ユーザーはコンプライアンス要件に準拠することができます。

Azure Database for MySQL フレキシブル サーバーでは、トランスポート層セキュリティ (TLS 1.2) を使用した暗号化された接続のみがサポートされ、TLS 1.0 と TLS 1.1 を使用した受信接続はすべて拒否されます。 すべてのフレキシブル サーバーで TLS 接続の適用が有効になるため、フレキシブル サーバーに接続するために TLS/SSL を無効にすることはできません。

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>TLS/SSL 接続のために証明書の検証を必要とするアプリケーション
安全に接続するために、信頼された証明機関 (CA) 証明書ファイルから生成されたローカルの証明書ファイルがアプリケーションに必要な場合があります。 Azure Database for MySQL フレキシブル サーバーでは、*DigiCert Global Root CA* を使用します。 [DigiCert Global Root CA](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) から SSL 経由で通信するために必要なこの証明書をダウンロードし、証明書ファイルを希望する場所に保存します。 たとえば、このチュートリアルでは `c:\ssl` を使用します。

### <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>TLS/SSL で mysql コマンド ライン クライアントを使用して接続する

"*プライベート アクセス (VNet 統合)* " を指定してフレキシブル サーバーを作成した場合は、サーバーと同じ VNet 内のリソースからサーバーに接続する必要があります。 仮想マシンを作成し、それをフレキシブル サーバーと共に作成された VNet に追加できます。

"*パブリック アクセス (使用できる IP アドレス)* " を指定してフレキシブル サーバーを作成した場合は、サーバー上のファイアウォール規則のリストにローカル IP アドレスを追加できます。

ローカル環境からサーバーに接続するには、[mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) または [MySQL Workbench](./connect-workbench.md) のどちらかを選択できます。 

次の例は、mysql コマンド ライン インターフェイスを使用してサーバーに接続する方法を示しています。 `--ssl-mode=REQUIRED` 接続文字列設定を使用して、TLS/SSL 証明書の検証を適用します。 ローカルの証明書ファイルのパスを `--ssl-ca` パラメーターに渡します。 値を実際のサーバー名とパスワードに置き換えてください。 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> `--ssl-ca` に渡された値が、保存済みの証明書のファイル パスと一致することを確認します。

### <a name="verify-the-tlsssl-connection"></a>TLS/SSL 接続を確認する

TLS/SSL を使用して MySQL サーバーに接続していることを確認するには、MySQL の **status** コマンドを実行します。

```dos
mysql> status
```
出力を確認することで、接続が暗号化されていることを確認します。次のように表示されます:**SSL:Cipher in use is AES256-SHA (SSL: 使用中の暗号は AES256 SHA です)** 。 この暗号スイートは例を示しており、クライアントによっては別の暗号スイートが表示されることがあります。

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>アプリケーションまたはフレームワークが TLS 接続をサポートしているかどうかの確認

データベース サービスに MySQL を使用するアプリケーション フレームワークの中には、インストール中に TLS が既定で有効にならないものがあります。 MySQL サーバーで TLS 接続が適用されていても、アプリケーションで TLS が構成されていない場合は、アプリケーションがデータベース サーバーへの接続に失敗する可能性があります。 TLS 接続を有効にする方法については、使用しているアプリケーションのドキュメントを参照してください。

## <a name="sample-code"></a>サンプル コード
Azure portal でサーバーに使用できる [接続文字列] ページで事前定義されている接続文字列には、TLS/SSL を使用してデータベース サーバーに接続するための一般的な言語の必須のパラメーターが含まれています。 TLS/SSL パラメーターは、コネクタによって異なります。 たとえば、"useSSL=true"、"sslmode=required"、または "ssl_verify_cert=true" などのバリエーションがあります。

アプリケーションから TLS/SSL 経由でフレキシブル サーバーへの暗号化された接続を確立するには、次のコード サンプルを参照してください。

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (PDO を使用)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'myadmin', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)

```python
try:
    conn = mysql.connector.connect(user='myadmin',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/DigiCertGlobalRootCA.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'})
```

### <a name="django-pymysql"></a>Django (PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/DigiCertGlobalRootCA.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
db, _ := sql.Open("mysql", connectionString)
```

### <a name="java-mysql-connector-for-java"></a>Java (MySQL Connector for Java)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="java-mariadb-connector-for-java"></a>Java (MariaDB Connector for Java)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "DigiCertGlobalRootCA.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>次のステップ
- [MySQL Workbench を使用して Azure Database for MySQL フレキシブル サーバーに接続し、そのデータにクエリを実行する](./connect-workbench.md)
- [PHP を使用して Azure Database for MySQL フレキシブル サーバーに接続し、そのデータにクエリを実行する](./connect-php.md)
- [Azure CLI を使用して Azure Database for MySQL フレキシブル サーバーの仮想ネットワークを作成および管理する](./how-to-manage-virtual-network-cli.md)。
- [Azure Database for MySQL フレキシブル サーバーでのネットワーク](./concepts-networking.md)の詳細を確認する
- [Azure Database for MySQL フレキシブル サーバーのファイアウォール規則](./concepts-networking.md#public-access-allowed-ip-addresses)の詳細を確認する

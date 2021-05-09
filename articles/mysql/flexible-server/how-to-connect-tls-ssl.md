---
title: Azure Database for MySQL - フレキシブル サーバーでの TLS/SSL を使用した暗号化された接続
description: Azure Database for MySQL - フレキシブル サーバーで TLS/SSL を使用して接続する方法に関する手順および情報。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 399cf8087d39f78184cfdae4b9f0e34efecaea66
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491605"
---
# <a name="connect-to-azure-database-for-mysql---flexible-server-with-encrypted-connections"></a>暗号化された接続を使用して Azure Database for MySQL - フレキシブル サーバーに接続する

> [!IMPORTANT]
> Azure Database for MySQL フレキシブル サーバーは現在、パブリック プレビュー段階にあります。

Azure Database for MySQL フレキシブル サーバーでは、Secure Sockets Layer (SSL) とトランスポート層セキュリティ (TLS) の暗号化を使用した MySQL サーバーへのクライアント アプリケーションの接続がサポートされます。 TLS は、データベース サーバーとクライアント アプリケーションの間の暗号化されたネットワーク接続を保証する業界標準のプロトコルであり、ユーザーがコンプライアンス要件に準拠できるようにします。

Azure Database for MySQL フレキシブル サーバーでは、トランスポート層セキュリティ (TLS 1.2) を使用する暗号化された接続が既定でサポートされ、TLS 1.0 と TLS 1.1 を使用する受信接続はすべて既定では拒否されます。 フレキシブル サーバーに対する暗号化された接続の強制または TLS のバージョンの構成は、この記事で説明されているように変更できます。 

次に、フレキシブル サーバーで使用できる SSL と TLS の設定のさまざまな構成を示します。

| 通信の種類   | サーバー パラメーターの設定      | 説明                                    |
|------------|--------------------------------|------------------------------------------------|
|SSL を無効にする (暗号化された接続) | require_secure_transport = OFF |レガシ アプリケーションで MySQL サーバーへの暗号化された接続がサポートされていない場合、require_secure_transport=OFF を設定すると、フレキシブル サーバーへの暗号化された接続の強制を無効にできます。|
|バージョン 1.2 より前の TLS で SSL を強制する | require_secure_transport = ON および tls_version = TLSV1 または TLSV1.1| レガシ アプリケーションで暗号化された接続がサポートされているが、バージョン 1.2 より前の TLS が必要な場合は、暗号化された接続を有効にできますが、アプリケーションでサポートされている TLS のバージョン (v1.0 または v1.1) での接続を許可するようにフレキシブル サーバーを構成します|
|TLS バージョン 1.2 で SSL を強制する (既定の構成)|require_secure_transport = ON および tls_version = TLSV1.2| これは、フレキシブル サーバーに推奨される既定の構成です。|
|TLS バージョン 1.3 で SSL を強制する (MySQL v8.0 以降でサポート)| require_secure_transport = ON および tls_version = TLSV1.3| これは、新しいアプリケーションの開発に便利であり、推奨されます|

> [!Note]
> フレキシブル サーバーでの SSL 暗号の変更はサポートされていません。 tls_version が TLS バージョン 1.2 に設定されている場合、FIPS 暗号スイートが既定で強制されます。 バージョン 1.2 以外の TLS の場合、SSL 暗号は、MySQL Community のインストールに付随する既定値に設定されます。

この記事では、次のことについて説明します。
* フレキシブル サーバーを構成する 
  * SSL を無効にする 
  * バージョン 1.2 より前の TLS で SSL を強制する
* mysql コマンド ラインを使用してフレキシブル サーバーに接続する 
  * 暗号化された接続を無効にする
  * 暗号化された接続を有効にする
* 接続の暗号化の状態を確認する
* さまざまなアプリケーション フレームワークを使用して、暗号化された接続でフレキシブル サーバーに接続する

## <a name="disable-ssl-on-your-flexible-server"></a>フレキシブル サーバーで SSL を無効にする
クライアント アプリケーションが暗号化された接続をサポートしていない場合、フレキシブル サーバーに対して、暗号化された接続の強制を無効にする必要があります。 暗号化された接続の強制を無効にするには、スクリーンショットに示されているように require_secure_transport サーバー パラメーターを OFF に設定し、このサーバー パラメーターの構成を保存して有効にします。 require_secure_transport は、ただちに有効になる **動的サーバー パラメーター** であるため、有効にするためにサーバーを再起動する必要はありません。

> :::image type="content" source="./media/how-to-connect-tls-ssl/disable-ssl.png" alt-text="Azure Database for MySQL フレキシブル サーバーで SSL を無効にする方法を示すスクリーンショット。":::

### <a name="connect-using-mysql-command-line-client-with-ssl-disabled"></a>SSL を無効にした mysql コマンド ライン クライアントを使用して接続する

次の例は、mysql コマンド ライン インターフェイスを使用してサーバーに接続する方法を示しています。 `--ssl-mode=DISABLED` 接続文字列設定を使用して、mysql クライアントからの TLS/SSL 接続を無効にします。 値を実際のサーバー名とパスワードに置き換えてください。 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=DISABLED 
```
require_secure_transport を OFF に設定しても、暗号化された接続がサーバー側でサポートされなくなるということではないので注意してださい。 フレキシブル サーバーに対して require_secure_transport を OFF に設定した場合でも、クライアントが暗号化された接続を使用して接続すると、それはそのまま受け入れられます。 次に示す、require_secure_transport=OFF で構成されたフレキシブル サーバーへの mysql クライアントを使用した接続も、下のように正常に機能します。

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED
```
```output
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 17
Server version: 5.7.29-log MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show global variables like '%require_secure_transport%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| require_secure_transport | OFF   |
+--------------------------+-------+
1 row in set (0.02 sec)
```

まとめると、require_secure_transport=OFF を設定すると、フレキシブル サーバーに対する暗号化された接続の強制が緩和され、クライアントからサーバーへの暗号化されていない接続も、暗号化された接続に加えて許可されます。

## <a name="enforce-ssl-with-tls-version--12"></a>バージョン 1.2 より前の TLS で SSL を強制する

アプリケーションが SSL を使用した MySQL サーバーへの接続をサポートしている一方でバージョン 1.2 よりも前の TLS をサポートしている場合、フレキシブル サーバーに対して TLS バージョンのサーバー パラメーターを設定する必要があります。 フレキシブル サーバーでサポートする TLS のバージョンを設定するには、スクリーンショットに示されているように、tls_version サーバー パラメーターを TLSV1、TLSV 1.1、または TLSV1 と TLSV 1.1 に設定し、このサーバー パラメーターの構成を保存して有効にする必要があります。 tls_version は **静的サーバー パラメーター** であるため、パラメーターを有効にするためにサーバーを再起動する必要があります。

> :::image type="content" source="./media/how-to-connect-tls-ssl/tls-version.png" alt-text="Azure Database for MySQL フレキシブル サーバーの TLS バージョンの設定方法を示すスクリーンショット。":::

## <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>TLS/SSL で mysql コマンド ライン クライアントを使用して接続する

### <a name="download-the-public-ssl-certificate"></a>パブリック SSL 証明書をダウンロードする
クライアント アプリケーションで暗号化された接続を使用するには、下のスクリーンショットに示されているように、Azure portal の [ネットワーク] ブレードでも入手できる[パブリック SSL 証明書](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)をダウンロードする必要があります。

> :::image type="content" source="./media/how-to-connect-tls-ssl/download-ssl.png" alt-text="Azure portal からパブリック SSL 証明書をダウンロードする方法を示すスクリーンショット。":::

証明書ファイルをダウンロードし、希望の場所に保存します。 たとえば、このチュートリアルでは、ローカル環境、またはアプリケーションがホストされているクライアント環境の `c:\ssl` または `\var\www\html\bin` を使用しています。 これにより、アプリケーションから SSL 経由で安全にデータベースに接続できるようになります。

*プライベート アクセス (Vnet 統合)* を使用してフレキシブル サーバーを作成した場合は、サーバーと同じ VNet 内のリソースからサーバーに接続する必要があります。 仮想マシンを作成し、それをフレキシブル サーバーと共に作成された VNet に追加できます。

*パブリック アクセス (使用できる IP アドレス)* を使用してフレキシブル サーバーを作成した場合は、サーバー上のファイアウォール規則の一覧にローカル IP アドレスを追加できます。

ローカル環境からサーバーに接続するには、[mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) または [MySQL Workbench](./connect-workbench.md) のどちらかを選択できます。 

次の例は、mysql コマンド ライン インターフェイスを使用してサーバーに接続する方法を示しています。 `--ssl-mode=REQUIRED` 接続文字列設定を使用して、TLS/SSL 証明書の検証を適用します。 ローカルの証明書ファイルのパスを `--ssl-ca` パラメーターに渡します。 値を実際のサーバー名とパスワードに置き換えてください。 

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> `--ssl-ca` に渡された値が、保存済みの証明書のファイル パスと一致することを確認します。

暗号化されていない接続を使用してサーバーに接続しようとすると、セキュリティで保護されていないトランスポートを使用した接続が禁止されていることを示す、下のようなエラーが表示されます。

```output
ERROR 3159 (HY000): Connections using insecure transport are prohibited while --require_secure_transport=ON.
```

## <a name="verify-the-tlsssl-connection"></a>TLS/SSL 接続を確認する

TLS/SSL を使用して MySQL サーバーに接続していることを確認するには、MySQL の **status** コマンドを実行します。

```dos
mysql> status
```
接続が暗号化されていることを確認します。そのために、出力に **SSL: Cipher in use is ** (SSL: 使用中の暗号: ) と表示されているとを確認します。 この暗号スイートは例を示しており、クライアントによっては別の暗号スイートが表示されることがあります。

## <a name="connect-to-your-flexible-server-with-encrypted-connections-using-various-application-frameworks"></a>さまざまなアプリケーション フレームワークを使用して、暗号化された接続でフレキシブル サーバーに接続する

Azure portal でサーバーに使用できる [接続文字列] ページで事前定義されている接続文字列には、TLS/SSL を使用してデータベース サーバーに接続するための一般的な言語の必須のパラメーターが含まれています。 TLS/SSL パラメーターは、コネクタによって異なります。 たとえば、"useSSL=true"、"sslmode=required"、または "ssl_verify_cert=true" などのバリエーションがあります。

アプリケーションから TLS/SSL 経由でフレキシブル サーバーへの暗号化された接続を確立するには、次のコード サンプルを参照してください。

### <a name="wordpress"></a>WordPress
[SSL パブリック証明書](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)をダウンロードして、wp-config.php 内で ```// ** MySQL settings - You can get this info from your web host ** //``` 行の後に次の行を追加します。

```php
//** Connect with SSL** //
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
//** SSL CERT **//
define('MYSQL_SSL_CERT','/FULLPATH/on-client/to/DigiCertGlobalRootCA.crt.pem');
```

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

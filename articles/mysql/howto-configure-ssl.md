---
title: "Azure Database for MySQL に安全に接続するために SSL 接続を構成する | Microsoft Docs"
description: "SSL 接続を正しく使用するために Azure Database for MySQL と関連するアプリケーションを適切に構成する方法について"
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 10/25/2017
ms.openlocfilehash: 83830e4776eaa7c4f10bc14dcefd47c6eaf25997
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2017
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Azure Database for MySQL に安全に接続するためにアプリケーションで SSL 接続を構成する
Azure Database for MySQL は、Secure Sockets Layer (SSL) を使用した、クライアント アプリケーションへの Azure Database for MySQL サーバーへの接続をサポートします。 データベース サーバーとクライアント アプリケーション間に SSL 接続を適用すると、サーバーとアプリケーション間のデータ ストリームが暗号化されて、"man in the middle" 攻撃から保護されます。

## <a name="step-1-obtain-ssl-certificate"></a>手順 1: SSL 証明書を取得する
Azure Database for MySQL サーバーで SSL 経由で通信するために必要な証明書を [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) からダウンロードし、その証明書ファイルをローカル ドライブ (このチュートリアルでは c:\ssl を使用) に保存します。
**Microsoft Internet Explorer と Microsoft Edge の場合:** ダウンロードが完了したら、証明書の名前を BaltimoreCyberTrustRoot.crt.pem に変更します。

## <a name="step-2-bind-ssl"></a>手順 2: SSL をバインドする
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>MySQL Workbench による SSL 経由でのサーバーへの接続
SSL 経由で安全に接続するように MySQL Workbench を構成します。 [Setup New Connection]\(新しい接続のセットアップ\) ダイアログから、**[SSL]** タブに移動します。**[SSL CA File:]\(SSL CA ファイル:\)** フィールドに **BaltimoreCyberTrustRoot.crt.pem** ファイルの場所を入力します。 
![カスタマイズされたタイル保存](./media/howto-configure-ssl/mysql-workbench-ssl.png) 既存の接続に SSL をバインドするには、接続アイコンを右クリックして編集を選択します。 次に **[SSL]** タブに移動して証明書ファイルをバインドします。

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>MySQL CLI による SSL 経由でのサーバーへの接続
SSL 証明書をバインドする別の方法として、次のコマンドを実行して MySQL コマンド ライン インターフェイスを使用します。
```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -u Username@mysqlserver4demo -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>手順 3: Azure で SSL 接続を適用する 
### <a name="using-the-azure-portal"></a>Azure ポータルの使用
Azure Portal から Azure Database for MySQL サーバーにアクセスし、**[接続のセキュリティ]** をクリックします。 トグル ボタンを使用して、**[SSL 接続を強制する]** 設定を有効または無効にし、**[保存]** をクリックします。 Microsoft では、セキュリティ強化のため **[SSL 接続を強制する]** 設定を常に有効にしておくことをお勧めします。
![SSL の有効化](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Azure CLI の使用
**ssl-enforcement** パラメーターを有効または無効にするには、Azure CLI でそれぞれ Enabled または Disabled の値を使用します。
```azurecli-interactive
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>手順 4: SSL 接続を確認する
MySQL の **status** コマンドを実行して、SSL 経由で MySQL サーバーに接続していることを確認します。
```dos
mysql> status
```
接続が暗号化されていることを確認します。そのためには、出力に "**SSL: Cipher in use is AES256-SHA**" (SSL: 使用中の暗号は AES256-SHA) と表示されていることを確認します。 

## <a name="sample-code"></a>サンプル コード
アプリケーションから Azure Database for MySQL へのセキュリティで保護された接続を SSL 経由で確立するためには、次のコード サンプルを参照してください。
### <a name="php"></a>PHP
```
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'myserver4demo.mysql.database.azure.com', 'myadmin@myserver4demo', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```
try:
    conn=mysql.connector.connect(user='myadmin@myserver4demo', 
        password='yourpassword', 
        database='quickstartdb', 
        host='myserver4demo.mysql.database.azure.com', 
        ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```
conn = pymysql.connect(user = 'myadmin@myserver4demo', 
        password = 'yourpassword', 
        database = 'quickstartdb', 
        host = 'myserver4demo.mysql.database.azure.com', 
        ssl = {'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```
### <a name="ruby"></a>Ruby
```
client = Mysql2::Client.new(
        :host     => 'myserver4demo.mysql.database.azure.com', 
        :username => 'myadmin@myserver4demo',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :ssl_ca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
```
### <a name="golang"></a>Golang
```
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool, InsecureSkipVerify: true})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@myserver4demo' , 'yourpassword', 'myserver4demo.mysql.database.azure.com', 'quickstartdb') 
db, _ := sql.Open("mysql", connectionString)
```
### <a name="javajdbc"></a>JAVA(JDBC)
```
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

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'myserver4demo.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@myserver4demo');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="javamariadb"></a>JAVA(MariaDB)
```
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

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'myserver4demo.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@myserver4demo');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

## <a name="next-steps"></a>次のステップ
[Azure Database for MySQL の接続ライブラリ](concepts-connection-libraries.md)に従って、さまざまなアプリケーション接続オプションを確認する

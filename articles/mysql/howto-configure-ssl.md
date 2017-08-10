---
title: "Azure Database for MySQL に安全に接続するために SSL 接続を構成する | Microsoft Docs"
description: "SSL 接続を正しく使用するために Azure Database for MySQL と関連するアプリケーションを適切に構成する方法について"
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 06/01/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 1303e6cf6f6fdd10a7310d770127828276d5056e
ms.contentlocale: ja-jp
ms.lasthandoff: 06/30/2017

---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Azure Database for MySQL に安全に接続するためにアプリケーションで SSL 接続を構成する
Azure Database for MySQL は、Secure Sockets Layer (SSL) を使用した、クライアント アプリケーションへの Azure Database for MySQL サーバーへの接続をサポートします。 データベース サーバーとクライアント アプリケーション間に SSL 接続を適用すると、サーバーとアプリケーション間のデータ ストリームが暗号化されて、"man in the middle" 攻撃から保護されます。

既定では、Azure Database for MySQL サーバーへの接続時に SSL 接続を要求するようにデータベース サービスを構成する必要があります。  可能な場合は、SSL オプションを無効にすることは避けてください。

## <a name="enforcing-ssl-connections"></a>SSL 接続の適用
Azure Portal や CLI を使用して新しい Azure Database for MySQL サーバーをプロビジョニングするときには、SSL 接続の適用が既定で有効になります。

同様に、Azure Portal のサーバー下にある [接続文字列] 設定で事前定義された接続文字列には、SSL で必要な一般的な言語の必須パラメーターが含まれます。 SSL パラメーターはコネクタによって異なります ("ssl=true"、"sslmode=require"、"sslmode=required" など)。

## <a name="configure-enforcement-of-ssl"></a>SSL 適用の構成
SSL の適用は、無効または有効にすることができます。 Microsoft Azure では、セキュリティ強化のため [Enforce SSL connection] \(SSL 接続の適用) 設定は常に有効にしておくことをお勧めします。

### <a name="using-azure-portal"></a>Azure Portal の使用
Azure Porta から Azure Database for MySQL サーバーにアクセスし、**[接続のセキュリティ]** をクリックします。 トグル ボタンを使用して、**[Enforce SSL connection] \(SSL 接続の適用)** 設定を有効または無効にします。 その後、 **[保存]**をクリックします。 Microsoft では、セキュリティ強化のため **[Enforce SSL connection] \(SSL 接続の適用)** 設定は常に有効にしておくことをお勧めします。
![SSL の有効化](./media/howto-configure-ssl/enable-ssl.png)

この設定は、**[概要]** ページの **SSL 適用ステータス** インジケーターで確認できます。

### <a name="using-azure-cli"></a>Azure CLI の使用
**ssl-enforcement** パラメーターを有効または無効にするには、Azure CLI でそれぞれ Enabled または Disabled の値を使用します。
```azurecli-interactive
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```
## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>アプリケーションまたはフレームワークが SSL 接続をサポートしているかどうかの確認
Wordpress、Drupal、Magento など、データベース サービスに MySQL を使用している一般的なアプリケーションの中には、インストール時に既定で SSL が有効にならないものが多く存在します。 これらのアプリケーションでは、SSL 接続をアプリケーションのインストール後またはアプリケーションに固有の CLI コマンドを使用して有効にしてください。 MySQL サーバーが SSL 接続を適用している場合、関連付けられているアプリケーションが正しく構成されていないと、データベース サーバーに接続できない可能性があります。 SSL 接続を有効にする方法については、使用しているアプリケーションのドキュメントを参照してください。

## <a name="applications-that-require-a-local-certificate-for-ssl-connectivity"></a>SSL 接続でローカル証明書を必要とするアプリケーション
安全に接続するために、証明機関 (CA) 証明書ファイル (.cer) から生成されたローカルの証明書ファイル (.pem) がアプリケーションに必要な場合があります。  .cer ファイルを取得し、ローカルの .pem ファイルを生成して、アプリケーションにバインドするには、次の手順を参照してください。

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>証明機関 (CA) からの証明書ファイルのダウンロード
Azure Database for MySQL サーバーとの SSL 経由の通信に必要な証明書は[こちら](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)にあります。  証明書ファイルをローカル ドライブにダウンロードします (本チュートリアルでは、**c:\ssl** を使用します)。

### <a name="download-and-install-openssl-on-your-pc"></a>PC への OpenSSL のダウンロードとインストール
アプリケーションがデータベース サーバーに安全に接続するために必要なローカルの **.pem** ファイルを生成するには、OpenSSL をローカル コンピューターにインストールする必要があります。  

次のセクションで、使用方法について説明します。 優先する OS によって、Linux PC または Windows PC を使用できます。 従う必要があるのは 1 つの方法のみです。

### <a name="linux-users---download-and-install-openssl-using-a-linux-pc"></a>Linux ユーザー - Linux PC での OpenSSL のダウンロードとインストール
OpenSSL ライブラリは、[OpenSSL Software Foundation](http://www.openssl.org) から直接入手できるソース コードで提供されます。  次の手順では、Linux コンピューターに OpenSSL をインストールするために必要な手順を説明します。  このガイドで説明されるコマンドは、Ubuntu 12.04 以降向けとなっています。

ターミナル セッションを開いて、OpenSSL をインストールします
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
```  
ダウンロード パッケージからファイルを展開します
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
ファイルが展開されたディレクトリに移動します。  既定では、次のようになります。

```bash
cd openssl-1.1.0e
```
次のコマンドを実行して OpenSSL を構成します。/usr/local/openssl 以外のフォルダーにファイルを保存するには、必要に応じて次のコマンドを変更します。

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
OpenSSL が適切に構成されたら、コンパイルして証明書を変換する必要があります。  コンパイルするには、次のコマンドを実行します。

```bash
make
```
コンパイルが完了したら、次のコマンドを実行して、OpenSSL を実行可能ファイルとしてインストールできます。
```bash
make install
```
OpenSSL がシステムに適切にインストールされたことを確認するには、次のコマンドを実行します。 同じ出力を取得できることを確認します。

```bash
/usr/local/openssl/bin/openssl version
```
問題なくインストールできている場合は、次のメッセージが表示されます。
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

### <a name="windows-pc-users---download-and-install-openssl-using-a-windows-pc"></a>Windows PC ユーザー - Windows PC での OpenSSL のダウンロードとインストール
アプリケーションがデータベース サーバーに安全に接続するために必要なローカルの **.pem** ファイルを生成するには、OpenSSL をローカル コンピューターにインストールする必要があります。

OpenSSL ライブラリは、[OpenSSL Software Foundation](http://www.openssl.org) から直接入手できるソース コードで提供されます。 次の手順では、Linux コンピューターに OpenSSL をインストールするために必要な手順を説明します。

Windows PC で OpenSSL をインストールするには、次の手順を実行します。

1. **(推奨)** Windows 10 以降でビルトインの Bash for Windows 機能を使用する場合、OpenSSL が既定でインストールされます。  Windows 10 の Bash for Windows 機能を有効にする方法については、[こちら](https://msdn.microsoft.com/commandline/wsl/install_guide)をご覧ください。

2. コミュニティが提供する Win32/64 アプリケーションをダウンロードする場合。 OpenSSL Software Foundation では、特定の Windows インストーラーの提供および保証はしていません。使用できるインストーラーの一覧については、[こちら](https://wiki.openssl.org/index.php/Binaries)を参照してください。

### <a name="convert-your-cer-certificate-to-a-local-pem"></a>.cer 証明書のローカル .pem ファイルへの変換
ダウンロードしたルート CA ファイルは、**.crt** 形式になっています。 OpenSSL を使用して、証明書ファイルを **.pem** ファイルに変換します。  そのためには、openssl.exe コマンド ライン ツールを実行し、次のコマンドを実行します。

```dos
OpenSSL>x509 -inform DER -in BaltimoreCyberTrustRoot.crt -out MyServerCACert.pem
```
証明書ファイル (MyServerCACert.pem) が問題なく作成できたら、SSL 経由でデータベース サーバーに安全に接続できるようになります。

次の例では、MySQL コマンド ライン インターフェイスと MySQL Workbench を使用して MySQL サーバーに接続する方法について説明します。いずれの場合も、**MyServerCACert.pem** ファイルを使用します。

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>MySQL CLI による SSL 経由でのサーバーへの接続
MySQL コマンド ライン インターフェイスを使用して、次のコマンドを実行します。

```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -uUsername@mysqlserver4demo -pYourPassword --ssl-ca=c:\ssl\MyServerCACert.pem
```
MySQL の **status** コマンドを実行して、SSL 経由で MySQL サーバーに接続していることを確認します。

```dos
mysql> status
--------------
mysql.exe  Ver 14.14 Distrib 5.7.18, for Win64 (x86_64)

Connection id:          65535
Current database:
Current user:           jason@66.235.55.141
SSL:                    Cipher in use is AES256-SHA
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             janderserver.mysql.sqltest-eg1.mscds.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    cp850
Conn.  characterset:    cp850
TCP port:               3306
Uptime:                 1 day 19 hours 9 min 43 sec

Threads: 4  Questions: 26082  Slow queries: 0  Opens: 112  Flush tables: 1  Open tables: 105  Queries per second avg: 0.167
--------------
```

> [!NOTE]
> 現在、サービスへの mysql.exe 接続で "--ssl-mode=VERIFY_IDENTITY" オプションを使用した場合に、接続が次のエラーで失敗するという既知の問題が確認されています。_ERROR 2026 (HY000): SSL connection error: SSL certificate validation failure_ Downgrade to "--ssl-mode=VERIFY_CA" or lesser [SSL modes](https://dev.mysql.com/doc/refman/5.7/en/secure-connection-options.html#option_general_ssl-mode) (エラー 2026 (HY000): SSL 接続エラー: SSL 証明書の検証エラー "--ssl-mode=VERIFY_CA" または以前の SSL モードにダウングレードしてください)。 "--ssl-mode=VERIFY_IDENTITY" を使用する必要がある場合は、この問題が解決するまで、リージョン サーバー名を使用してください。 サーバー名 (`westeurope1-a.control.database.windows.net` など) を ping してリージョン サーバーを確認し、確認したリージョン サーバー名を接続で使用してください。 この制限は、今後削除する予定です。

### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>MySQL Workbench による SSL 経由でのサーバーへの接続
SSL 経由で安全に接続するように MySQL Workbench を構成します。 MySQL workbench で、[新しい接続のセットアップ] ダイアログの **[SSL]** タブに移動します。 **MyServerCACert.pem** ファイルの場所を **[SSL CA ファイル:]** フィールドに入力します。
![カスタマイズしたタイルの保存](./media/howto-configure-ssl/mysql-workbench-ssl.png)

## <a name="next-steps"></a>次のステップ
[Azure Database for MySQL の接続ライブラリ](concepts-connection-libraries.md)に従って、さまざまなアプリケーション接続オプションを確認する


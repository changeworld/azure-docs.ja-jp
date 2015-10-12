<properties
	pageTitle="Azure SDK for PHP をダウンロードする"
	description="Azure SDK for PHP をダウンロードしてインストールする方法について説明します。"
	documentationCenter="php"
	services=""
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="tomfitz"/>

#Azure SDK for PHP をダウンロードする

## 概要

Azure SDK for PHP には、Azure 向けの PHP アプリケーションを開発、デプロイ、管理するためのコンポーネントが用意されています。Azure SDK for PHP には次のコンポーネントが用意されています。

* **Azure 用 PHP クライアント ライブラリ**。これらのクラス ライブラリには、データ管理サービスやクラウド サービスなどの Azure の機能にアクセスするためのインターフェイスが用意されています。  
* **Mac、Linux、Windows の Azure コマンド ライン インターフェイス (Azure CLI)**これは、Azure Websites や Azure Virtual Machines などの Azure サービスをデプロイおよび管理するためのコマンドのセットです。Azure CLI は、Mac、Linux、Windows など、すべてのプラットフォームで動作します。
* **Azure PowerShell (Windows のみ)**。これは、Cloud Services や Virtual Machines などの Azure サービスをデプロイおよび管理するための PowerShell コマンドレットのセットです。
* **Azure エミュレーター (Windows のみ)**。コンピューティング エミュレーターとストレージ エミュレーターは、アプリケーションをローカルでテストできるようにするためのクラウド サービスおよびデータ管理サービスのローカル エミュレーターです。Azure エミュレーターは Windows 上でのみ動作します。

以下のセクションでは、上に示したコンポーネントをダウンロードおよびインストールする方法を説明します。

このトピックの手順では、[PHP][install-php] がインストールされていることを前提としています。

> [AZURE.NOTE]Azure 用 PHP クライアント ライブラリを使用するには、PHP 5.3 以上が必要です。

##Windows Azure 用 PHP クライアント ライブラリ

Azure 用 PHP クライアント ライブラリには、任意のオペレーティング システムからデータ管理サービスやクラウド サービスなどの Azure の機能にアクセスするためのインターフェイスが用意されています。これらのライブラリは、Composer または PEAR パッケージ マネージャーを使用してインストールすることも、手動でインストールすることもできます。

Azure 用 PHP クライアント ライブラリを使用する方法については、「[BLOB サービスの使用方法][blob-service]」、「[テーブル サービスの使用方法][table-service]」、および「[キュー サービスの使用方法][queue-service]」を参照してください。

###Composer 経由でインストールする

1. [Git をインストールします][install-git]。


	> [AZURE.NOTE]Windows では、Git 実行可能ファイルを PATH 環境変数に追加する必要があります。

2. プロジェクトのルートに **composer.json** という名前のファイルを作成して、次のコードを追加します。

        {
            "repositories": [
                {
                    "type": "pear",
                    "url": "http://pear.php.net"
                }
            ],
            "require": {
                "pear-pear.php.net/mail_mime" : "*",
                "pear-pear.php.net/http_request2" : "*",
                "pear-pear.php.net/mail_mimedecode" : "*",
                "microsoft/windowsazure": "*"
            }
        }

3. **[composer.phar][composer-phar]** をプロジェクトのルートにダウンロードします。

4. コマンド プロンプトを開き、次のコマンドをプロジェクトのルートで実行します。

		php composer.phar install

###PEAR パッケージとしてインストールする

Azure 向け PHP クライアント ライブラリを PEAR パッケージとしてインストールするには、次のステップに従います。

1. [PEAR をインストールします][install-pear]。
2. Azure PEAR チャネルをセットアップします。

		pear channel-discover pear.windowsazure.com
3. PEAR パッケージをインストールします。

		pear install pear.windowsazure.com/WindowsAzure-0.4.0

インストールが完了した後は、アプリケーションからクラス ライブラリを参照できます。

###手動でインストールする

Azure 向け PHP クライアント ライブラリを手動でダウンロードしてインストールするには、次のステップに従います。

1. ライブラリが含まれている .zip アーカイブを [GitHub][php-sdk-github] からダウンロードします。または、リポジトリをフォークして、ローカル コンピューターにその複製を作成します(この 2 つ目の方法では、GitHub アカウントが必要であるほか、Git がローカルにインストールされている必要があります)。

	> [AZURE.NOTE]Azure 向け PHP クライアント ライブラリは、[HTTP\_Request2](http://pear.php.net/package/HTTP_Request2)、[Mail\_mime](http://pear.php.net/package/Mail_mime)、および [Mail\_mimeDecode](http://pear.php.net/package/Mail_mimeDecode) の PEAR パッケージに依存しています。これらの依存関係を解決するには、[PEAR パッケージ マネージャー](http://pear.php.net/manual/en/installation.php)を使用してこれらのパッケージをインストールすることをお勧めします。

2. ダウンロードしたアーカイブの `WindowsAzure` ディレクトリをアプリケーションのディレクトリ構造にコピーし、アプリケーションからクラスを参照します。

##Azure PowerShell と Azure エミュレーター

Azure PowerShell は、Cloud Services や Virtual Machines などの Azure サービスをデプロイおよび管理するための PowerShell コマンドレットのセットです。Azure エミュレーターは、アプリケーションをローカルでテストできるようにするためのクラウド サービスおよびデータ管理サービスのエミュレーターです。これらのコンポーネントは、Windows のみでサポートされています。

Azure PowerShell と Azure エミュレーターは、[Microsoft Web プラットフォーム インストーラー][download-wpi]を使用してインストールすることをお勧めします。PHP、SQL Server、Microsoft Drivers for SQL Server for PHP、WebMatrix など、他の開発用コンポーネントをインストールすることもできます。

Azure PowerShell の使用方法については、「[Azure PowerShell の使用方法][powershell-tools]」を参照してください。

##Azure CLI

Azure CLI は、Azure Websites や Azure Virtual Machines などの Azure サービスをデプロイおよび管理するためのコマンド ライン ツールのセットです。Azure CLI のインストール方法については、「[Azure CLI のインストール](xplat-cli-install.md)」を参照してください。

## 次のステップ

詳細については、[PHP デベロッパー センター](/develop/php/)を参照してください。


[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[pear-net]: http://pear.php.net/
[http-request2-package]: http://pear.php.net/package/HTTP_Request2
[mail-mimedecode-package]: http://pear.php.net/package/Mail_mimeDecode
[mail-mime-package]: http://pear.php.net/package/Mail_mime
[install-pear]: http://pear.php.net/manual/en/installation.getting.php
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git

<!---HONumber=Oct15_HO1-->
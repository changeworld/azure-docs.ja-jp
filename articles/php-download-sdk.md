<properties title="Download the Azure SDK for PHP" pageTitle="Download the Azure SDK for PHP" metaKeywords="" description="Learn how to download and install the Azure SDK for PHP." documentationCenter="PHP" services="" solutions="web" authors="robmcm" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Azure SDK for PHP をダウンロードする

Azure SDK for PHP には、Azure 向けの PHP アプリケーションを開発、デプロイ、管理するためのコンポーネントが用意されています。Azure SDK for PHP には次のコンポーネントが用意されています。

-   **Azure 用 PHP クライアント ライブラリ**。これらのクラス ライブラリには、データ管理サービスやクラウド サービスなどの Azure の機能にアクセスするためのインターフェイスが用意されています。
-   **Mac および Linux 用 Azure コマンド ライン ツール**。これは、Azure Websites や Azure Virtual Machines などの Azure サービスをデプロイおよび管理するためのコマンド ライン ツールのセットです。これらのツールは、Mac、Linux、Windows など、すべてのプラットフォームで動作します。
-   **Azure PowerShell (Windows のみ)**。これは、クラウド サービスや仮想マシンなどの Azure サービスをデプロイおよび管理するための PowerShell コマンドレットのセットです。
-   **Azure エミュレーター (Windows のみ)**。コンピューティング エミュレーターとストレージ エミュレーターは、アプリケーションをローカルでテストできるようにするためのクラウド サービスおよびデータ管理サービスのローカル エミュレーターです。Azure エミュレーターは Windows 上でのみ動作します。

以下のセクションでは、上に示したコンポーネントをダウンロードおよびインストールする方法を説明します。

このトピックの手順では、[PHP][PHP] がインストールされていることを前提としています。

> [WACOM.NOTE]
> Azure 用 PHP クライアント ライブラリを使用するには、PHP 5.3 以上が必要です。

## Windows Azure 用 PHP クライアント ライブラリ

Azure 用 PHP クライアント ライブラリには、任意のオペレーティング システムからデータ管理サービスやクラウド サービスなどの Azure の機能にアクセスするためのインターフェイスが用意されています。これらのライブラリは、Composer または PEAR パッケージ マネージャーを使用してインストールすることも、手動でインストールすることもできます。

Azure 用 PHP クライアント ライブラリを使用する方法については、「[BLOB サービスの使用方法][BLOB サービスの使用方法]」、「[テーブル サービスの使用方法][テーブル サービスの使用方法]」、および「[キュー サービスの使用方法][キュー サービスの使用方法]」を参照してください。

### Composer 経由でインストールする

1.  [Git をインストールします][Git をインストールします]。

    > [WACOM.NOTE]
    > Windows では、Git 実行可能ファイルを PATH 環境変数に追加する必要があります。

2.  プロジェクトのルートに **composer.json** という名前のファイルを作成して、次のコードを追加します。

        {
            "require": {
                "microsoft/windowsazure": "*"
            },          
            "repositories": [
                {
                    "type": "pear",
                    "url": "http://pear.php.net"
                }
            ],
            "minimum-stability": "dev"
        }

3.  **[composer.phar][composer.phar]** をプロジェクトのルートにダウンロードします。

4.  コマンド プロンプトを開き、次のコマンドをプロジェクトのルートで実行します。

        php composer.phar install

### PEAR パッケージとしてインストールする

Azure 向け PHP クライアント ライブラリを PEAR パッケージとしてインストールするには、次のステップに従います。

1.  [PEAR をインストールします][PEAR をインストールします]。
2.  Azure PEAR チャネルをセットアップします。

        pear channel-discover pear.windowsazure.com

3.  PEAR パッケージをインストールします。

        pear install pear.windowsazure.com/WindowsAzure-0.3.1

インストールが完了した後は、アプリケーションからクラス ライブラリを参照できます。

### 手動でインストールする

Azure 向け PHP クライアント ライブラリを手動でダウンロードしてインストールするには、次のステップに従います。

1.  ライブラリが含まれている .zip アーカイブを [GitHub][GitHub] からダウンロードします。または、リポジトリをフォークして、ローカル コンピューターにその複製を作成します (この 2 つ目の方法では、GitHub アカウントが必要であるほか、Git がローカルにインストールされている必要があります)。

    > [WACOM.NOTE]
    > Azure 向け PHP クライアント ライブラリは、[HTTP\_Request2][HTTP\_Request2]、[Mail\_mime][Mail\_mime]、および [Mail\_mimeDecode][Mail\_mimeDecode] の PEAR パッケージに依存しています。これらの依存関係を解決するには、[PEAR パッケージ マネージャー][PEAR パッケージ マネージャー]を使用してこれらのパッケージをインストールすることをお勧めします。

2.  ダウンロードしたアーカイブの `WindowsAzure` ディレクトリをアプリケーションのディレクトリ構造にコピーし、アプリケーションからクラスを参照します。

## Azure PowerShell と Azure エミュレーター

Azure PowerShell は、Cloud Services や Virtual Machines などの Azure サービスをデプロイおよび管理するための PowerShell コマンドレットのセットです。Azure エミュレーターは、アプリケーションをローカルでテストできるようにするためのクラウド サービスおよびデータ管理サービスのエミュレーターです。これらのコンポーネントは、Windows のみでサポートされています。

Azure PowerShell と Azure エミュレーターは、[Microsoft Web プラットフォーム インストーラー][Microsoft Web プラットフォーム インストーラー]を使用してインストールすることをお勧めします。PHP、SQL Server、Microsoft Drivers for SQL Server for PHP、WebMatrix など、他の開発用コンポーネントをインストールすることもできます。

Azure PowerShell の使用方法については、「[Azure PowerShell の使用方法][Azure PowerShell の使用方法]」を参照してください。

## Mac および Linux 用 Azure コマンド ライン ツール

Mac および Linux 用 Azure コマンド ライン ツールは、Azure Websites や Azure Virtual Machines などの Azure サービスをデプロイおよび管理するためのコマンド ライン ツールのセットです。オペレーティング システムに応じた、ツールのインストール方法を次に示します。

-   **Mac**: Azure SDK インストーラーをダウンロードします。[][]<http://go.microsoft.com/fwlink/?LinkId=252249></a>。ダウンロードした .pkg ファイルを開き、指示に従ってインストール手順を実行します。

-   **Linux**: 最新バージョンの [Node.js][Node.js] をインストールし ([パッケージ マネージャーを使った Node.js のインストールに関するページ][パッケージ マネージャーを使った Node.js のインストールに関するページ]を参照)、次のコマンドを実行します。

        npm install azure-cli -g

    > [WACOM.NOTE]
    > 昇格した特権で次のコマンドを実行することが必要になる場合があります。 `sudo npm install azure-cli -g`

Mac および Linux 用 Azure コマンド ライン ツールの使用方法については、「[Mac および Linux 用コマンド ライン ツールの使用方法][Mac および Linux 用コマンド ライン ツールの使用方法]」を参照してください。

  [PHP]: http://www.php.net/manual/en/install.php
  [BLOB サービスの使用方法]: http://go.microsoft.com/fwlink/?LinkId=252714
  [テーブル サービスの使用方法]: http://go.microsoft.com/fwlink/?LinkId=252715
  [キュー サービスの使用方法]: http://go.microsoft.com/fwlink/?LinkId=252716
  [Git をインストールします]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [composer.phar]: http://getcomposer.org/composer.phar
  [PEAR をインストールします]: http://pear.php.net/manual/en/installation.getting.php
  [GitHub]: http://go.microsoft.com/fwlink/?LinkId=252719
  [HTTP\_Request2]: http://pear.php.net/package/HTTP_Request2
  [Mail\_mime]: http://pear.php.net/package/Mail_mime
  [Mail\_mimeDecode]: http://pear.php.net/package/Mail_mimeDecode
  [PEAR パッケージ マネージャー]: http://pear.php.net/manual/en/installation.php
  [Microsoft Web プラットフォーム インストーラー]: http://go.microsoft.com/fwlink/?LinkId=253447
  [Azure PowerShell の使用方法]: http://go.microsoft.com/fwlink/?LinkId=252718
  []: http://go.microsoft.com/fwlink/?LinkId=252249
  [Node.js]: http://nodejs.org/
  [パッケージ マネージャーを使った Node.js のインストールに関するページ]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
  [Mac および Linux 用コマンド ライン ツールの使用方法]: http://go.microsoft.com/fwlink/?LinkId=252717

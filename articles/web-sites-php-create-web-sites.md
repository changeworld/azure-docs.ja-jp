<properties title="How to create a PHP website in Azure Websites" pageTitle="How to create a PHP website in Azure Websites" metaKeywords="PHP Azure Web Sites" description="Learn how to create a PHP website in Azure Websites" documentationCenter="PHP" services="Web Sites" editor="mollybos" manager="wpickett" authors="cephalin" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Azure Websites での PHP Web サイトの作成方法

この記事では、[Azure 管理ポータル][Azure 管理ポータル]、[Mac および Linux 用 Azure コマンド ライン ツール][Mac および Linux 用 Azure コマンド ライン ツール]、または [Azure PowerShell コマンドレット][Azure PowerShell コマンドレット]を使用して、[Azure Websites][Azure Websites]で PHP Web サイトを作成する方法を示します。

一般的に、PHP Web サイトの作成は、Azure Websites で他の Web サイトを作成する場合と変わりません。既定で、PHP はすべての Web サイトに対して有効になっています。PHP の構成 (または独自にカスタマイズした PHP ランタイムの使用) については、「[Azure の Web サイトでの PHP の構成方法][Azure の Web サイトでの PHP の構成方法]」を参照してください。

ここで説明する各オプションは、共有ホスティング環境を利用して無料で Web サイトを作成する方法を示していますが、CPU と帯域幅の使用については一定の制限があります。詳細については、[Azure の Web サイトの料金詳細][Azure の Web サイトの料金詳細]を参照してください。Web サイトをアップグレードおよび規模を変更する方法については、「[Web サイトの規模の設定方法][Web サイトの規模の設定方法]」を参照してください。

## 目次

-   [Azure 管理ポータルを使用して Web サイトを作成する][Azure 管理ポータルを使用して Web サイトを作成する]
-   [Mac および Linux 用 Azure コマンド ライン ツールを使用して Web サイトを作成する][Mac および Linux 用 Azure コマンド ライン ツールを使用して Web サイトを作成する]
-   [Azure PowerShell コマンドレットを使用して Web サイトを作成する][Azure PowerShell コマンドレットを使用して Web サイトを作成する]

## <a name="portal"></a>Azure 管理ポータルを使用して PHP Web サイトを作成する

Azure 管理ポータルで Web サイトを作成する場合、**[簡易作成]**、**[データベースと共に作成]**、**[ギャラリーから]** という 3 つのオプションがあります。ここに示す手順では、**[簡易作成]** オプションを使用します。他の 2 つのオプションについては、「[PHP-MySQL Azure の Web サイトを作成して Git で展開する][PHP-MySQL Azure の Web サイトを作成して Git で展開する]」および「[Azure でギャラリーから WordPress Web サイトを作成する][Azure でギャラリーから WordPress Web サイトを作成する]」を参照してください。

Azure の管理ポータルを使用して PHP Web サイトを作成するには、次の操作を行います。

1.  [Azure の管理ポータル][Azure 管理ポータル]にログインします。
2.  ページの下部にある **[新規]** をクリックし、**[コンピューティング]**、**[Web サイト]**、**[簡易作成]** の順にクリックします。Web サイトの **URL** を指定し、適切な**リージョン**を選択します。最後に、**[Web サイトの作成]** をクリックします。

    ![Web サイトの簡易作成を選択する][Web サイトの簡易作成を選択する]

## <a name="XplatTools"></a>Mac および Linux 用 Azure コマンド ライン ツールを使用して PHP Web サイトを作成する

Mac および Linux 用 Azure コマンド ライン ツールを使用して PHP Web サイトを作成するには、次の操作を行います。

1.  「[Mac および Linux 用 Azure コマンド ライン ツールのインストール方法][Mac および Linux 用 Azure コマンド ライン ツールのインストール方法]」に記載されている手順に従って、Azure コマンド ライン ツールをインストールします。

2.  次の手順に従って発行設定ファイルをダウンロードおよびインポートします: [発行の設定をダウンロードおよびインポートする方法][発行の設定をダウンロードおよびインポートする方法]。

3.  コマンド プロンプトで、次のコマンドを実行します。

        azure site create MySiteName

新しく作成した Web サイトの URL は `http://MySiteName.azurewebsites.net` になります。

`azure site create` コマンドは、次のいずれかのオプションと組み合わせて実行できます。

-   `--location [location name]`。このオプションを使用すると、Web サイトを作成するデータ センターの場所を指定できます (たとえば、"米国西部")。このオプションを省略した場合、場所を選択するよう促されます。
-   `--hostname [custom host name]`。このオプションを使用すると、Web サイトのカスタム ホスト名を指定できます。
-   `--git`。このオプションを指定すると、git を使用してローカル アプリケーション ディレクトリと Web サイトのデータ センターの両方に git リポジトリを作成して Web サイトに発行できます。ローカル フォルダーが既に git リポジトリである場合、このコマンドを実行すると新しいリモートが既存のリポジトリに追加され、Web サイトのデータ センター内のリポジトリが指定されます。

その他のオプションについては、「[Azure の Web サイトを作成および管理する方法][Azure の Web サイトを作成および管理する方法]」を参照してください。

## <a name="PowerShell"></a>Azure PowerShell コマンドレットを使用して PHP Web サイトを作成する

Azure PowerShell コマンドレットを使用して PHP Web サイトを作成するには、次の操作を行います。

1.  「[Azure PowerShell の概要][Azure PowerShell の概要]」に記載されている手順に従って、Azure PowerShell コマンドレットをインストールします。

2.  次の手順に従って発行設定ファイルをダウンロードおよびインポートします: [方法: 発行の設定をインポートする][方法: 発行の設定をインポートする]。

3.  PowerShell コマンド プロンプトを開き、次のコマンドを実行します。

        New-AzureWebSite MySiteName

新しく作成した Web サイトの URL は `http://MySiteName.azurewebsites.net` になります。

`New-AzureWebSite` コマンドは、次のいずれかのオプションと組み合わせて実行できます。

-   `-Location [location name]`。このオプションを使用すると、Web サイトを作成するデータ センターの場所を指定できます (たとえば、"米国西部")。このオプションを省略した場合、場所を選択するよう促されます。
-   `-Hostname [custom host name]`。このオプションを使用すると、Web サイトのカスタム ホスト名を指定できます。
-   `-Git`。このオプションを指定すると、git を使用してローカル アプリケーション ディレクトリと Web サイトのデータ センターの両方に git リポジトリを作成して Web サイトに発行できます。ローカル フォルダーが既に git リポジトリである場合、このコマンドを実行すると新しいリモートが既存のリポジトリに追加され、Web サイトのデータ センター内のリポジトリが指定されます。

その他のオプションについては、「[方法: Azure の Web サイトを作成および管理する][方法: Azure の Web サイトを作成および管理する]」を参照してください。

## <a name="NextSteps"></a>次のステップ

Azure Websites で PHP Web サイトを作成したので、サイトの管理、構成、監視、規模設定、およびこのサイトへのデプロイを行うことができます。詳細については、次のリンクを参照してください。

-   [Web サイトの構成方法][Web サイトの構成方法]
-   [Azure の Web サイトでの PHP の構成方法][Azure の Web サイトでの PHP の構成方法]
-   [Web サイトの管理方法][Web サイトの管理方法]
-   [Web サイトの監視方法][Web サイトの監視方法]
-   [Web サイトの規模変更方法][Web サイトの規模の設定方法]
-   [Git を使用した発行][Git を使用した発行]

エンド ツー エンドのチュートリアルについては、「[PHP デベロッパー センター - チュートリアル][PHP デベロッパー センター - チュートリアル]」を参照してください。

  [Azure 管理ポータル]: http://manage.windowsazure.com/
  [Mac および Linux 用 Azure コマンド ライン ツール]: /ja-jp/develop/php/how-to-guides/command-line-tools/
  [Azure PowerShell コマンドレット]: /ja-jp/develop/php/how-to-guides/powershell-cmdlets/
  [Azure Websites]: /ja-jp/manage/services/web-sites/
  [Azure の Web サイトでの PHP の構成方法]: /ja-jp/develop/php/common-tasks/configure-php-web-site/
  [Azure の Web サイトの料金詳細]: http://www.windowsazure.com/ja-jp/pricing/details/#header-1
  [Web サイトの規模の設定方法]: /ja-jp/manage/services/web-sites/how-to-scale-websites/
  [Azure 管理ポータルを使用して Web サイトを作成する]: #portal
  [Mac および Linux 用 Azure コマンド ライン ツールを使用して Web サイトを作成する]: #XplatTools
  [Azure PowerShell コマンドレットを使用して Web サイトを作成する]: #PowerShell
  [PHP-MySQL Azure の Web サイトを作成して Git で展開する]: /ja-jp/develop/php/tutorials/website-w-mysql-and-git/
  [Azure でギャラリーから WordPress Web サイトを作成する]: /ja-jp/develop/php/tutorials/website-from-gallery/
  [Web サイトの簡易作成を選択する]: ./media/web-sites-php-create-web-sites/select-quickcreate-website.png
  [Mac および Linux 用 Azure コマンド ライン ツールのインストール方法]: /ja-jp/develop/php/how-to-guides/command-line-tools/#Download
  [発行の設定をダウンロードおよびインポートする方法]: /ja-jp/develop/php/how-to-guides/command-line-tools/#Account
  [Azure の Web サイトを作成および管理する方法]: /ja-jp/develop/php/how-to-guides/command-line-tools/#WebSites
  [Azure PowerShell の概要]: /ja-jp/develop/php/how-to-guides/powershell-cmdlets/#GetStarted
  [方法: 発行の設定をインポートする]: /ja-jp/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings
  [方法: Azure の Web サイトを作成および管理する]: /ja-jp/develop/php/how-to-guides/powershell-cmdlets/#WebSite
  [Web サイトの構成方法]: /ja-jp/manage/services/web-sites/how-to-configure-websites/
  [Web サイトの管理方法]: /ja-jp/manage/services/web-sites/how-to-manage-websites/
  [Web サイトの監視方法]: /ja-jp/manage/services/web-sites/how-to-monitor-websites/
  [Git を使用した発行]: /ja-jp/develop/php/common-tasks/publishing-with-git/
  [PHP デベロッパー センター - チュートリアル]: /ja-jp/develop/php/tutorials/

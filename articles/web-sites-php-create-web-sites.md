<properties title="How to create a PHP website in Azure Websites" pageTitle="Azure Web サイトでの PHP Web サイトの作成方法" metaKeywords="PHP Azure Web Sites" description="Azure Websites で PHP Web サイトを作成する方法について説明します。" documentationCenter="PHP" services="Web Sites" editor="mollybos" manager="wpickett" authors="tomfitz" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/21/2014" ms.author="tomfitz" />

#Azure Web サイトでの PHP Web サイトの作成方法

この記事では、[ Azure の管理ポータル]、[Mac および Linux 用 Azure コマンド ライン ツール][xplat-tools] または、[Azure PowerShell コマンドレット][powershell-cmdlets] を使用して、[Azure の Web サイト][waws] でPHP Web サイトを作成する方法を示します。

全般的に、PHP Web サイトの作成は、Azure web サイトで他の Web サイトを作成する場合と変わりません。既定で、PHP はすべての Web サイトに対して有効になっています。PHP の構成 (または独自にカスタマイズした PHP ランタイムの使用) については、「[Azure の Web サイトでの PHP の構成方法][configure-php]」を参照してください。

ここで説明する各オプションは、共有ホスティング環境を利用して無料で Web サイトを作成する方法を示していますが、CPU と帯域幅の使用については一定の制限があります。詳細については、[Azure の Web サイトの料金詳細][websites-pricing]を参照してください。Web サイトをアップグレードおよび規模を変更する方法については、[How to scale Web Sites (Web サイトの規模の設定方法)][scale-websites] を参照してください。

> [WACOM.NOTE]
> アカウントにサインアップする前に、Azure Web サイトの使用を開始する場合は、Azure Web サイト <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>で、無料で期間限定の ASP.NET スターター サイト を作成できます。クレジット カードは必要ありません。また、支払いも発生しません。

##目次
* [Azure 管理ポータルを使用して Web サイトを作成する](#portal)
* [Mac および Linux 用 Azure コマンド ライン ツールを使用して Web サイトを作成する](#XplatTools)
* [Azure PowerShell コマンドレットを使用して Web サイトを作成する](#PowerShell)

<h2><a name="portal"></a>Azure 管理ポータルを使用して PHP Web サイトを作成する</h2>

Azure 管理ポータルで Web サイトを作成する場合、**[簡易作成]**、**[データベースと共に作成]**、**[ギャラリーから]** という 3 つのオプションがあります。ここに示す手順では、**[簡易作成]** オプションを使用します。他の 2 つのオプションについては、「[PHP-MySQL Azure の Web サイトを作成して Git で展開する][website-mysql-git]」および「[Azure でギャラリーから WordPress Web サイトを作成する][wordpress-gallery]」を参照してください。

Azure の管理ポータルを使用して PHP Web サイトを作成するには、次の操作を行います。

1. [Azure 管理ポータル] にログインします。
1. ページの下部にある **[新規]** をクリックし、 **[コンピューティング]**、**[Web サイト]**、**[簡易作成]** の順にクリックします。Web サイトの **URL**を指定し、適切な **リージョン** を選択します。最後に、**[Web サイトの作成]** をクリックします。

	![Web サイトの簡易作成を選択する](./media/web-sites-php-create-web-sites/select-quickcreate-website.png)

<h2><a name="XplatTools"></a>Mac および Linux 用 Azure コマンド ライン ツールを使用して PHP Web サイトを作成する</h2>

Mac および Linux 用 Azure コマンド ライン ツールを使用して PHP Web サイトを作成するには、次の操作を行います。

1. ここでの指示に従って、Azure コマンド ライン ツールをインストールします。[How to install the Azure Command Line Tools for Mac and Linux (Mac および Linux 用 Azure コマンド ライン ツールをインストールする方法)](/ja-jp/develop/php/how-to-guides/command-line-tools/#Download)。

1. 次の手順に従って発行設定ファイルをダウンロードおよびインポートします:[How to download and import publish settings (発行の設定をダウンロードおよびインポートする方法)](/ja-jp/develop/php/how-to-guides/command-line-tools/#Account)。

1. コマンド プロンプトで、次のコマンドを実行します。

		azure site create MySiteName

新しく作成した Web サイトの URL は `http://MySiteName.azurewebsites.net` になります。  
 
`azure site create` コマンドは、次のいずれかのオプションと組み合わせて実行できます。

* `--location [location name]`。このオプションを使用すると、Web サイトを作成するデータ センターの場所を指定できます (例:「米国西部」)。このオプションを省略した場合、場所を選択するよう促されます。
* `--hostname [custom host name]`。このオプションを使用すると、Web サイトのカスタム ホスト名を指定できます。
* `--git` 。このオプションを指定すると、git を使用してローカル アプリケーション ディレクトリと Web サイトのデータ センターの両方に git リポジトリを作成して Web サイトに発行できます。ローカル フォルダーが既に git リポジトリである場合、このコマンドを実行すると新しいリモートが既存のリポジトリに追加され、Web サイトのデータ センター内のリポジトリが指定されます。

その他のオプションについては、次を参照してください。 [How to create and manage an Azure Web Site (Azure の Web サイトを作成および管理する方法)](/ja-jp/develop/php/how-to-guides/command-line-tools/#WebSites)。

<h2><a name="PowerShell"></a>Azure PowerShell コマンドレットを使用して PHP Web サイトを作成する</h2>

Azure PowerShell コマンドレットを使用して PHP Web サイトを作成するには、次の操作を行います。

1. ここでの指示に従って、Azure PowerShell コマンドレットをインストールします。[Get started with Azure PowerShell (Azure PowerShell の概要)](/ja-jp/develop/php/how-to-guides/powershell-cmdlets/#GetStarted)。

1. 次の手順に従って発行設定ファイルをダウンロードおよびインポートします:[How to:Import publish settings (発行の設定をインポートする方法)](/ja-jp/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings)。

1. PowerShell コマンド プロンプトを開き、次のコマンドを実行します。

		New-AzureWebSite MySiteName

新しく作成した Web サイトの URL は `http://MySiteName.azurewebsites.net` になります。  
 
`New-AzureWebSite` コマンドは、次のいずれかのオプションと組み合わせて実行できます。

* `-Location [location name]` です。このオプションを使用すると、Web サイトを作成するデータ センターの場所を指定できます (例:「米国西部」)。このオプションを省略した場合、場所を選択するよう促されます。
* `-Hostname [custom host name]`。このオプションを使用すると、Web サイトのカスタム ホスト名を指定できます。
* `-Git`。このオプションを指定すると、git を使用してローカル アプリケーション ディレクトリと Web サイトのデータ センターの両方に git リポジトリを作成して Web サイトに発行できます。ローカル フォルダーが既に git リポジトリである場合、このコマンドを実行すると新しいリモートが既存のリポジトリに追加され、Web サイトのデータ センター内のリポジトリが指定されます。

その他のオプションについては、次を参照してください。[How to:Create and manage an Azure Web Site (Azure の Web サイトを作成および管理する方法)](/ja-jp/develop/php/how-to-guides/powershell-cmdlets/#WebSite).

<h2><a name="NextSteps"></a>次のステップ</h2>

Azure Web サイトで PHP Web サイトを作成したため、サイトの管理、構成、監視、規模設定、このサイトへのデプロイを行うことができます。詳細については、次のリンクを参照してください。

* [How to configure Web Sites (Web サイトを構成する方法)](/ja-jp/manage/services/web-sites/how-to-configure-websites/)
* [How to configure PHP in Azure Web Sites (Azure の Web サイトでの PHP の構成方法)][configure-php]
* [How to manage Web Sites (Web サイトの管理方法)](/ja-jp/manage/services/web-sites/how-to-manage-websites/)
* [How to monitor Web Sites (Web サイトの監視方法)](/ja-jp/manage/services/web-sites/how-to-monitor-websites/)
* [How to scale Web SitesWeb (サイトの規模変更方法)](/ja-jp/manage/services/web-sites/how-to-scale-websites/)
* [Publishing with Git (Git を使用した発行)](/ja-jp/develop/php/common-tasks/publishing-with-git/)

エンド ツー エンドのチュートリアルについては、[PHP Developer Center - Tutorials (PHP デベロッパー センター - チュートリアル)] (/ja-jp/develop/php/tutorials/) ページを参照してください。

[waws]: /ja-jp/manage/services/web-sites/
[Azure 管理ポータル]: http://manage.windowsazure.com/
[xplat-tools]: /ja-jp/develop/php/how-to-guides/command-line-tools/
[powershell-cmdlets]: /ja-jp/develop/php/how-to-guides/powershell-cmdlets/
[configure-php]: /ja-jp/develop/php/common-tasks/configure-php-web-site/
[website-mysql-git]: /ja-jp/develop/php/tutorials/website-w-mysql-and-git/
[wordpress-gallery]: /ja-jp/develop/php/tutorials/website-from-gallery/
[websites-pricing]: http://www.windowsazure.com/ja-jp/pricing/details/#header-1
[scale-websites]: /ja-jp/manage/services/web-sites/how-to-scale-websites/

<!--HONumber=35.2-->

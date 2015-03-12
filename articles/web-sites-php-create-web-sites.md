<properties 
	pageTitle="Azure Web サイトでの PHP Web サイトの作成方法" 
	description="Azure Websites で PHP Web サイトを作成する方法について説明します。" 
	documentationCenter="php" 
	services="" 
	editor="mollybos" 
	manager="wpickett" 
	authors="tfitzmac"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="10/21/2014" 
	ms.author="tomfitz"/>

#Azure Web サイトでの PHP Web サイトの作成方法

この記事では、[Azure の管理ポータル]、[Mac および Linux 用 Azure コマンド ライン ツール][xplat-tools] または、[Azure PowerShell コマンドレット][powershell-cmdlets] を使用して、[Azure の Web サイト][waws]で PHP Web サイトを作成する方法を示します。

全般的に、PHP Web サイトの作成は、Azure Web サイトで他の Web サイトを作成する場合と変わりません。 *any*[CO1]既定で、PHP はすべての Web サイトに対して有効になっています。PHP の構成 (または独自にカスタマイズした PHP ランタイムの使用) については、「[Azure の Web サイトでの PHP の構成方法][configure-php].」をご覧ください。

ここで説明する各オプションは、共有ホスティング環境を利用して無料で Web サイトを作成する方法を示していますが、CPU と帯域幅の使用については一定の制限があります。詳細については、[Azure の Web サイトの料金詳細][websites-pricing]をご覧ください。Web サイトをアップグレードおよび規模を変更する方法については、「[How to scale Web Sites (Web サイトの規模の設定方法)][scale-websites]」をご覧ください。

> [AZURE.NOTE]
> アカウントにサインアップする前に Azure Web サイトを実際に使ってみるには、<a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a> にアクセスしてください。Azure Web サイトで、有効期限付きの ASP.NET スターター サイトを無償で簡単に作成できます。クレジット カードは必要ありません。また、支払いも発生しません。

##目次
* [Azure 管理ポータルを使用して Web サイトを作成する](#portal)
* [Mac および Linux 用 Azure コマンド ライン ツールを使用して Web サイトを作成する](#XplatTools)
* [Azure PowerShell コマンドレットを使用して Web サイトを作成する](#PowerShell)

<h2><a name="portal"></a>Azure 管理ポータルを使用して PHP Web サイトを作成する</h2>

Azure 管理ポータルで Web サイトを作成する場合、**[簡易作成]**、**[データベースと共に作成]**、**[ギャラリーから]** という 3 つのオプションがあります。ここに示す手順では、**[簡易作成]** オプションを使用します。他の 2 つのオプションについては、「[PHP-MySQL Azure の Web サイトを作成して Git で展開する][website-mysql-git]」および「[Azure でギャラリーから WordPress Web サイトを作成する][wordpress-gallery]」をご覧ください。

Azure の管理ポータルを使用して PHP Web サイトを作成するには、次の操作を行います。

1. [Azure 管理ポータル] にログインします。
1. ページの下部にある **[新規]** をクリックし、 **[コンピューティング]**、**[Web サイト]**、**[簡易作成]** の順にクリックします。Web サイトの **URL**を指定し、適切な **リージョン** を選択します。最後に、**[Web サイトの作成]** をクリックします。

	![Select Quick Create web site](./media/web-sites-php-create-web-sites/select-quickcreate-website.png)

<h2><a name="XplatTools"></a>Mac および Linux 用 Azure コマンド ライン ツールを使用して PHP Web サイトを作成する</h2>

Mac および Linux 用 Azure コマンド ライン ツールを使用して PHP Web サイトを作成するには、次の操作を行います。

1. ここでの指示に従って、Azure コマンド ライン ツールをインストールします。[Mac および Linux 用 Azure コマンド ライン ツールをインストールする方法](/ja-jp/develop/php/how-to-guides/command-line-tools/#Download).

1. 次の手順に従って発行設定ファイルをダウンロードおよびインポートします:[発行の設定をダウンロードおよびインポートする方法](/ja-jp/develop/php/how-to-guides/command-line-tools/#Account).

1. コマンド プロンプトで、次のコマンドを実行します。

		azure site create MySiteName

新しく作成した Web サイトの URL は `http://MySiteName.azurewebsites.net` になります。  
 
 `azure site create` コマンドは、次のいずれかのオプションと組み合わせて実行できます。

* `--location [location name]`。このオプションを使用すると、Web サイトを作成するデータ センターの場所を指定できます (例:「米国西部」)。このオプションを省略した場合、場所を選択するよう促されます。
* `--hostname [custom host name]`。このオプションを使用すると、Web サイトのカスタム ホスト名を指定できます。
* `--git`。このオプションを指定すると、git を使用してローカル アプリケーション ディレクトリと Web サイトのデータ センターの両方に git リポジトリを作成して Web サイトに発行できます。ローカル フォルダーが既に git リポジトリである場合、このコマンドを実行すると新しいリモートが既存のリポジトリに追加され、Web サイトのデータ センター内のリポジトリが指定されます。

その他のオプションについては、「[Azure の Web サイトを作成および管理する方法](/ja-jp/develop/php/how-to-guides/command-line-tools/#WebSites).」をご覧ください。

<h2><a name="PowerShell"></a>Azure PowerShell コマンドレットを使用して PHP Web サイトを作成する</h2>

Azure PowerShell コマンドレットを使用して PHP Web サイトを作成するには、次の操作を行います。

1. ここでの指示に従って、Azure PowerShell コマンドレットをインストールします。[Azure PowerShell の概要](/ja-jp/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).

1. 次の手順に従って発行設定ファイルをダウンロードおよびインポートします:[方法:発行の設定をインポートする](/ja-jp/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings).

1. PowerShell コマンド プロンプトを開き、次のコマンドを実行します。

		New-AzureWebSite MySiteName

新しく作成した Web サイトの URL は  `http://MySiteName.azurewebsites.net` になります。  
 
 `New-AzureWebSite` コマンドは、次のいずれかのオプションと組み合わせて実行できます。

* `-Location [location name]`。このオプションを使用すると、Web サイトを作成するデータ センターの場所を指定できます (例:「米国西部」)。このオプションを省略した場合、場所を選択するよう促されます。
* `-Hostname [custom host name]`。このオプションを使用すると、Web サイトのカスタム ホスト名を指定できます。
* `-Git`。このオプションを指定すると、git を使用してローカル アプリケーション ディレクトリと Web サイトのデータ センターの両方に git リポジトリを作成して Web サイトに発行できます。ローカル フォルダーが既に git リポジトリである場合、このコマンドを実行すると新しいリモートが既存のリポジトリに追加され、Web サイトのデータ センター内のリポジトリが指定されます。

その他のオプションについては、次をご覧ください。[How to:Create and manage an Azure Web Site (Azure の Web サイトを作成および管理する方法)](/ja-jp/develop/php/how-to-guides/powershell-cmdlets/#WebSite).

<h2><a name="NextSteps"></a>次のステップ</h2>

Azure Web サイトで PHP Web サイトを作成したため、サイトの管理、構成、監視、規模設定、このサイトへのデプロイを行うことができます。詳細については、次のリンクをご覧ください。

* [How to configure Web Sites (Web サイトを構成する方法)](/ja-jp/manage/services/web-sites/how-to-configure-websites/)
* [Azure の Web サイトでの PHP の構成方法][configure-php]
* [Web サイトの管理方法](/ja-jp/manage/services/web-sites/how-to-manage-websites/)
* [Web サイトの監視方法](/ja-jp/manage/services/web-sites/how-to-monitor-websites/)
* [How to scale Web Sites (Web サイトの規模変更方法)](/ja-jp/manage/services/web-sites/how-to-scale-websites/)
* [Git を使用した発行](/ja-jp/develop/php/common-tasks/publishing-with-git/)

エンド ツー エンドのチュートリアルについては、「[PHP デベロッパー センター - チュートリアル](/ja-jp/develop/php/tutorials/)」をご覧ください。

[waws]: /ja-jp/manage/services/web-sites/
[Azure 管理ポータル]: http://manage.windowsazure.com/
[xplat-tools]: /ja-jp/develop/php/how-to-guides/command-line-tools/
[powershell-cmdlets]: /ja-jp/develop/php/how-to-guides/powershell-cmdlets/
[configure-php]: /ja-jp/develop/php/common-tasks/configure-php-web-site/
[website-mysql-git]: /ja-jp/develop/php/tutorials/website-w-mysql-and-git/
[wordpress-gallery]: /ja-jp/develop/php/tutorials/website-from-gallery/
[websites-pricing]: http://azure.microsoft.com/pricing/details/#header-1
[scale-websites]: /ja-jp/manage/services/web-sites/how-to-scale-websites/


<!--HONumber=42-->
[CO1]placeholder 65 should be delete during post edit.

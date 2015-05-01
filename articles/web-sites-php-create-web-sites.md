<properties 
	pageTitle="Azure App Service で PHP Web アプリを作成する" 
	description="Azure App Service で PHP Web アプリを作成する方法について説明します。" 
	documentationCenter="php" 
	services="app-service\web" 
	editor="mollybos" 
	manager="wpickett" 
	authors="tfitzmac"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tomfitz"/>

# Azure App Service で PHP Web アプリを作成する

## 概要
この記事では、[Azure ポータル]、[Mac と Linux 用 Azure コマンド ライン ツール][xplat-tools]、[Azure PowerShell コマンドレット][powershell-cmdlets] を使用して [Azure App Service] で PHP Web アプリを作成する方法を示します。

一般的に、PHP Web アプリを作成するのは、Azure App Service で *any* Web アプリを作成するのと変わりません。既定で、PHP はすべての Web アプリに対して有効になっていますPHPの構成に関する詳細 (または独自のカスタマイズされた PHP ランタイムの提供) については、「[Azure App Service Web アプリでの PHP の構成方法]」をご覧ください。

ここで説明する各オプションは、共有ホスティング環境を利用して無料で Web アプリを作成する方法を示していますが、CPU と帯域幅の使用については一定の制限があります。詳細については、「[アプリ サービス料金]」をご覧ください。アプリ サービスで Web アプリをアップグレードし、拡張する方法については、「[Azure App Service の Web アプリをスケーリングする]」をご覧ください。

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[アプリ サービスを試す](http://go.microsoft.com/fwlink/?LinkId=523751)」をご覧ください。そこでは、アプリ サービスで有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

<a name="portal"></a>
## Azure ポータルを使用して PHP Web アプリを作成する

Azure ポータルで、PHP Web アプリを作成する場合は、次の 3 つのオプションがあります。 

- **簡易作成** - [方法:Azure ポータルを使用した Web アプリの作成](web-sites-create-deploy.md#createawebsiteportal)に関するページをご覧ください
- **データベースで作成する** - 「[Azure App Service で PHP-MySQL Web アプリを作成して Git でデプロイする]」をご覧ください
- **Marketplace から** - 「[Azure App Service での WordPress Web アプリの作成]」をご覧ください

<a name="XplatTools"></a>
## Mac と Linux 用 Azure コマンド ライン ツールを使用して PHP Web アプリを作成する

Mac と Linux 用 Azure コマンド ライン ツールを使用して PHP Web アプリを作成するには、次の操作を行います。

1. ここでの指示に従って、Azure コマンド ライン ツールをインストールします。[Mac と Linux 用 Azure](xplat-cli.md#install)マンド ライン ツールをインストールする方法]に関するページ。

1. 次の手順に従って発行設定ファイルをダウンロードし、インポートします: [発行の設定をダウンロードやインポートする方法](xplat-cli.md#configure)に関するページ。

1. コマンド プロンプトで、次のコマンドを実行します。

		azure site create MyWebAppName

新しく作成した Web アプリの URL は  `http://MyWebAppName.azurewebsites.net` になります。  
 
 `azure site create` コマンドは、次のいずれかのオプションと組み合わせて実行できます。

* `--location [location name]`。このオプションを使用すると、Web アプリを作成するデータ センターの場所を指定できます (たとえば、"米国西部")。このオプションを省略した場合、場所を選択するよう促されます。
* `--hostname [custom host name]`。このオプションを使用すると、Web アプリのカスタム ホスト名を指定できます。
* `--git` 。このオプションを指定すると、git を使用してローカル アプリケーション ディレクトリと Web アプリのデータ センターの両方に git リポジトリを作成して Web アプリに発行できます。ローカル フォルダーが既に git リポジトリである場合、このコマンドを実行すると新しいリモートが既存のリポジトリに追加され、Web アプリのデータ センター内のリポジトリが指定されます。

<a name="PowerShell"></a>
## Azure PowerShell コマンドレットを使用して PHP Web アプリを作成する

Azure PowerShell コマンドレットを使用して PHP Web アプリを作成するには、次の操作を行います。

1. ここでの指示に従って、Azure PowerShell コマンドレットをインストールします。[Azure PowerShell の使用](/develop/php/how-to-guides/powershell-cmdlets/#GetStarted)に関するページ。

1. 次の手順に従って発行設定ファイルをダウンロードし、インポートします:[方法: 発行の設定をインポートする](/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings)に関するページ。

1. PowerShell コマンド プロンプトを開き、次のコマンドを実行します。

		New-AzureWebsite MyWebAppName

新しく作成した Web アプリの URL は `http://MyWebAppName.azurewebsites.net` になります。  
 
 `New-AzureWebsite` コマンドは、次のいずれかのオプションと組み合わせて実行できます。

* `-Location [location name]` です。このオプションを使用すると、Web アプリを作成するデータ センターの場所を指定できます (たとえば、"米国西部")。このオプションを省略した場合、場所を選択するよう促されます。
* `-Hostname [custom host name]`。このオプションを使用すると、Web アプリのカスタム ホスト名を指定できます。
* `-Git`。このオプションを指定すると、git を使用してローカル アプリケーション ディレクトリと Web アプリのデータ センターの両方に git リポジトリを作成して Web アプリに発行できます。ローカル フォルダーが既に git リポジトリである場合、このコマンドを実行すると新しいリモートが既存のリポジトリに追加され、Web アプリのデータ センター内のリポジトリが指定されます。

<a name="NextSteps"></a>
## 次のステップ

Azure App Service で PHP Web アプリを作成したので、アプリの管理、構成、監視、スケーリング、このアプリへの展開を行うことができます。詳細については、次のリンクをご覧ください。

* [Azure App Service での Web アプリの構成](web-sites-configure.md)
* [Azure App Service Web アプリでの PHP の構成方法]
* [Azure ポータルによる Web アプリの管理](web-sites-manage.md)
* [Azure App Service の Web アプリの監視](web-sites-monitor.md)
* [Azure App Service の Web アプリをスケーリングする]
* [Azure App Service での GIT による継続的なデプロイ](web-sites-publish-source-control.md)

エンド ツー エンドのチュートリアルについては、[PHP デベロッパー センターのチュートリアル](/develop/php/tutorials/)のページをご覧ください。

## 変更内容
* Web サイトからアプリ サービスへの変更ガイドについては、次のものをご覧ください。[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)
* 古いポータルから新しいポータルへの変更ガイドについては、次のものをご覧ください。[Azure ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure ポータル]: http://go.microsoft.com/fwlink/?LinkId=529715
[xplat-tools]: xplat-cli.md
[powershell-cmdlets]: powershell-install-configure.md
[Azure App Service Web アプリでの PHP の構成方法]: web-sites-php-configure.md
[Azure App Service で PHP-MySQL Web アプリを作成して Git でデプロイする]: web-sites-php-mysql-deploy-use-git.md
[Azure App Service での WordPress Web アプリの作成]: web-sites-php-web-site-gallery.md
[アプリ サービス料金]: /pricing/details/app-service/
[Azure App Service の Web アプリをスケーリングする]: web-sites-scale.md


<!--HONumber=52-->
<properties urlDisplayName="Specifying a Node.js Version" pageTitle="Node.js のバージョンの指定" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="nodejs" title="Specifying a Node.js version in an Azure application" authors="larryfr" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="na" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />





# Azure アプリケーションでの Node.js のバージョンの指定

Node.js アプリケーションをホストするときに、アプリケーションで特定のバージョンの Node.js を使用することが必要になる場合があります。Azure でホストされるアプリケーションについてこれを行うには、いくつかの方法があります。

##既定のバージョン

現在、Azure では Node.js Version 0.6.17、0.6.20、および 0.8.4 を提供しています。特に指定しない場合、0.6.20 が既定のバージョンとして使用されます。

<div class="dev-callout">
<strong>注</strong>
<p>Azure クラウド サービス (Web ロールまたは worker ロール) でアプリケーションをホストしており、初めてアプリケーションを展開した場合、開発環境にインストールされていた Node.js が Azure で使用できる既定のバージョンのいずれかと一致するときには、同じバージョンを使用することが試行されます。</p>
</div>


##package.json でバージョンを指定する

次の内容を **package.json** ファイルに追加することによって、使用する Node.js のバージョンを指定できます。

	"engines":{"node":version}

ここで、*version*は、使用する特定のバージョン番号です。次のように、より複雑なバージョンの条件を指定することもできます。

	"engines":{"node": "0.6.22 || 0.8.x"}

0.6.22 はホスティング環境で利用可能なバージョンには含まれていないため、0.8 シリーズで利用可能な最も高いバージョンである 0.8.4 が代わりに使用されます。

##PowerShell でクラウド サービスのバージョンを指定する

クラウド サービスでアプリケーションをホストしている場合で、Azure PowerShell を使用してアプリケーションをデプロイしている場合は、PowerShell の **Set-AzureServiceProjectRole** コマンドレットを使用して既定の Node.js のバージョンをオーバーライドできます。次に例を示します。

	Set-AzureServiceProjectRole WebRole1 node 0.8.4

また、**Get-AzureServiceProjectRoleRuntime** を使用して、クラウド サービスとしてホストされるアプリケーションで利用可能な Node.js のバージョンの一覧を取得できます。

##Azure Websites でカスタム バージョンを使用する

Azure には Node.js の既定のバージョンが複数用意されていますが、既定以外のバージョンを使用することもできます。アプリケーションが Azure Website としてホストされている場合、**iisnode.yml** ファイルを使用してこれを実現できます。以下の手順では、Azure Website で Node.Js のカスタム バージョンを使用するプロセスを説明します。

1. 新しいディレクトリを作成し、そのディレクトリ内に **server.js** ファイルを作成します。**server.js** ファイルの内容は次のようになっている必要があります。

		var http = require('http');
		http.createServer(function(req,res) {
		  res.writeHead(200, {'Content-Type': 'text/html'});
		  res.end('Hello from Azure running node version: ' + process.version + '</br>');
		}).listen(process.env.PORT || 3000);

	これにより、Web サイトを閲覧するときに、使用されている Node.js のバージョンが表示されます。

2. 新しい Web サイトを作成し、サイトの名前をメモしておきます。たとえば、次の例では [Azure コマンド ライン ツール]を使用して、**mywebsite** という名前の新しい Azure Website を作成し、この Web サイトの Git リポジトリを有効にします。

		azure site create mywebsite --git

3. **server.js** ファイルが格納されているディレクトリの子として **bin** という名前の新しいディレクトリを作成します。

4. アプリケーションで使用する特定のバージョンの **node.exe** (Windows 版) をダウンロードします。たとえば、次の例では **curl** を使用して Version 0.8.1 をダウンロードします。

		curl -O http://nodejs.org/dist/v0.8.1/node.exe

	**node.exe** ファイルを前の手順で作成した **bin** フォルダーに保存します。

5. **iisnode.yml** ファイルを **server.js** ファイルと同じディレクトリに作成し、次の内容を **iisnode.yml** ファイルに追加します。

		nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

	このパスは、アプリケーションを Azure Website に発行した際に、プロジェクト内の **node.exe** ファイルが配置される場所です。

6. アプリケーションを発行します。たとえば、先ほど --git パラメーターを指定して新しい Web サイトを作成したので、次のコマンドによって、自分のローカル Git リポジトリにアプリケーション ファイルが追加された後、Web サイトのリポジトリにプッシュされます。

		git add .
		git commit -m "testing node v0.8.1"
		git push azure master

	アプリケーションが発行された後、ブラウザーで Web サイトを開きます。"Hello from Windows Azure running node version: v0.8.1" というメッセージが表示されます。

##次のステップ

アプリケーションで使用される Node.js のバージョンを指定する方法が理解できたら、[モジュールの使用方法]、[Node.js Web サイトを構築、デプロイする方法]、[Mac および Linux 用 Azure コマンド ライン ツールの使用方法]に関する各トピックを参照してください。

[Mac および Linux 用 Azure コマンド ライン ツールの使用方法]: /ja-jp/documentation/articles/xplat-cli/
[Azure コマンドライン ツール]: /ja-jp/documentation/articles/xplat-cli/
[モジュールの使用]: /ja-jp/documentation/articles/nodejs-use-node-modules-azure-apps/
[Node.js Web サイトの構築と展開]: /ja-jp/documentation/articles/web-sites-nodejs-develop-deploy-mac/

<properties urlDisplayName="Working with Node.js Modules" pageTitle="Node.js モジュールの操作" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="nodejs" title="Using Node.js Modules with Azure applications" authors="larryfr" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="na" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />





# Azure アプリケーションでの Node.js モジュールの使用

このドキュメントは、Azure でホストされているアプリケーションでの Node.js モジュールの使用に関するガイダンスを示します。また、Azure でアプリケーションによって特定のバージョンのモジュールとネイティブ モジュールが確実に使用されるようにするためのガイダンスも紹介します。

Node.js モジュールである **package.json** および **npm-shrinkwrap.json** ファイルの使い方について既によくわかっている方は、この記事で概要を簡単にご確認ください。

* Azure Websites では **package.json** および **npm-shrinkwrap.json** ファイルが認識され、これらのファイルのエントリに基づいてモジュールをインストールできます。
* Azure Cloud Services は、すべてのモジュールが開発環境にインストールされ、**node\_modules** ディレクトリがデプロイ パッケージの一部として含められることを想定します。

<div class="dev-callout">
<strong>注</strong>
<p>VM でのデプロイは仮想マシンでホストされているオペレーティング システムに依存するので、この記事では Azure の仮想マシンについては説明しません。</p>
</div>

<div class="dev-callout">
<strong>注</strong>
<p><b>package.json</b> または <b>npm-shrinkwrap.json</b> ファイルを使用したモジュール インストールのサポートを有効にできますが、このサポートを有効にするには、クラウド サービスのプロジェクトで使用される既定のスクリプトをカスタマイズする必要があります。これを行う方法の例については、「<a href="http://nodeblog.azurewebsites.net/startup-task-to-run-npm-in-azure">npm インストールを実行してノード モジュールのデプロイを回避するための Windows Azure スタートアップ タスク</a></p>」を参照してください。
</div>

##Node.js モジュール

モジュールは、アプリケーションに対して特定の機能を提供する読み込み可能な JavaScript パッケージです。通常は **npm** コマンド ライン ツールを使用してインストールされますが、http モジュールのようにコア Node.js パッケージの一部として提供されるものもあります。

インストールされたモジュールは、アプリケーション ディレクトリ構造のルートにある **node\_modules** ディレクトリに保存されます。この **node\_modules** ディレクトリの各モジュールに独自の **node\_modules** ディレクトリが保持されており、そこには、そのモジュールが依存するすべてのモジュールが含まれます。これは、依存関係チェーンの端から端まですべてのモジュールに対して繰り返されます。これにより、インストールされている各モジュールが、自身が依存するモジュールに対して独自のバージョン要件を持つことができます。ただし、この結果、ディレクトリ構造が非常に大きくなる可能性があります。

**node\_modules** ディレクトリをアプリケーションの一部としてデプロイすると、デプロイのサイズは、**package.json** または **npm-shrinkwrap.json** ファイルを使用した場合に比べると大きくなりますが、運用環境で使用されるモジュールのバージョンが、開発環境で使用されるものと必ず同じになります。

###ネイティブ モジュール

ほとんどのモジュールがシンプルな JavaScript テキスト ファイルですが、中にはプラットフォーム固有のバイナリ イメージのモジュールもあります。こうしたモジュールは、通常、インストール時に Python および node-gyp を使用してコンパイルされます。Azure Websites では、**package.json** または **npm-shrinkwrap.json** ファイルで指定されたモジュールのインストール方法がネイティブで認識されますが、Python または node-gyp が用意されていないので、ネイティブ モジュールを構築できません。これは Azure Websites の制限事項の 1 つです。

Azure Cloud Services は、アプリケーションの一部としてデプロイされている **node\_modules** フォルダーに依存するので、インストールされたモジュールの一部として含まれるネイティブ モジュールはすべて、Windows 開発システムでインストールおよびコンパイルされている限りクラウド サービスで動作します。 

Azure Websites では、ネイティブ モジュールがサポートされていません。JSDOM、MongoDB など、オプションでネイティブの依存関係が含まれている一部のモジュールについては、Azure Websites でホストされているアプリケーションで動作します。

###package.json ファイルの使用

**package.json** ファイルは、ホスティング プラットフォームが依存関係をインストールできるように、アプリケーションに必要な最上位レベルの依存関係を指定する 1 つの手段です。この場合、**node\_packages** フォルダーをデプロイの一部として含める必要はありません。アプリケーションのデプロイ後、**npm install** コマンドによって **package.json** ファイルが解析され、表示されているすべての依存関係がインストールされます。

開発中、モジュールをインストールするときに **--save**、**--save-dev**、または **--save-optional** パラメーターを使用すると、モジュールのエントリを **package.json** ファイルに自動的に追加することができます。詳細については、「[npm-install](https://npmjs.org/doc/install.html)」を参照してください。

**package.json** ファイルの潜在的な問題の 1 つが、このファイルでは最上位レベルの依存関係のバージョンしか指定されないという点です。インストールされているモジュールはそれぞれ、自身が依存しているモジュールのバージョンを指定することがあれば、指定しないこともあります。したがって、依存関係チェーンが、開発時に使用されたもの以外になる可能性があります。 

> [WACOM.NOTE]
> Azure Websites へのデプロイ中、<b>package.json</b> ファイルがネイティブ モジュールを参照すると、Git を使用してアプリケーションを発行するときに次のようなエラーが表示されます。

>		npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>		npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1	


###npm-shrinkwrap.json ファイルの使用

**npm-shrinkwrap.json** ファイルは、**package.json** ファイルのモジュール バージョン管理制限への対処を試みるものです。**package.json** ファイルに含まれるのは最上位レベルのモジュールのバージョンのみですが、**npm-shrinkwrap.json** ファイルには、完全なモジュール依存関係チェーンのバージョン要件が含まれます。

アプリケーションの運用の準備ができたら、バージョン要件を整理して確定し、**npm shrinkwrap** コマンドを使用して **npm-shrinkwrap.json** ファイルを作成できます。これにより **node\_modules** フォルダーに現在インストールされているバージョンが使用され、これらのバージョンが **npm-shrinkwrap.json** ファイルに記録されます。ホスティング環境へのアプリケーションのデプロイ後、**npm install** コマンドによって **npm-shrinkwrap.json** ファイルが解析され、表示されているすべての依存関係がインストールされます。詳細については、「[npm-install](https://npmjs.org/doc/install.html)」を参照してください。

> [WACOM.NOTE]
>Azure Websites へのデプロイ中、<b>npm-shrinkwrap.json</b> ファイルがネイティブ モジュールを参照すると、Git を使用してアプリケーションを発行するときに次のようなエラーが表示されます。
		
>		npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>		npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


##次のステップ

Azure で Node.js モジュールを使用する方法が理解できたら、[Node.js バージョンを指定する方法]、[Node.js Web サイトを構築、デプロイする方法]、[Mac および Linux 用 Azure コマンド ライン ツールを使用する方法]に関する各トピックを参照してください。

[Node.js のバージョンの指定]: /ja-jp/documentation/articles/nodejs-specify-node-version-azure-apps/
[Mac および Linux 用 Azure コマンド ライン ツールの使用方法]: /ja-jp/documentation/articles/xplat-cli/
[Node.js Web サイトの構築と展開]: /ja-jp/documentation/articles/web-sites-nodejs-develop-deploy-mac/
[MongoDB (MongoLab) のストレージを使用する Node.js Web アプリケーション]: /ja-jp/documentation/articles/store-mongolab-web-sites-nodejs-store-data-mongodb/
[Git を使用した発行]: /ja-jp/documentation/articles/web-sites-publish-source-control/
[Node.js アプリケーションの構築と Azure クラウド サービスへのデプロイ]: /ja-jp/documentation/articles/cloud-services-nodejs-develop-deploy-app/



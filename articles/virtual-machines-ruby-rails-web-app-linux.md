<properties 
	pageTitle="Linux VM を使用した Azure での Ruby on Rails Web アプリケーション" 
	description="Linux 仮想マシンを使用して、Azure で Ruby on Rails ベースの Web サイトをホストします。" 
	services="virtual-machines" 
	documentationCenter="ruby" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>





#Azure VM での Ruby on Rails Web アプリケーション

このチュートリアルでは、Linux 仮想マシンを使用して、Azure で Ruby on Rails ベースの Web サイトをホストする方法について説明します。このチュートリアルは、Azure を使用した経験がない読者を対象に作成されています。このチュートリアルを完了すると、クラウドで動作する Ruby on Rails ベースのアプリケーションが完成します。

学習内容:

* 開発環境を設定する

* Ruby on Rails をホストするように Azure の仮想マシンを設定する

* 新しい Rails アプリケーションを作成する

* scp を使用してファイルを仮想マシンにコピーする 

完成したアプリケーションのスクリーンショットは次のようになります。

![a browser displaying Listing Posts][blog-rails-cloud]

##この記事の内容

* [開発環境を設定する](#setup)

* [Rails アプリケーションを作成する](#create)

* [アプリケーションをテストする](#test)

* [Azure の仮想マシンを作成する](#createvm)

* [アプリケーションを VM にコピーする](#copy)

* [gem をインストールしてアプリケーションを起動する](#start)

* [次のステップ](#next)

##<a id="setup"></a>開発環境を設定する

1. 開発環境に Ruby をインストールします。この手順は、使用しているオペレーティング システムによって異なる場合があります。

	* **Apple OS X** - OS X 用には、いくつかの Ruby ディストリビューションがあります。このチュートリアルの検証には、OS X で [Homebrew](http://brew.sh/) を使用して **rbenv** および **ruby-build** をインストールしました。インストールに関する情報は、[https://github.com/sstephenson/rbenv/](https://github.com/sstephenson/rbenv/) で確認できます。

	* **Linux** - ディストリビューションのパッケージ管理システムを使用します。このチュートリアルは、Ubuntu 12.10 で ruby1.9.1 および ruby1.9.1-dev パッケージを使用して検証されました。

	* **Windows** - Windows 用には、いくつかの Ruby ディストリビューションがあります。このチュートリアルは、[RailsInstaller](http://railsinstaller.org/) 1.9.3-p392 を使用して検証されました。

2. 新しいコマンド ラインまたはターミナル セッションを開き、次のコマンドを入力して Ruby on Rails をインストールします。

		gem install rails --no-rdoc --no-ri

	> [AZURE.NOTE] オペレーティング システムによっては、このコマンドには管理者特権または root 権限が必要となる場合があります。このコマンドの実行中にエラーが発生した場合は、次のように  'sudo' を使用してください。
	>
	>````` 
	sudo gem install rails
	`````

	> [AZURE.NOTE] このチュートリアルでは、Rails gem Version 3.2.12 を使用しました。

3. JavaScript インタープリターもインストールする必要があります。これは、Rails アプリケーションで使われる CoffeeScript アセットをコンパイルするために Rails によって使用されます。サポートされているインタープリターの一覧は、[https://github.com/sstephenson/execjs#readme](https://github.com/sstephenson/execjs#readme) で確認できます。
	
	このチュートリアルの検証には、[Node.js](http://nodejs.org/) を使用しました。このインタープリターは OS X、Linux、Windows の各オペレーティング システムで利用できるためです。

##<a id="create"></a>Rails アプリケーションを作成する

1. コマンド ラインまたはターミナル セッションから、次のコマンドを使用して、「blog_app」という名前の新しい Rails アプリケーションを作成します。

		rails new blog_app

	このコマンドは、**blog_app** という新しいディレクトリを作成し、Rails アプリケーションに必要なファイルとサブディレクトリを格納します。

	> [AZURE.NOTE] このコマンドが完了するまでには、しばらく時間がかかることがあります。このコマンドによって既定のアプリケーションに必要な gem のサイレント インストールが実行されますが、その間にハングしたように見える場合があります。

2. ディレクトリを **blog_app** に変更し、次のコマンドを使用して、基本的なブログ スキャフォールディングを作成します。

		rails generate scaffold Post name:string title:string content:text

	これにより、コントローラー、ビュー、モデル、およびブログへの投稿を保持するためのデータベースの移行が作成されます。各投稿には、作者名、記事のタイトル、およびテキスト コンテンツが含められます。

3. 次のコマンドを実行して、ブログへの投稿を保存するデータベースを作成します。

		rake db:migrate

	この場合、Rails の既定のデータベース プロバイダーである [SQLite3 データベース][sqlite3]が使用されます。運用アプリケーションでは別のデータベースを使用する方が適している場合もありますが、このチュートリアルの目的では SQLite で十分です。

##<a id="test"></a>アプリケーションをテストする

次の手順を実行して、開発環境で Rails サーバーを開始します。

1. コマンド ラインまたはターミナル セッションから、次のコマンドを使用して rails サーバーを開始します。

		rails s

	次のような出力が表示されます。Web サーバーがリッスンしているポートに注目してください。下の例では、ポート 3000 をリッスンしています。

		=> Booting WEBrick
		=> Rails 3.2.12 application starting in development on http://0.0.0.0:3000
		=> Call with -d to detach
		=> Ctrl-C to shutdown server
		[2013-03-12 19:11:31] INFO  WEBrick 1.3.1
		[2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
		[2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2. ブラウザーを開き、http://localhost:3000/ に移動します。次のようなページが表示されます。

	![default rails page][default-rails]

	これは静的な開始ページです。スキャフォールディング コマンドによって生成されたフォームを表示するには、http://localhost:3000/posts に移動します。次のようなページが表示されます。

	![a page listing posts][blog-rails]

	サーバー プロセスを停止するには、コマンド ラインで Ctrl + C キーを押します。

##<a id="createvm"></a>Azure の仮想マシンを作成する

[ここ][vm-instructions]に記載されている指示に従って、Linux をホストする Azure の仮想マシンを作成します。

> [AZURE.NOTE] このチュートリアルの手順は、Ubuntu 12.10 をホストする Azure 仮想マシンで実行しています。他の Linux ディストリビューションを使用する場合は、同じタスクを完了するために別の手順が必要になることがあります。

> [AZURE.IMPORTANT] ここで作成する必要があるのは、仮想マシン**のみ**です。SSH を使用して仮想マシンに接続する方法を確認したら戻ってください。

Azure の仮想マシンを作成したら、次の手順を実行して、仮想マシンに Ruby および Rails をインストールします。

1. コマンド ラインまたはターミナル セッションから、次のコマンドを使用して SSH で仮想マシンに接続します。

		ssh username@vmdns -p port

	各パラメーターは、VM の作成時に指定したユーザー名、VM の DNS アドレス、SSH エンドポイントに置き換えてください。次に例を示します。

		ssh railsdev@railsvm.cloudapp.net -p 61830

	> [AZURE.NOTE] 開発環境として Windows を使用している場合、SSH 機能には **PuTTY** などのユーティリティを使用できます。PuTTY は、[PuTTY のダウンロード ページ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)から入手できます。

2. SSH セッションから、次のコマンドを使用して VM に Ruby をインストールします。

		sudo apt-get update -y
		sudo apt-get upgrade -y
		sudo apt-get install ruby1.9.1 ruby1.9.1-dev build-essential libsqlite3-dev nodejs -y

	インストールが完了したら、次のコマンドを使用して Ruby が正常にインストールされたことを確認します。

		ruby -v

	このコマンドは、仮想マシンにインストールされている Ruby のバージョンを返します。これは 1.9.1 ではない可能性もあります。たとえば、**ruby 1.9.3p194 (2012-04-20 revision 35410) [x86_64-linux]** のようになります。

2. 次のコマンドを使用して Bundler をインストールします。

		sudo gem install bundler --no-rdoc --no-ri

	Bundler は、いったんサーバーにコピーされると、Rails アプリケーションに必要な gem をインストールするために使用されます。

##<a id="copy"></a>アプリケーションを VM にコピーする

開発環境から、新しいコマンド ラインまたはターミナル セッションを開き、**scp** コマンドを使用して、**blog_app** ディレクトリを仮想マシンにコピーします。このコマンドの形式は次のとおりです。

	scp -r -P 54822 -C directory-to-copy user@vmdns:

次に例を示します。

	scp -r -P 54822 -C ~/blog_app railsdev@railsvm.cloudapp.net:

> [AZURE.NOTE] 開発環境として Windows を使用している場合、scp 機能には **pscp** などのユーティリティを使用できます。pscp は、[PuTTY のダウンロード ページ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)から入手できます。

このコマンドで使用されるパラメーターの意味は次のとおりです。

* **-r**:指定したディレクトリとそのサブディレクトリの内容を再帰的にコピーします

* **-P**:指定したポートを SSH 通信に使用します

* **-C**:圧縮を有効にします

* **directory-to-copy**:コピーするローカル ディレクトリ

* **user@vmdns**:ファイルのコピー先となるコンピューターのアドレスと、ログインに使用するユーザー アカウント

コピー操作が完了すると、**blog_app** ディレクトリがユーザーのホーム ディレクトリに配置されます。仮想マシンへの SSH セッションで次のコマンドを使用して、コピーされたファイルを表示します。

	cd ~/blog_app
	ls

返されるファイルの一覧は、開発環境の **blog_app** ディレクトリに含まれているファイルに一致します。

##<a id="start"></a>gem をインストールして Rails を開始する

1. 仮想マシンで、ディレクトリを **blog_app** に変更し、次のコマンドを使用して **Gemfile** に指定されている gem をインストールします。

		sudo bundle install

2. 次のコマンドを使用してデータベースを作成します。

		rake db:migrate

3. 次のコマンドを使用してサーバーを開始します。
	
		rails s

	次のような出力が表示されます。Web サーバーがリッスンしているポートに注目してください。下の例では、ポート 3000 をリッスンしています。

		=> Booting WEBrick
		=> Rails 3.2.12 application starting in development on http://0.0.0.0:3000
		=> Call with -d to detach
		=> Ctrl-C to shutdown server
		[2013-03-12 19:11:31] INFO  WEBrick 1.3.1
		[2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
		[2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2. ブラウザーで [Azure 管理ポータル][management-portal]に移動し、適切な仮想マシンを選択します。

	![virtual machine list][vmlist]

3. ページの上部にある **[エンドポイント]** を選択し、ページの下部にある **[エンドポイントの追加]** をクリックします。

	![endpoints page][endpoints]

4. **[エンドポイントの追加]** ダイアログで、左下にある矢印をクリックして次のページへ進み、フォームに次の情報を入力します。

	* **名前**:HTTP

	* **プロトコル**:TCP

	* **パブリック ポート**:80

	* **プライベート ポート**:&lt;前の手順 3. で確認したポート情報&gt;

	これにより、プライベート ポート 3000 にトラフィックをルーティングするパブリック ポート 80 が作成されます。ルーティング先のプライベート ポートは、Rails がリッスンしています。

	![new endpoint dialog][new-endpoint]

5. チェックマークをクリックして、エンドポイントを保存します。

6. **「更新が進行中です」** というメッセージが表示されます。このメッセージが消えると、エンドポイントがアクティブになります。この時点で仮想マシンの DNS 名に移動すると、アプリケーションをテストできます。Web サイトは次のようになります。

	![default rails page][default-rails-cloud]

	URL に **/posts** を追加すると、スキャフォールディング コマンドで生成されたページが表示されます。

	![posts page][blog-rails-cloud]

##<a id="next"></a>次のステップ

この記事では、基本的なフォーム ベースの Rails アプリケーションを作成し、Azure の仮想マシンに発行する方法について説明しました。ほとんどの操作は手動で実行しましたが、通常、運用環境では自動化が求められます。また、運用環境では、Apache や NginX などの別のサーバー プロセスと組み合わせて Rails アプリケーションをホストすることがほとんどです。これらのサーバーは、複数の Rails アプリケーション インスタンスへの要求のルーティングを処理すると共に、静的リソースを提供します。

Rails アプリケーションのデプロイの自動化や、Unicorn Web サーバーと NginX の使用方法については、[Azure VM での Unicorn、NginX、Capistrano][unicorn-nginx-capistrano] に関するページを参照してください。

Ruby on Rails の詳細について学習するには、[Ruby on Rails のガイド][rails-guides]を参照してください。

Azure SDK for Ruby を使用して Ruby アプリケーションから Azure サービスにアクセスする方法については、次のリンクを参照してください。

* [BLOB を使用して非構造化データを保存する][blobs]

* [テーブルを使用してキー/値のペアを保存する][tables]

* [コンテンツ配信ネットワークを使用して高帯域幅コンテンツを配信する][cdn-howto]



<!-- WA.com links -->
[blobs]: /ja-jp/documentation/articles/storage-ruby-how-to-use-blob-storage

[cdn-howto]: /ja-jp/develop/ruby/app-services/

[management-portal]: https://manage.windowsazure.com/

[tables]: /ja-jp/develop/ruby/how-to-guides/table-service/

[unicorn-nginx-capistrano]: /ja-jp/documentation/articles/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/

[vm-instructions]: /ja-jp/documentation/articles/virtual-machines-linux-tutorial


<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/

[sqlite3]: http://www.sqlite.org/

<!-- Images -->
[blog-rails]: ./media/virtual-machines-ruby-rails-web-app-linux/blograilslocal.png

[blog-rails-cloud]: ./media/virtual-machines-ruby-rails-web-app-linux/blograilscloud.png 

[default-rails]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailslocal.png

[default-rails-cloud]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailscloud.png

[vmlist]: ./media/virtual-machines-ruby-rails-web-app-linux/vmlist.png

[endpoints]: ./media/virtual-machines-ruby-rails-web-app-linux/endpoints.png

[new-endpoint]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint.png


<!--HONumber=45--> 

<properties linkid="dev-ruby-web-app-with-linux-vm-capistrano" urlDisplayName="Ruby on Rails Azure VM Capistrano" pageTitle="Capistrano を使用して Ruby on Rails Web アプリケーションを Azure 仮想マシンに展開 - チュートリアル" metaKeywords="ruby on rails、ruby on rails azure、rails azure、rails vm、capistrano azure vm、capistrano azure rails、unicorn azure vm、unicorn azure rails、unicorn nginx capistrano、unicorn nginx capistrano azure、nginx azure" description="Capistrano、Unicorn、Nginxを使用して、Azure の仮想マシンに Ruby on Rails アプリケーションを展開する方法について学習します。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Capistrano を使用して Azure VM に Ruby on Rails Web アプリケーションを展開する" authors="" />



#Capistrano を使用して Azure VM に Ruby on Rails Web アプリケーションを展開する

このチュートリアルでは、[Capistrano](https://github.com/capistrano/capistrano/) を使用して Azure の仮想マシンに Ruby on Rails ベースの Web サイトを展開する方法について説明します。また、[Nginx](http://nginx.org/) および [Unicorn](https://github.com/blog/517-unicorn) を使用して、仮想マシンでアプリケーションをホストする方法についても説明します。

このチュートリアルは、Azure を使用した経験がない読者を対象に作成されています。このチュートリアルを完了すると、クラウドで動作する Ruby on Rails ベースのアプリケーションが完成します。

学習内容:

* 開発環境を設定する

* Ruby および Ruby on Rails をインストールする

* Nginx および Unicorn をインストールして構成する

* 新しい Rails アプリケーションを作成する

* Capistrano を使用して Azure の仮想マシンに Rails アプリケーションを展開する

完成したアプリケーションのスクリーンショットは次のようになります。

![ブラウザーに表示された [Listing Posts]][blog-rails-cloud]

<div class="dev-callout">
<strong>注</strong>
<p>このチュートリアルで使用するアプリケーションには、ネイティブのバイナリ コンポーネントが含まれています。このため、開発環境が Linux ベースでない場合は、開発用コンピューターで生成された Gemfile.lock に Linux と互換性のないバージョンの gem のエントリが含まれる可能性があり、問題が発生することがあります。</p>
<p>Windows 開発環境を使用する場合向けには、対象とするデプロイ環境との違いが特に大きいため、固有の手順を記載しています。ただし、この記事で説明されていないエラーがデプロイ中やデプロイ後に発生した場合は、この記事の手順を Linux ベースの開発環境から再試行することをお勧めします。</p>

</div>

##この記事の内容

* [開発環境を設定する](#setup)

* [Rails アプリケーションを作成する](#create)

* [アプリケーションをテストする](#test)

* [ソース リポジトリを作成する](#repository)

* [Azure の仮想マシンを作成する](#createvm)

* [Nginx をテストする](#nginx)

* [デプロイの準備をする](#capify)

* [展開](#deploy)

* [次のステップ](#next)

##<a id="setup"></a>開発環境を設定する

1. 開発環境に Ruby をインストールします。この手順は、使用しているオペレーティング システムによって異なる場合があります。

	***Apple OS X** - OS X 用には、いくつかの Ruby ディストリビューションがあります。このチュートリアルの検証には、OS X で [Homebrew](http://brew.sh/) を使用して **rbenv** と **ruby-build** をインストールしました。インストールに関する情報は、[https://github.com/sstephenson/rbenv/](https://github.com/sstephenson/rbenv/) で確認できます。

	***Linux** - ディストリビューションのパッケージ管理システムを使用します。このチュートリアルは、Ubuntu 12.10 で ruby1.9.1 および ruby1.9.1-dev パッケージを使用して検証されました。

	* **Windows** - Windows 用には、いくつかの Ruby ディストリビューションがあります。このチュートリアルは、[RailsInstaller](http://railsinstaller.org/) 1.9.3-p392 を使用して検証されました。

2. 新しいコマンド ラインまたはターミナル セッションを開き、次のコマンドを入力して Ruby on Rails をインストールします。

		gem install rails --no-rdoc --no-ri

	<div class="dev-callout">
	<strong>注</strong>
	<p>オペレーティング システムによっては、このコマンドには管理者特権または root 権限が必要となる場合があります。このコマンドの実行中にエラーが発生した場合は、次のように "sudo" を使用してください。</p>
	<pre class="prettyprint">sudo gem install rails</pre>
	</div>

	<div class="dev-callout">
	<strong>メモ</strong>
	<p>このチュートリアルでは、Rails gem Version 3.2.12 を使用しました。</p>
	</div>

3. JavaScript インタープリターもインストールする必要があります。これは、Rails アプリケーションで使われる CoffeeScript アセットをコンパイルするために Rails によって使用されます。サポートされているインタープリターの一覧は、[https://github.com/sstephenson/execjs#readme](https://github.com/sstephenson/execjs#readme) で確認できます。
	
	[Node.js](http://nodejs.org/) をこのチュートリアルの検証中に使用しました。このインタープリターは OS X、Linux、Windows の各オペレーティング システムで利用できるためです。

##<a id="create"></a>Rails アプリケーションを作成する

1. コマンド ラインまたはターミナル セッションから、次のコマンドを使用して、"blog_app" という名前の新しい Rails アプリケーションを作成します。

		rails new blog_app

	このコマンドは、**blog_app** という新しいディレクトリを作成し、Rails アプリケーションに必要なファイルとサブディレクトリをそこに格納します。

	<div class="dev-callout">
	<strong>注</strong>
	<p>このコマンドが完了するまでには、しばらく時間がかかることがあります。このコマンドによって既定のアプリケーションに必要な gem のサイレント インストールが実行されますが、その間にハングしたように見える場合があります。</p>
	</div>

2. ディレクトリを **blog_app** に変更し、次のコマンドを使用して、基本的なブログ スキャフォールディングを作成します。

		rails generate scaffold Post name:string title:string content:text

	これにより、コントローラー、ビュー、モデル、およびブログへの投稿を保持するためのデータベースの移行が作成されます。各投稿には、作者名、記事のタイトル、およびテキスト コンテンツが含められます。

3. 次のコマンドを実行して、ブログへの投稿を保存するデータベースを作成します。

		rake db:migrate

	この場合、Rails の既定のデータベース プロバイダーである [SQLite3 データベース][sqlite3]が使用されます。運用アプリケーションでは別のデータベースを使用する方が適している場合もありますが、このチュートリアルの目的では SQLite で十分です。

##<a id="test"></a>アプリケーションをテストする

次の手順を実行して、開発環境で Rails サーバーを開始します。

1. 別のディレクトリにいる場合は、**blog_app** ディレクトリに変更します。次のコマンドを使用して rails サーバーを開始します。

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

	![既定の rails ページ][default-rails]

	これは静的な開始ページです。スキャフォールディング コマンドによって生成されたフォームを表示するには、http://localhost:3000/posts に移動します。次のようなページが表示されます。

	![投稿の一覧のページ][blog-rails]

	サーバー プロセスを停止するには、コマンド ラインで Ctrl + C キーを押します。

##<a id="repository"></a>ソース リポジトリを作成する

このチュートリアルでは、バージョン管理およびコードの集中的な保管場所として [Git](http://git-scm.com/) と [GitHub](https://github.com/) を使用します。

1. 	[GitHub](https://github.com/) で新しいリポジトリを作成します。GitHub アカウントを持っていない場合は、無料のアカウントにサインアップできます。このチュートリアルの手順では、リポジトリ名を **blog_app** と想定します。

	<div class="dev-callout">
	<strong>メモ</strong>
	<p>このドキュメントの後の手順で作成するスクリプトには、使用している仮想マシンのアドレスと、SSH 経由でのアプリケーションの展開に使用したユーザー名を含めることになります。このため、可能であればプライベートな GitHub リポジトリを使用することをお勧めします。</p>
	</div>

2. 	コマンド プロンプトから、ディレクトリを **blog_app** に変更し、次のコマンドを実行して、最初のバージョンのアプリケーションを GitHub リポジトリにアップロードします。

		git init
		git add .
		git commit -m "initial commit on azure"
		git remote add origin https://github.com/YOUR_GITHUB_ACCOUNT/blog-azure.git
		git push -u origin master


##<a id="createvm"></a>Azure の仮想マシンを作成する

[ここ][vm-instructions]に記載されている指示に従って、Linux をホストする Azure の仮想マシンを作成します。

<div class="dev-callout">
<strong>メモ</strong>
<p>このチュートリアルの手順は、Ubuntu 12.10. をホストする Azure の仮想マシンで実行されました。他の Linux ディストリビューションを使用する場合は、同じタスクを完了するために別の手順が必要になることがあります。</p>
</div>

<div class="dev-callout">
<strong>注</strong>
<p>ここで作成する必要があるのは、仮想マシンだけ<strong></strong>です。SSH を使用して仮想マシンに接続する方法を確認したら戻ってください。</p>
</div>

SSH を使用して Azure の仮想マシンを作成したら、次の手順を実行して、仮想マシンに Ruby および Rails をインストールします。

1. SSH を使用して仮想マシンに接続し、次のコマンドを使用して、既存のパッケージの更新と Ruby 環境のインストールを行います。

		sudo apt-get -y update
		sudo apt-get -y upgrade
		sudo apt-get -y install ruby1.9.1 ruby1.9.l-dev build-essential libsqlite3-dev nodejs curl git-core nginx

	インストールが完了したら、次のコマンドを使用して Ruby が正常にインストールされたことを確認します。

		ruby -v

	このコマンドは、仮想マシンにインストールされている Ruby のバージョンを返します。これは 1.9.1 ではない可能性もあります。たとえば、**ruby 1.9.3p194 (2012-04-20 revision 35410) [x86_64-linux]** のようになります。

2. 次のコマンドを使用して Bundler をインストールします。

		sudo gem install bundler

	Bundler は、いったんサーバーにコピーされると、Rails アプリケーションに必要な gem をインストールするために使用されます。

##<a id="nginx"></a>ポート 80 を開いて Nginx をテストする

Nginx には、仮想マシンが Web トラフィックを受け入れているかどうかを確認するために使用できる既定の Web サイトが用意されています。次の手順を実行して、ポート 80 経由のトラフィックを有効にし、既定の Nginx Web サイトをテストします。

2. 	VM への SSH セッションから、Nginx サービスを開始します。

		sudo service nginx start

	これにより Nginx サービスが開始され、ポート 80 で受信トラフィックをリッスンします。

2. ブラウザーで [Azure 管理ポータル][management-portal]に移動し、適切な仮想マシンを選択します。

	![仮想マシンの一覧][vmlist]

3. ページの上部にある **[エンドポイント]** を選択し、ページの下部にある **[エンドポイントの追加]** をクリックします。

	![エンドポイント ページ][endpoints]

4. **[エンドポイントの追加]** ダイアログで、左下にある矢印をクリックして次のページへ進み、フォームに次の情報を入力します。

	* **NAME**: HTTP

	* **PROTOCOL**: TCP

	* **PUBLIC PORT**: 80

	* **PRIVATE PORT**: 80

	これにより、プライベート ポート 80 にトラフィックをルーティングするパブリック ポート 80 が作成されます。ルーティング先のプライベート ポートは、Nginx がリッスンしています。

	![新しいエンドポイントのダイアログ][new-endpoint]

5. チェックマークをクリックして、エンドポイントを保存します。

6. **"更新が進行中です"** というメッセージが表示されます。このメッセージが消えると、エンドポイントがアクティブになります。この時点で仮想マシンの DNS 名に移動すると、アプリケーションをテストできます。Web サイトは次のように表示されます。

	![nginx の開始ページ][nginx-welcome]

2. 	次のコマンドを使用して、既定の Nginx Web サイトを停止して削除します。

		sudo service nginx stop
		sudo rm /etc/nginx/sites-enabled/default

	このチュートリアルの後の手順で実行する展開スクリプトでは、blog_app が Nginx によって提供される既定の Web サイトに設定されます。

##<a id="capify"></a>展開の準備をする

このセクションでは、Unicorn Web サーバーを使用するようにアプリケーションを変更し、デプロイ用に Capistrano を有効にします。さらに、仮想マシンから GitHub へのアクセスを有効にし、アプリケーションのデプロイと開始に使用するスクリプトを作成します。

1. [SSH キーの生成方法に関するページ](https://help.github.com/articles/generating-ssh-keys#platform-all)に記載されている手順を実行して、証明書を使用して GitHub アカウントに認証できるように仮想マシンを承認します。これは、展開スクリプトから GitHub リポジトリにアクセスするために使用されます。

	<div class="dev-callout">
	<strong>注</strong>
	<p>SSH キーは仮想マシン上で生成する必要がありますが、GitHub アカウントへのキーの追加には開発環境のブラウザーを使用できます。</p>
	<p>SSH 証明書の内容を表示するには、次のコマンドを使用します。この出力結果を GitHub にコピーして貼り付けることができます。</p>
	<pre>cat ~/.ssh/id_rsa.pub</pre>
	</div>


1. 開発用コンピューターで、**Gemfile** を変更し、**Capistrano** と **Unicorn** の行のコメントを解除します。このためには、次の行の先頭にある '#" 文字を削除します。

		# gem 'unicorn'
		# gem 'capistrano'

	<div class="dev-callout">
	<strong>メモ</strong>
	<p>Unicorn は Windows では利用できません。開発環境として Windows を使用している場合は、次のように <strong>Gemfile</strong> を変更して、VM に展開するときにのみ Unicorn のインストールを試みるようにしてください。</p>
	<pre class="prettyprint">platforms :ruby do<br />  gem 'unicorn'<br />end</pre>
	</div>
 
5. 	次のコマンドを実行して、新しい gem をインストールし、プロジェクト向けに Capistrano をセットアップします。
		
		bundle
		capify .

6. 	**blog_app/config** ディレクトリに次のファイルを追加し、以下のリンク先の内容で各ファイルを更新します。

	* [nginx.conf](https://gist.github.com/cff582f4f970a95991e9) - Nginx を Unicorn と連携させ、Rails アプリケーションに含まれている静的ファイルを提供するように構成します
	* [unicorn_init.sh](https://gist.github.com/3272994) - Unicorn サーバー プロセスを開始するときに使用するシェル スクリプト
	* [unicorn.rb](https://gist.github.com/3273014) - Unicorn の構成ファイル

	各ファイルでは、**blogger** という単語を、仮想マシンへのログインに使用するユーザー名に置き換えてください。このユーザーがアプリケーションの展開に使用されます。

7. **blog_app/config** ディレクトリで、**deploy.rb** ファイルを編集し、既存のコンテンツを次の内容に置き換えます。

		require "bundler/capistrano"

		set :application, "blog_app"
		set :user, "blogger"

		set :scm, :git
		set :repository, "git@github.com:YourGitHubAccount/blog_app.git"
		set :branch, "master"
		set :use_sudo, true

		server "VMDNSName", :web, :app, :db, primary: true

		set :deploy_to, "/home/#{user}/apps/#{application}"
		default_run_options[:pty] = true
		ssh_options[:forward_agent] = true
		ssh_options[:port] = SSHPort

		namespace :deploy do
		  desc "Fix permissions"
		  task :fix_permissions, :roles => [ :app, :db, :web ] do
		  	run "chmod +x #{release_path}/config/unicorn_init.sh"
		  end

		  %w[start stop restart].each do |command|
		    desc "#{command} unicorn server"
		    task command, roles: :app, except: {no_release: true} do
		      run "service unicorn_#{application} #{command}"
		    end
		  end

		  task :setup_config, roles: :app do
		    sudo "ln -nfs #{current_path}/config/nginx.conf /etc/nginx/sites-enabled/#{application}"
		    sudo "ln -nfs #{current_path}/config/unicorn_init.sh /etc/init.d/unicorn_#{application}"
		    sudo "mkdir -p #{shared_path}/config"
		  end
		  after "deploy:setup", "deploy:setup_config"

		  task :symlink_config, roles: :app do
		    # Add database config here
		  end
		  after "deploy:finalize_update", "deploy:fix_permissions"
		  after "deploy:finalize_update", "deploy:symlink_config"
		end

	上のテキストにおいて、次の単語は適切に置き換えてください。

	* **YourGitHubAccount** は GitHub アカウント名に置換
	* **VMDNSName** は、Azure 仮想マシンの DNS に置換
	* **blogger** は仮想マシンへのログインに使用するユーザー名に置換
	* **SSHPort** は仮想マシンの外部 SSH ポートに置換

	<div class="dev-callout">
	<strong>メモ</strong>
	<p>開発環境が Windows システムの場合は、<b>deploy.rb</b> ファイルに次の行を追加する必要があります。この行は、ファイルの先頭にある他の <b>set</b> ステートメントと一緒に記述する必要があります。</p>
	<pre>set :bundle_flags, "--no-deployment --quiet"</pre>
	<p>この方法では、デプロイ中に Gemfile.lock ではなく Gemfile から gem が読み込まれます。これはベスト プラクティスとは言えませんが、対象のシステム (Linux) が開発システム (Windows) と異なるために必要です。</p>
	</div>

7.	**blog_app** ディレクトリにある **Capfile** で、アセットの行のコメントを解除します。

		load 'deploy/assets'

8.	次のコマンドを実行して、プロジェクトへの変更をコミットし、GitHub にアップロードします。

		git add .
		git commit -m "adding config files"
		git push

##<a id="deploy"></a>展開

2.	ローカルの開発用コンピューターから次のコマンドを使用して、リモートの Azure VM を展開用にセットアップします。

		cap deploy:setup

	仮想マシンのユーザー アカウントのパスワードを要求されたら入力します。Capistrano が VM に接続し、ユーザー アカウントのホーム ディレクトリの下に **apps** ディレクトリを作成します。

3.	Azure VM への SSH 接続から、次のコマンドを使用して **app** ディレクトリのパーミッションを変更します。
		
		sudo chmod -R 777 apps

	<div class="dev-callout">
	<strong>注</strong>
	<p>これは運用環境では実行しない手順ですが、ここではデモンストレーションの目的で使用しています。</p>
	</div>

4.	開発環境で次のコマンドを使用して、コールド展開を実行します。これにより、アプリケーションが仮想マシンに展開され、Unicorn サービスが開始されます。

		cap deploy:cold

3.	Nginx サービスを開始します。これにより、Unicorn へのトラフィックのルーティングと静的コンテンツの提供が開始されます。

		sudo service nginx start

この時点で、Ruby on Rails アプリケーションは既に Azure の仮想マシンで動作していることになります。これを確認するには、Web ブラウザーに仮想マシンの DNS 名を入力します。たとえば、「http://railsvm.cloudapp.net」のように入力します。'Welcome aboard' という画面が表示されます。

!["Welcome aboard" ページ][default-rails-cloud]

URL に "/posts" を追加すると、投稿のインデックスが表示され、ポストの作成、編集、削除を行うことができます。

##<a id="next"></a>次のステップ

この記事では、基本的なフォーム ベースの Rails アプリケーションを作成し、Capistrano を使用して Azure の仮想マシンに発行する方法について説明しました。仮想マシンでは、Unicorn と Nginx を使用してアプリケーションへの Web 要求を処理しました。

より基礎的な例として、Rails アプリケーションを作成し、SSH のみを使用して Azure VM に展開する手順については、「[Linux VM を使用した Azure での Ruby on Rails Web アプリケーション][ruby-vm]」を参照してください。

Ruby on Rails の詳細について学習するには、「[Ruby on Rails のガイド][rails-guides]」を参照してください。

Azure SDK for Ruby を使用して Ruby アプリケーションから Azure サービスにアクセスする方法については、次のリンクを参照してください。

* [BLOB を使用して非構造化データを保存する][blobs]

* [テーブルを使用してキー/値のペアを保存する][tables]

* [コンテンツ配信ネットワークを使用して高帯域幅コンテンツを配信する][cdn-howto]

[vm-instructions]: /ja-jp/manage/linux/tutorials/virtual-machine-from-gallery/


[rails-guides]: http://guides.rubyonrails.org/
[blobs]: /ja-jp/develop/ruby/how-to-guides/blob-storage/
[tables]: /ja-jp/develop/ruby/how-to-guides/table-service/
[cdn-howto]: /ja-jp/develop/ruby/app-services/
[ruby-vm]: /ja-jp/develop/ruby/tutorials/web-app-with-linux-vm/
 
[blog-rails]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilslocal.png
[blog-rails-cloud]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilscloud.png 
[default-rails]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailslocal.png
[default-rails-cloud]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailscloud.png
[vmlist]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/vmlist.png
[endpoints]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/endpoints.png
[new-endpoint]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/newendpoint80.png
[nginx-welcome]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/welcomenginx.png

[management-portal]: https://manage.windowsazure.com/
[sqlite3]: http://www.sqlite.org/


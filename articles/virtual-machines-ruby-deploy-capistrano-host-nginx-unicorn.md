<properties urlDisplayName="Ruby on Rails Azure VM Capistrano" pageTitle="Capistrano を使用して Azure 仮想マシンに Ruby on Rails Web アプリケーションをデプロイする - チュートリアル" metaKeywords="ruby on rails, ruby on rails azure, rails azure, rails vm, capistrano azure vm, capistrano azure rails, unicorn azure vm, unicorn azure rails, unicorn nginx capistrano, unicorn nginx capistrano azure, nginx azure" description="Capistrano、Unicorn、および Nginx を使用して Azure 仮想マシンに Ruby on Rails Web アプリケーションをデプロイする方法について説明します。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Capistrano を使用して Azure VM に Ruby on Rails Web アプリケーションをデプロイする" authors="larryfr" manager="wpickett" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-linux" ms.devlang="ruby" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Capistrano を使用して Azure VM に Ruby on Rails Web アプリケーションをデプロイする

このチュートリアルでは、[Capistrano][Capistrano] を使用して Azure の仮想マシンに Ruby on Rails Web サイトをデプロイする方法について説明します。デプロイすると、[Nginx][Nginx] と [Unicorn][Unicorn] を使用して Web サイトをホストします。[PostgreSQL][PostgreSQL] は、デプロイされるアプリケーションのアプリケーション データを保存します。

このチュートリアルは、Azure を初めて使用するユーザーを対象としていますが、Ruby、Rails、Git、および Linux について詳しく理解していることを前提としています。このチュートリアルを完了すると、クラウドで動作する Ruby on Rails ベースのアプリケーションが完成します。

学習内容:

-   開発環境と運用環境を設定する

-   Ruby および Ruby on Rails をインストールする

-   Unicorn、Nginx、および PostgreSQL をインストールする

-   新しい Rails アプリケーションを作成する

-   Capistrano デプロイメントを作成し、これを使用してアプリケーションをデプロイする

完成したアプリケーションのスクリーンショットは次のようになります。

![a browser displaying Listing Posts][a browser displaying Listing Posts]

> [WACOM.NOTE] このチュートリアルで使用するアプリケーションには、ネイティブのバイナリ コンポーネントが含まれています。開発環境が Linux ベースでない場合、VM へのデプロイ時にエラーが発生する場合があります。デプロイ中に使用される Gemfile.lock ファイルにはプラットフォーム固有の gem が含まれますが、ここには VM で必要な gem のネイティブ Linux バージョンのエントリが含まれていないことがあります。
>
> Windows 開発環境を使用する場合向けに、固有の手順を記載しています。ただし、この記事で説明されていないエラーがデプロイ中やデプロイ後に発生した場合は、この記事の手順を Linux ベースの開発環境から再試行することをお勧めします。

## この記事の内容

-   [開発環境を設定する][開発環境を設定する]

-   [Rails アプリケーションを作成する][Rails アプリケーションを作成する]

-   [アプリケーションをテストする][アプリケーションをテストする]

-   [ソース リポジトリを作成する][ソース リポジトリを作成する]

-   [Azure の仮想マシンを作成する][Azure の仮想マシンを作成する]

-   [Nginx をテストする][Nginx をテストする]

-   [デプロイの準備をする][デプロイの準備をする]

-   [展開][展開]

-   [次のステップ][次のステップ]

## <span id="setup"></span></a>開発環境を設定する

1.  開発環境に Ruby をインストールします。この手順は、使用しているオペレーティング システムによって異なる場合があります。

    -   **Apple OS X** - OS X 用には、いくつかの Ruby ディストリビューションがあります。このチュートリアルの検証には、OS X で [Homebrew][Homebrew] を使用して **rbenv**、**ruby-build**、および **Ruby 2.0.0-p451** をインストールしました。インストールに関する情報は、<https://github.com/sstephenson/rbenv/> で確認できます。

    -   **Linux** - ディストリビューションのパッケージ管理システムを使用します。このチュートリアルは、Ubuntu 12.10 で **rbenv**、**ruby-build**、および **Ruby 2.0.0-p451** を使用して検証されました。

    -   **Windows** - Windows 用には、いくつかの Ruby ディストリビューションがあります。このチュートリアルの検証では、[RubyInstaller][RubyInstaller] を使用して **Ruby 2.0.0-p451** をインストールしました。コマンドは [Git for Windows][Git for Windows] で使用可能な **GitBash** コマンド ラインを使用して実行されました。

2.  新しいコマンド ラインまたはターミナル セッションを開き、次のコマンドを入力して Ruby on Rails をインストールします。

        gem install rails --no-rdoc --no-ri

    > [WACOM.NOTE] オペレーティング システムによっては、このコマンドには管理者特権または root 権限が必要となる場合があります。このコマンドの実行中にエラーが発生した場合は、次のように "sudo" を使用してください。
    >
    > `sudo gem install rails`

    > [WACOM.NOTE] このチュートリアルでは、Rails gem Version 4.0.4 を使用しました。

3.  JavaScript インタープリターもインストールする必要があります。これは、Rails アプリケーションで使われる CoffeeScript アセットをコンパイルするために Rails によって使用されます。サポートされているインタープリターの一覧は、<https://github.com/sstephenson/execjs#readme> で確認できます。

    > [WACOM.NOTE] [Node.js] (http://nodejs.org/) をこのチュートリアルの検証中に使用しました。このインタープリターは OS X、Linux、Windows の各オペレーティング システムで利用できるためです。

## <span id="create"></span></a>Rails アプリケーションを作成する

1.  コマンド ラインまたはターミナル セッションから、次のコマンドを使用して、"blog\_app" という名前の新しい Rails アプリケーションを作成します。

        rails new blog_app

    これは、**blog\_app** という新しいディレクトリを作成し、Rails アプリケーションに必要なファイルとサブディレクトリをそこに格納します。

    > [WACOM.NOTE] このコマンドが完了するまでには、しばらく時間がかかることがあります。このコマンドによって既定のアプリケーションに必要な gem のサイレント インストールが実行されますが、実行中は応答しなくなります。

2.  ディレクトリを **blog\_app** に変更し、次のコマンドを使用して、基本的なブログ スキャフォールディングを作成します。

        rails generate scaffold Post name:string title:string content:text

    これにより、コントローラー、ビュー、モデル、およびブログへの投稿を保持するためのデータベースの移行が作成されます。各投稿には、作者名、記事のタイトル、およびテキスト コンテンツが含められます。

3.  次のコマンドを実行して、ブログへの投稿を保存するデータベースを作成します。

        rake db:migrate

    このコマンドで、Rails の既定のデータベース プロバイダーである [SQLite3 データベース][SQLite3 データベース]を使用して投稿を保存するデータベース スキーマが作成されます。

4.  ホーム ページとして投稿の一覧を表示するには、**config/routes.rb** ファイルを変更して、`resources :posts` 行の後に次のコードを追加します。

        root 'posts#index'

    これでユーザーが Web サイトを訪問したときに投稿のリストが表示されます。

## <span id="test"></span></a> アプリケーションをテストします。

1.  別のディレクトリにいる場合は、**blog\_app** ディレクトリに変更します。次のコマンドを使用して rails サーバーを開始します。

        rails s

    次のような出力が表示されます。Web サーバーがリッスンしているポートに注目してください。下の例では、ポート 3000 をリッスンしています。

        => Booting WEBrick
        => Rails 4.0.4 application starting in development on http://0.0.0.0:3000
        => Call with -d to detach
        => Ctrl-C to shutdown server
        [2013-03-12 19:11:31] INFO  WEBrick 1.3.1
        [2013-03-12 19:11:31] INFO  ruby 2.0.0 (2014-02-24) [x86_64-linux]
        [2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2.  ブラウザーを開き、http://localhost:3000/ に移動します。次のようなページが表示されます。

    ![a page listing posts][a page listing posts]

    サーバー プロセスを停止するには、コマンド ラインで Ctrl + C キーを押します。

## <span id="repository"></span></a>ソース リポジトリを作成する

Capistrano を使用してアプリケーションをデプロイすると、リポジトリから対象のファイルがプルされます。このチュートリアルでは、バージョン管理に [Git][Git] を使用し、リポジトリに [GitHub][GitHub] を使用します。

1.  [GitHub][GitHub] で新しいリポジトリを作成します。GitHub アカウントを持っていない場合は、無料のアカウントにサインアップできます。次の手順では、リポジトリ名が **blog\_app** であることを前提としています。

    > [WACOM.NOTE] アプリケーションの自動デプロイメントをサポートするには、GitHub への認証に SSH キーを使用する必要があります。詳細については、[Generating SSH Keys (SSH キーの生成方法)][Generating SSH Keys (SSH キーの生成方法)] の GitHub ドキュメントを参照してください。

2.  コマンド プロンプトから、ディレクトリを **blog\_app** に変更し、次のコマンドを実行して、アプリケーションを GitHub リポジトリにアップロードします。**YourGitHubName** を GitHub アカウントの名前に置き換えます。

        git init
        git add .
        git commit -m "initial commit on azure"
        git remote add origin git@github.com:YourGitHubName/blog-azure.git
        git push -u origin master

次のセクションでは、このアプリケーションがデプロイされる仮想マシンを作成します。

## <span id="createvm"></span></a>Azure の仮想マシンを作成する

[ここ][ここ]に記載されている指示に従って、Linux をホストする Azure の仮想マシンを作成します。

1.  [Azure 管理ポータル][Azure 管理ポータル]にサインインします。コマンド バーで、**[新規]** を選択します。

2.  **[仮想マシン]**、**[ギャラリーから]** の順に選択します。

3.  **[イメージの選択]** から **[Ubuntu]** を選択し、次にバージョン **[12.04 LTS]** を選択します。矢印を選択して続行します。

    > [WACOM.NOTE] このチュートリアルの手順は、Ubuntu 12.04 LTS をホストする Azure 仮想マシンで実行しています。他の Linux ディストリビューションを使用する場合は、同じタスクを完了するために別の手順が必要になることがあります。

4.  **[仮想マシン名]** に、使用する名前を入力します。この名前は、この仮想マシンのドメイン名を作成するときに使用します。

5.  **[新しいユーザー名]** に、このマシンの管理者アカウントの名前を入力します。

    > [WACOM.NOTE] このチュートリアルでは、アプリケーションのデプロイでも管理者アカウントが使用されます。デプロイメント用の個別のアカウントを作成する詳細については、[Capistrano][1] のドキュメントを参照してください。

6.  **[認証]** で、**[互換性のある認証用の SSH キーのアップロード]** をチェックしてから、証明書が含まれた **.pem** ファイルを検索して選択します。最後に、矢印を選択して続行します。

    > [WACOM.NOTE] SSH キーの生成または使用に慣れていない場合は、「[How to use SSH with Linux on Azure (Azure 上の Linux における SSH の使用方法)][How to use SSH with Linux on Azure (Azure 上の Linux における SSH の使用方法)]」で SSH キーの作成手順を参照してください。
    >
    > パスワードの認証も有効にできますが、自動デプロイメントで使用されるため、SSH キーも指定する必要があります。

7.  **[エンドポイント]** で、**[値を入力または選択してください]** ドロップダウンの **[HTTP]** を選択します。このページ上の他のフィールドは既定値のままにしておくことができます。後の手順で使用するため、**[クラウド サービス DNS 名]** をメモしておきます。最後に、矢印を選択して続行します。

8.  最後のページで、仮想マシンを作成するチェックマークを選択します。

### Git、Ruby、および Nginx のインストール

仮想マシンが作成されたら、SSH を使用してマシンを接続し、次のコマンドを使用して Ruby アプリケーションのためのホスティング環境を準備します。

    sudo apt-get update
    sudo apt-get -y upgrade
    sudo apt-get -y install git build-essential libssl-dev curl nodejs nginx
    git clone git://github.com/sstephenson/rbenv.git ~/.rbenv
    echo 'export RBENV_ROOT=~/.rbenv' >> ~/.bash_profile
    echo 'export PATH="$RBENV_ROOT/bin:$PATH"' >> ~/.bash_profile
    echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
    source ~/.bash_profile
    eval "$(rbenv init -)"
    git clone git://github.com/sstephenson/ruby-build.git
    cd ruby-build
    sudo ./install.sh
    ~/.rbenv/bin/rbenv install 2.0.0-p451
    ~/.rbenv/bin/rbenv global 2.0.0-p451
    gem install bundler
    ~/.rbenv/bin/rbenv rehash

> [WACOM.NOTE] 前のコマンドをスクリプト (.sh ファイル) に保存しておくと、実行時に入力しなくてすみます。

> [WACOM.NOTE] **~/.rbenv/bin/rbenv install 2.0.0-p451** コマンドは完了に数分かかる場合があります。

**rbenv-install.sh** スクリプトは次のアクションを実行します。

-   現在インストール済みのパッケージの更新とアップグレード
-   ビルド ツールのインストール
-   Git のインストール
-   Node.js のインストール
-   Nginx のインストール
-   Ruby と Rails で必要なビルド ツールと他のユーティリティのインストール
-   [rbenv] のローカル (ユーザー) デプロイメントのセットアップ
-   Ruby 2.0.0-p451 のインストール
-   [Bundler] gem のインストール

インストールが完了したら、次のコマンドを使用して Ruby が正常にインストールされたことを確認します。

    ruby -v

このコマンドで、`ruby 2.0.0p451` がバージョンとして返されます。

### PostgreSQL のインストール

Rails で開発用に使用される既定のデータベースは SQLite です。通常、運用環境では別のデータベースを使用します。次の手順では、仮想マシンに PostgreSQL をインストールし、その後、ユーザーとデータベースを作成します。後の手順では、デプロイメント時に PostgreSQL を使用するように Rails アプリケーションを構成します。

1.  次のコマンドを使用して PostgreSQL と開発用のプログラム片をインストールします。

        sudo apt-get -y install postgresql postgresql-contrib libpq-dev

2.  次のコマンドを使用してログイン用のユーザーとデータベースを作成します。**my\_username** と **my\_database** をログイン名に置き換えます。たとえば、vm のログイン名が **deploy** の場合は、これを **my\_username** と **my\_database** を **deploy** に置き換えてください。

        sudo -u postgres createuser -D -A -P my_username
        sudo -u postgres createdb -O my_username my_database

    > [WACOM.NOTE] ユーザー名をデータベース名にも使用します。このアプリケーションが使用する capistrano-postgresql gem では、このようにする必要があります。

    入力を求められたら、ユーザーのパスワードを入力します。新しいロールの作成をユーザーに許可するよう求められたら、**y** を選択します。これは、Rails アプリケーションが使用するデータベースとログインの作成のデプロイ時にこのユーザーが使用されるためです。

3.  ユーザー アカウントを使用してデータベースに接続できるか検証するには、次のコマンドを使用します。**my\_username** と **my\_database** を先ほど使用した値に置き換えます。

        psql -U my_username -W my_database

    この後、`database=>` プロンプトが表示されます。psql ユーティリティを終了するには、プロンプトから「`\q`」と入力します。

### <span id="nginx"></span></a>Nginx をテストする

仮想マシンの作成時に追加した HTTP エンドポイントによって、ポート 80 経由の HTTP 要求の受け入れが可能になります。これを検証するには、次の手順に従って Nginx で作成された既定のサイトにアクセスできるか確認します。

1.  VM への SSH セッションから、Nginx サービスを開始します。

        sudo service nginx start

    これにより Nginx サービスが開始され、ポート 80 で受信トラフィックをリッスンします。

2.  仮想マシンの DNS 名に移動して、アプリケーションをテストします。Web サイトは次のようになります。

    ![nginx welcome page][nginx welcome page]

    > [WACOM.NOTE] このチュートリアルの後の手順で実行するデプロイメント スクリプトでは、blog\_app が Nginx によって提供される既定の Web サイトに設定されます。

この時点で、Azure 仮想マシンには、デプロイの準備が完了した Ruby、Nginx、および PostgreSQL があります。次のセクションで、デプロイを実行するスクリプトと情報を追加するために Rails アプリケーションを変更します。

## <span id="capify"></span></a>展開の準備をする

開発環境で、Unicorn Web サーバーと PostgreSQL を使用するようにアプリケーションを変更し、デプロイ用に Capistrano を有効にします。また、デプロイとアプリケーションの起動に使用するスクリプトを作成します。

1.  開発用のマシンで、次のように **Gemfile** を変更し、次の行を追加して Unicorn、PostgreSQL、Capistrano 用の gem と、Rails アプリケーション用の関連 gem を追加します。

        # Use Unicorn
        gem 'unicorn'
        # Use PostgreSQL
        gem 'pg', group: :production

        group :development do
          # Use Capistrano for deployment
          gem 'capistrano', '~> 3.1'
          gem 'capistrano-rails', '~> 1.1.1'
          gem 'capistrano-bundler'
          gem 'capistrano-rbenv', '~> 2.0'
          gem 'capistrano-unicorn-nginx', '~> 2.0'
          gem 'capistrano-postgresql', '~> 3.0'
        end

    > [WACOM.NOTE] Unicorn は Windows では利用できません。開発環境として Windows を使用している場合は、次のように unicorn gem を指定して VM にデプロイするときにのみ Unicorn のインストールを試みるように、**Gemfile** を変更してください。
    >
    > `platforms :ruby do`
    > `gem 'unicorn'`
    > `end`

    大部分の capistraon-\* gem は、特定の対象で動作するヘルパーであり、運用サーバー (rbenv) またはフレームワーク (rails) 上で使用します。

    capistrano-unicorn-nginx gem は、Unicorn と Nginx で使用するスクリプトをデプロイ中に自動作成するため、これらのスクリプトを手動で作成する必要はありません。capistrano-postgresql は、アプリケーションで使用する PostgreSQL のデータベース、ユーザー、パスワードを自動生成します。これらを使用する必要はありませんが、いずれもデプロイメントのプロセスを大きく簡略化します。

2.  次のコマンドを使用して新しい gem をインストールします。

        bundle

3.  次のコマンドを使用して、Capistrano で使用される既定のデプロイメント ファイルを作成します。

        cap install

    `cap install`コマンドが終了すると、次のファイルとディレクトリがアプリケーションに追加されます。

        ├── Capfile
        ├── config
        │   ├── deploy
        │   │   ├── production.rb
        │   │   └── staging.rb
        │   └── deploy.rb
        └── lib
            └── capistrano
                    └── tasks

    **deploy.rb** ファイルがあらゆるタイプのデプロイメント用の一般的な構成とアクションを提供する一方で、**production.rb** と **staging.rb** には、それぞれ、運用デプロイメントとステージング デプロイメントの追加の構成が含まれています。

    **capistrano** フォルダーには、デプロイメント プロセスの一部として使用されるタスクと他のファイルが含まれています。

4.  アプリケーションのルートで **Capfile** を編集し、次の各行で、行の先頭から **\#** 文字を削除してコメント解除します。

        require 'capistrano/rbenv'
        require 'capistrano/bundler'
        require 'capistrano/rails/assets'
        require 'capistrano/rails/migrations'

    前の行でコメント解除したら、次の行を追加します。

        require 'capistrano/unicorn_nginx'
        require 'capistrano/postgresql'

    これらの行は、先に Gemfile に追加された gem を使用するよう Capistrano に指示します。

    前の変更が終了したら、ファイルを保存します。

5.  **config/deploy.rb** ファイルを編集して、ファイルのコンテンツを次のように置き換えます。**YourApplicationName** をアプリケーションの名前に置き換えます。また、**https://github.com/YourGitHubName/YourRepoName.git** を、このプロジェクトの GitHub リポジトリの URL に置き換えます。

        lock '3.1.0'
        # application name and the github repository
        set :application, 'YourApplicationName'
        set :repo_url, 'git@github.com:YourGitHubName/YourRepoName.git'

        # describe the rbenv environment we are deploying into
        set :rbenv_type, :user
        set :rbenv_ruby, '2.0.0-p451'
        set :rbenv_prefix, "RBENV_ROOT=#{fetch(:rbenv_path)} RBENV_VERSION=#{fetch(:rbenv_ruby)} #{fetch(:rbenv_path)}/bin/rbenv exec"
        set :rbenv_map_bins, %w{rake gem bundle ruby rails}

        # dirs we want symlinked to the shared folder
        # during deployment
        set :linked_dirs, %w{bin log tmp/pids tmp/cache tmp/sockets vendor/bundle public/system}

        namespace :deploy do

          desc 'Restart application'
          task :restart do
            on roles(:app), in: :sequence, wait: 5 do
              # Your restart mechanism here, for example:
              # execute :touch, release_path.join('tmp/restart.txt')
              #
              # The capistrano-unicorn-nginx gem handles all this
              # for this example
            end
          end

          after :publishing, :restart

          after :restart, :clear_cache do
            on roles(:web), in: :groups, limit: 3, wait: 10 do
              # Here we can do anything such as:
              # within release_path do
              #   execute :rake, 'cache:clear'
              # end
            end
          end

        end

    このファイルは、すべてのデプロイメントのタイプで共通の一般情報とタスクを提供します。

6.  **config/deploy/production.rb** ファイルを編集して、既存のコンテンツを次のように置き換えます。**YourVm.cloudapp.net** を VM のドメイン名に置き換えます。**YourAzureVMUserName** を、先ほど Azure VM 用に作成したログイン アカウントに置き換えます。

        # production deployment
        set :stage, :production
        # use the master branch of the repository
        set :branch, "master"
        # the user login on the remote server
        # used to connect and deploy
        set :deploy_user, "YourAzureVMUserName"
        # the 'full name' of the application
        set :full_app_name, "#{fetch(:application)}_#{fetch(:stage)}"
        # the server(s) to deploy to
        server 'YourVM.cloudapp.net', user: 'YourAzureVMUserName', roles: %w{web app db}, primary: true
        # the path to deploy to
        set :deploy_to, "/home/#{fetch(:deploy_user)}/apps/#{fetch(:full_app_name)}"
        # set to production for Rails
        set :rails_env, :production

    このファイルは、運用デプロイメントに固有の情報を提供します。

7.  次のコマンドを実行して、前の手順で行ったファイルへの変更をコミットし、次に変更を GitHub にアップロードします。

        git add .
        git commit -m "adding config files"
        git push

これでアプリケーションはデプロイする準備ができました。

> [WACOM.NOTE] より複雑なアプリケーション、または、別のデータベースかアプリケーションには、追加の構成スクリプトやデプロイメント スクリプトが必要になることがあります。

## <span id="deploy"></span></a>展開

1.  ローカルの開発マシンから、次のコマンドを使用して、アプリケーションで使用する構成ファイルを VM にデプロイします。

        bundle exec cap production setup

    Capistrano は SSH を使用して VM に接続してから、アプリケーションがデプロイされるディレクトリ (~/apps) を作成します。これが初回のデプロイメントの場合、capistrano-postgresql gem によってロールとデータベースもサーバー上の PostgreSQL に作成されます。また、Rails がデータベースに接続するために使用する database.yml 構成ファイルも作成されます。

    > [WACOM.NOTE] デプロイ中に、"**認証ソケットからの応答読み取り中の長さエラー**" というエラーが表示された場合は、次の `ssh-agent` コマンドを使用して SSH エージェントを開始する必要がある場合があります。たとえば、`eval $(ssh-agent)` を ~/.bash\_profile ファイルに追加します。
    >
    > また、次の `ssh-add` コマンドを使用して SSH キーを追加する必要が発生する場合もあります。

2.  次のコマンドを使用して運用デプロイを実行します。これによってアプリケーションが仮想マシンにデプロイされ、Unicorn サービスが開始されます。さらに、Unicorn にトラフィックがルーティングされるように Nginx が構成されます。

        bundle exec cap production deploy

    このコマンドはアプリケーションを VM にデプロイして必要な gem をインストールし、その後、Unicorn と Nginx を起動または再起動します。

    > [WACOM.NOTE] このプロセスは処理中に、数分間一時停止する場合があります。

    > [WACOM.NOTE] デプロイメントの一部から 'exit status 1 (failed)' ('終了ステータス 1 (失敗)') が返される場合があります。デプロイメントが正常に終了していれば、このステータスは通常は無視できます。

    > [WACOM.NOTE] 一部のシステムでは、GitHub への認証時に SSH エージェントがリモート VM に資格情報を転送できなくなる場合があります。この場合、エラーを解決する代替方法として、**config/deploy.rb** ファイルを変更し、`set :repo_url` 行を、GitHub へのアクセス時に HTTPS を使用するように変更する方法があります。HTTPS を使用する場合は、GitHub ユーザー名とパスワード (または認証トークン) を URL の一部として指定する必要があります。次に例を示します。
    >
    > 'set :repo\_url, 'https://you:yourpassword@github.com/You/yourrepository.git'
    >
    > これによってエラーを回避でき、このチュートリアルも終了できますが、運用デプロイメントにお勧めできる解決法ではありません。この方法は、資格情報をアプリケーションの一部としてプレーンテキストで保存するためです。SSH エージェントでの転送について、オペレーティング システムのドキュメントを確認してください。

この時点で、Ruby on Rails アプリケーションは既に Azure の仮想マシンで動作していることになります。これを確認するには、Web ブラウザーに仮想マシンの DNS 名を入力します。たとえば、「http://railsvm.cloudapp.net」のように入力します。投稿のインデックスが表示され、ポストを作成、編集、削除できます。

## <span id="next"></span></a>次のステップ

この記事では、基本的な Rails アプリケーションを作成し、Capistrano を使用して Azure の仮想マシンに発行する方法について説明しました。この記事で紹介した基本的なアプリケーションを使用した方法は、デプロイメントのために Capistrano でできることの一例にすぎません。Capistrano の使用方法の詳細については、以下を参照してください。

-   [Capistranorb.com][1] - Capistrano のサイトです。
-   [Azure, Ruby on Rails, Capistrano 3, & PostgreSQL][Azure, Ruby on Rails, Capistrano 3, & PostgreSQL] - Azure にデプロイする代替的な手法で、カスタム デプロイメント スクリプトについて説明します。
-   [Capistrano 3 tutorial (Capistrano 3 チュートリアル)][Capistrano 3 tutorial (Capistrano 3 チュートリアル)] - Capistrano 3 を使用したチュートリアルです。

より基礎的な例として、Rails アプリケーションを作成し、SSH のみを使用して Azure VM に展開する手順については、「[Linux VM を使用した Azure での Ruby on Rails Web アプリケーション][Linux VM を使用した Azure での Ruby on Rails Web アプリケーション]」を参照してください。

Ruby on Rails の詳細について学習するには、[Ruby on Rails のガイド][Ruby on Rails のガイド]を参照してください。

Azure SDK for Ruby を使用して Ruby アプリケーションから Azure サービスにアクセスする方法については、次のリンクを参照してください。

-   [BLOB を使用して非構造化データを保存する][BLOB を使用して非構造化データを保存する]

-   [テーブルを使用してキー/値のペアを保存する][テーブルを使用してキー/値のペアを保存する]

-   [コンテンツ配信ネットワークを使用して高帯域幅コンテンツを配信する][コンテンツ配信ネットワークを使用して高帯域幅コンテンツを配信する]

  [Capistrano]: https://github.com/capistrano/capistrano/
  [Nginx]: http://nginx.org/
  [Unicorn]: https://github.com/blog/517-unicorn
  [PostgreSQL]: https://www.postgresql.org
  [a browser displaying Listing Posts]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilscloud.png
  [開発環境を設定する]: #setup
  [Rails アプリケーションを作成する]: #create
  [アプリケーションをテストする]: #test
  [ソース リポジトリを作成する]: #repository
  [Azure の仮想マシンを作成する]: #createvm
  [Nginx をテストする]: #nginx
  [デプロイの準備をする]: #capify
  [展開]: #deploy
  [次のステップ]: #next
  [Homebrew]: http://brew.sh/
  [RubyInstaller]: http://RubyInstaller.org/
  [Git for Windows]: http://git-scm.com/download/win
  [SQLite3 データベース]: http://www.sqlite.org/
  [a page listing posts]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilslocal.png
  [Git]: http://git-scm.com/
  [GitHub]: https://github.com/
  [Generating SSH Keys (SSH キーの生成方法)]: https://help.github.com/articles/generating-ssh-keys
  [ここ]: /ja-jp/manage/linux/tutorials/virtual-machine-from-gallery/
  [Azure 管理ポータル]: https://manage.windowsazure.com/
  [1]: http://capistranorb.com
  [How to use SSH with Linux on Azure (Azure 上の Linux における SSH の使用方法)]: http://azure.microsoft.com/ja-jp/documentation/articles/linux-use-ssh-key/
  [nginx welcome page]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/welcomenginx.png
  [Azure, Ruby on Rails, Capistrano 3, & PostgreSQL]: http://wootstudio.ca/articles/tutorial-windows-azure-ruby-on-rails-capistrano-3-postgresql
  [Capistrano 3 tutorial (Capistrano 3 チュートリアル)]: http://www.talkingquickly.co.uk/2014/01/deploying-rails-apps-to-a-vps-with-capistrano-v3/
  [Linux VM を使用した Azure での Ruby on Rails Web アプリケーション]: /ja-jp/develop/ruby/tutorials/web-app-with-linux-vm/
  [Ruby on Rails のガイド]: http://guides.rubyonrails.org/
  [BLOB を使用して非構造化データを保存する]: /ja-jp/develop/ruby/how-to-guides/blob-storage/
  [テーブルを使用してキー/値のペアを保存する]: /ja-jp/develop/ruby/how-to-guides/table-service/
  [コンテンツ配信ネットワークを使用して高帯域幅コンテンツを配信する]: /ja-jp/develop/ruby/app-services/

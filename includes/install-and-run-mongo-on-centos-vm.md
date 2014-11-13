次のステップに従って、MongoDB を CentOS Linux が実行されている仮想マシンにインストールして実行します。

<div class="dev-callout">
<b>警告</b>
<p>認証、IP アドレス バインドなどの MongoDB セキュリティ機能は既定では有効になっていません。MongoDB を運用環境に展開する前に、セキュリティ機能を有効にすることをお勧めします。詳細については、「<a href="http://www.mongodb.org/display/DOCS/Security+and+Authentication">Security and Authentication (セキュリティと認証)</a>」を参照してください。</p>
</div>

1.  MongoDB をインストールできるようにパッケージ管理システム (YUM) を構成します。*/etc/yum.repos.d/10gen.repo* ファイルを作成し、リポジトリの情報を保存して、以下を追加します。

        [10gen]
        name=10gen Repository
        baseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64
        gpgcheck=0
        enabled=1

2.  repo ファイルを保存し、次のコマンドを実行して、ローカル パッケージ データベースを更新します。

        $ sudo yum update

3.  パッケージをインストールするには、次のコマンドを実行し、MongoDB の最新の安定バージョンと関連ツールをインストールします。

        $ sudo yum install mongo-10gen mongo-10gen-server

    MongoDB がダウンロードおよびインストールされるまで待ちます。

4.  データ ディレクトリを作成します。MongoDB では */data/db* ディレクトリにデータが保存されるように既定で設定されていますが、このディレクトリは作成する必要があります。作成するには、次のように実行します。

        $ sudo mkdir -p /srv/datadrive/data
        $ sudo chown `id -u` /srv/datadrive/data

    Linux への MongoDB のインストールの詳細については、「[Install MongoDB on Linux (Linux への MongoDB のインストール)][Install MongoDB on Linux (Linux への MongoDB のインストール)]」を参照してください。

5.  データベースを開始するには、次を実行します。

        $ mongod --dbpath /srv/datadrive/data --logpath /srv/datadrive/data/mongod.log

    MongoDB サーバーがジャーナル ファイルを開始して事前に割り当てると、すべてのログ メッセージが */srv/datadrive/data/mongod.log* ファイルにダイレクトされます。MongoDB がジャーナル ファイルを事前に割り当てて、接続のリッスンを開始するには、数分かかる場合があります。

6.  MongoDB 管理シェルを開始するには、個別の SSH または PuTTY ウィンドウを開いて実行します。

        $ mongo
        > db.foo.save ( { a:1 } )
        > db.foo.find()
        { _id : ..., a : 1 }
        > show dbs  
        ...
        > show collections  
        ...  
        > help  

    データベースは挿入によって作成されます。

7.  MongoDB をインストールしたら、MongoDB にリモートでアクセスできるように、エンドポイントを構成する必要があります。管理ポータルで、**[仮想マシン]**、新しい仮想マシンの名前、**[エンドポイント]** の順にクリックします。

    ![エンドポイント][エンドポイント]

8.  ページの下部にある **[エンドポイントの追加]** をクリックします。

    ![エンドポイント][1]

9.  エンドポイントを追加します。名前に「Mongo」、プロトコルに **[TCP]** を指定し、**[パブリック ポート]** と **[プライベート ポート]** に「27017」を指定します。これにより、MongoDB へのリモート アクセスが可能になります。

    ![エンドポイント][2]

  [Install MongoDB on Linux (Linux への MongoDB のインストール)]: http://www.mongodb.org/display/DOCS/Quickstart+Unix
  [エンドポイント]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint.png
  [1]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint2.png
  [2]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint3.png

<properties linkid="services-linux-cassandra-with-linux" urlDisplayName="Linux での Cassandra" pageTitle="Azure 上の Linux での Cassandra の実行" metaKeywords="" description="Azure の仮想マシンの Linux で Cassandra クラスターを実行する方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="Node.js" title="Azure 上の Linux で Cassandra を実行して Node.js からアクセス" authors="" solutions="" manager="" editor="" />





<h1><a id = ""></a>Azure 上の Linux で Cassandra を実行して Node.js からアクセス</h1>
**執筆者:** Hanu Kommalapati

## 目次##

- [概要][]
- [Cassandra の展開方式][]
- [複合展開][]
- [仮想マシンの展開][]
- [タスク 1: Linux クラスターの展開][]
- [タスク 2: 各仮想マシンでの Cassandra のセットアップ][]
- [タスク 3: Node.js からの Cassandra クラスターへのアクセス][]
- [まとめ][]


##<a id="overview"> </a>概要##

Azure には、スキーマなしでビジネス オブジェクトを保存できる Azure テーブル ストレージによる NoSQL データベース サービスが用意されています。このサービスは、HTTP および REST をサポートする Node.js、.NET、Java などの言語から使用できます。ただし、ほかにも Cassandra や Couchbase のように、ステートレス クラウド モデルであるために Azure PaaS では実行できない人気の NoSQL データベースも存在します。Azure の仮想マシンでは、現在、このような NoSQL データベースをコードベースを変更することなく Azure 上で実行できます。この文章の目的は、仮想マシン上で Cassandra クラスターを実行し、それに Node.js からアクセスする方法を示すことです。実際の運用環境への Cassandra の展開については取り上げません。運用環境では、マルチ データ センターの Cassandra クラスターおよび関連するバックアップ戦略と回復戦略に目を向ける必要があります。この演習では、Linux の Ubuntu バージョン 12.04 および Cassandra 1.0.10 を使用します。ただし、手順はどの Linux ディストリビューションに対しても応用できます。

## <a id="schematic"> </a>Cassandra のデプロイ方式##

Azure の仮想マシン機能では、[Cassandra](http://wiki.apache.org/cassandra/) のような NoSQL データベースを、プライベート クラウド環境と同じように簡単に Microsoft パブリック クラウド上で実行できます。違うのは 1 つだけ、Azure の仮想マシン インフラストラクチャに特有の仮想ネットワーク構成だけです。この記事の執筆時点では、Cassandra は Azure 上の管理されたサービスとしては利用できません。そのため、この記事では、仮想マシン上で Cassandra クラスターをセットアップして、仮想マシン内部でホストされた別の Linux インスタンスからアクセスします。ここで示した node.js のコード断片は、PaaS でホストしている Web アプリケーションまたは Web サービスからも使用できます。Azure の中核的な強みの 1 つは、PaaS の世界と IaaS の世界で優れたところを共に活用する複合アプリケーション モデルが可能なことです。

Cassandra アプリケーション環境で実現できる展開モデルは 2 つあります。自立的仮想マシン展開と複合展開です。複合デプロイの場合、仮想マシンでホストされた Cassandra クラスターは、PaaS でホストされた Azure Web アプリケーション (または Web サービス) から Thrift インターフェイスを使用してロード バランサーを介して利用されます。各 Cassandra ノードは、キー スペース フォールト時に他のピア ノードに対する要求のプロキシになりますが、ロード バランサーがエントリ レベルの要求の負荷分散をこなします。また、ロード バランサーは、データ制御を強化するために、ファイアウォールで保護されたサンドボックスを作成します。

## <a id="composite"> </a> 複合デプロイ##

複合展開の目的は、仮想マシンのインフラストラクチャ管理によるオーバーヘッドを減らすために仮想マシンの規模を極限まで抑えて、PaaS の使用効率を最大化することです。サーバー管理にはオーバーヘッドがあるため、市場投入までの時間、不明なソース コード、OS に対する低水準アクセスなどのさまざまな理由により簡単には修正できず、ステートフルな動作を必要とするコンポーネントだけを展開します。

![複合デプロイ図](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux1.png)

##<a id="deployment"> </a>Azure の仮想マシンの展開##

![仮想マシンのデプロイ](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux2.png)

前に示している図では、Thrift トラフィックを許すように構成されたロード バランサーの背後で、4 ノードの Cassandra クラスターが仮想マシン内に展開されています。Azure でホストする PaaS アプリケーションは、言語固有の Thrift ライブラリを使用してクラスターにアクセスします。Java、C#、Node.js、Python、C++ などの言語用のライブラリがあります。2 つ目の図に示されている自立的仮想マシン展開では、仮想マシンでホストされた別のクラウド サービス内部で実行されているアプリケーションによってデータが利用されます。

##<a id="task1"> </a>タスク 1: Linux クラスターの展開##

仮想マシンのプレビュー リリース中に、Linux VM が同じ仮想ネットワークの一部になるためには、すべての仮想マシンを同じクラウド サービスに展開する必要があります。クラスター作成の一般的な流れは次のようになります。

![クラスター作成の流れ図](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux4.png)

**手順 1. SSH キー ペアの生成**

Azure では、プロビジョニング時に PEM または DER でエンコードされた X509 公開キーが必要です。「[Azure 上の Linux における SSH の使用方法](http://www.windowsazure.com/ja-jp/manage/linux/how-to-guides/ssh-into-linux/)」の指示に従って公開キーと秘密キーのペアを生成します。Windows または Linux で SSH クライアントとして putty.exe を使用する場合は、puttygen.exe を使用して、PEM でエンコードした RSA 秘密キーを PPK 形式に変換する必要があります。この手順については、「[Generating SSH Key Pair for Linux VM Deployment on Azure (Azure 上の Linux VM 展開用の SSH キー ペアの生成)](http://blogs.msdn.com/b/hanuk/archive/2012/06/07/generating-ssh-key-pair-for-linux-vm-deployment-on-windows-azure.aspx)」を参照してください。

**手順 2. Ubuntu VM の作成**

最初の Ubuntu VM を作成するには、Azure プレビュー ポータルにログインして、**[新規]**、**[仮想マシン]**、**[ギャラリーから]**、**[Unbuntu Server 12.xx]** の順にクリックして、右矢印をクリックします。Linux VM の作成方法を説明したチュートリアルについては、「[Linux を実行する仮想マシンの作成](http://www.windowsazure.com/ja-jp/manage/linux/tutorials/virtual-machine-from-gallery/)」を参照してください。

次に、[仮想マシンの構成] 画面で、次の情報を入力します。

<table>
	<tr>
		<th>フィールド名</th>
		<th>フィールド値</th>
		<th>解説</th>
	</tr>
	<tr>
		<td>仮想マシン名</td>
		<td>hk-cas1</td>
		<td>これは、仮想マシンのホスト名です。</td>
	</tr>
	<tr>
		<td>新しいユーザー名</td>
		<td>localadmin</td>
		<td>"admin" は Ubuntu 12.xx の予約ユーザー名です。</td>
	</tr>
	<tr>
		<td>新しいパスワード</td>
		<td><i>強力なパスワード</i></td>
		<td></td>
	</tr>
	<tr>
		<td>パスワードの確認</td>
		<td><i>強力なパスワード</i></td>
		<td></td>
	</tr>
	<tr>
		<td>サイズ</td>
		<td>S</td>
		<td>IO のニーズに応じて仮想マシンを選択します。</td>
	</tr>
	<tr>
		<td>認証に SSH キーを使用してセキュリティを確保する</td>
		<td>チェック ボックスをオンにします</td>
		<td>SSH キーによるセキュリティが必要な場合にオンにします。</td>
	</tr>
	<tr>
		<td>証明書</td>
		<td><i>公開キー証明書のファイル名</i></td>
		<td>OpenSSL などのツールを使って生成された DER または PEM でエンコードされた SSH 公開キー。</td>
	</tr>
</table>

[VM モード] 画面で、次の情報を入力します。


<table>
	<tr>
		<th>フィールド名</th>
		<th>フィールド値</th>
		<th>解説</th>
	</tr>
	<tr>
		<td>スタンドアロンの仮想マシン</td>
		<td>ラジオ ボタンをクリックします。</td>
		<td>これは最初の仮想マシンの場合です。それ以後の仮想マシンでは、[既存の仮想マシンに接続する] をクリックします。</td>
	</tr>
	<tr>
		<td>DNS 名</td>
		<td><i>独自の名前</i>.cloudapp.net</td>
		<td>マシンとは独立したロード バランサー名を指定します。</td>
	</tr>
	<tr>
		<td>ストレージ アカウント</td>
		<td><i>既定のストレージ アカウント</i></td>
		<td>作成した既定のストレージ アカウントを使用します。</td>
	</tr>
	<tr>
		<td>リージョン/アフィニティ グループ/仮想ネットワーク</td>
		<td>米国西部</td>
		<td>Cassandra クラスターにアクセスする Web アプリケーションのリージョンを選択します。</td>
	</tr>
</table>

Cassandra クラスターに属するすべての仮想マシンについて、前に示している手順を繰り返します。この時点では、すべての仮想マシンが同じネットワークに属し、お互いに Ping を送信できます。Ping が機能しない場合は、仮想マシンのファイアウォールの構成 (iptables など) をチェックして、ICMP が許可されていることを確認します。ネットワーク接続のテストに成功したら、攻撃手段を減らすために ICMP は無効にしてください。

**手順 3. 負荷分散された Thrift エンドポイントの追加**

手順 1. および手順 2. を終えると、各 VM には既に SSH エンドポイントが定義されています。ここで、パブリック ポートが 9160 で負荷分散された Thrift エンドポイントを追加します。手順は次のとおりです。

a.	最初の VM の詳細ビューで、[エンドポイントの追加] をクリックします。

b.	[仮想マシンにエンドポイントを追加します] 画面で、[エンドポイントの追加] をクリックします。

c.	右矢印をクリックします。

d.	[エンドポイントの詳細を指定します] 画面で、次の情報を入力します。<table>
	<tr>
		<th>フィールド名</th>
		<th>フィールド値</th>
		<th>解説</th>
	</tr>
	<tr>
		<td>名前</td>
		<td>cassandra</td>
		<td>重複しなければどのような名前でもかまいません。</td>
	</tr>
	<tr>
		<td>プロトコル</td>
		<td>TCP</td>
		<td></td>
	</tr>
	<tr>
		<td>パブリック ポート</td>
		<td>9160</td>
		<td>既定の Thrift ポートです。</td>
	</tr>
	<tr>
		<td>プライベート ポート</td>
		<td>9160</td>
		<td>cassandra.yaml でこれを変更していない場合。</td>
	</tr>
</table>
前に示している設定を終えると、最初の VM では、cassandra エンドポイントの [負荷分散] が [いいえ] と表示されます。当面、これは無視してください。このエンドポイントを以降の VM に追加すると [はい] に変わります。

e.	2 つ目の VM を選択し、前に示している手順を繰り返してエンドポイントを追加します。少しだけ違うのは、[既存のエンドポイントのトラフィックを負荷分散します] を選択し、ドロップダウン ボックスの一覧の [cassandra-960] を選択することです。この段階で、両方の VM にマッピングされたエンドポイントは、[負荷分散] の状態が [いいえ] から [はい] に変わります。

クラスターの以降のノードについて、"e" の手順を繰り返します。

これで VM の準備が完了しました。次は各 VM で Cassandra をセットアップします。Cassandra は多くの Linux ディストリビューションで標準パッケージではないため、手作業で展開します。

[各 VM に対するソフトウェアのインストールは手作業で行います。ただし、この作業は、完全に機能する Cassandra VM をセットアップして、それを基本イメージとしてキャプチャし、この基本イメージから残りのインスタンスを作成することで、時間を短縮することができます。Linux イメージのキャプチャ方法については、「[Linux を実行する仮想マシンのイメージをキャプチャする方法](https://www.windowsazure.com/ja-jp/manage/linux/how-to-guides/capture-an-image/)]」を参照してください。

##<a id="task2"> </a>タスク 2: 各仮想マシンでの Cassandra のセットアップ##

**手順 1. 前提条件のインストール**

Cassandra には Java 仮想マシンが必要であり、そのため、Ubuntu を含む Debian 系 Linux では、次のコマンドを使用して最新の JRE をインストールする必要があります。         
         
	sudo add-apt-repository ppa:webupd8team/java
    sudo apt-get update
    sudo apt-get install oracle-java7-installer

**手順 2. インストールした Cassandra のテスト**

1.	SSH を使用して Linux (Ubuntu) VM インスタンスにログインします。

2.	wget を使用して、ダウンロード ページ (http://cassandra.apache.org/download/)[http://cassandra.apache.org/download/] に記載されたミラーから Cassandra を ~/downloads ディレクトリに apache-cassandra-bin.tar.gz としてダウンロードします。処理がバージョンに左右されないように、ダウンロードしたファイルの名前にはバージョン番号を含めていないことに注意してください。

3.	次のコマンドを実行して、tar ball を既定のログイン ディレクトリに展開します。
	
		tar -zxvf downloads/apache-cassandra-bin.tar.gz
このコマンドは、アーカイブを apache-cassandra- [version] ディレクトリに展開します。

4. ログとデータを保持する次の 2 つの既定ディレクトリを作成します。

		$ sudo chown -R /var/lib/cassandra
		$ sudo chown -R /var/log/cassandra
5.	Cassandra を実行するユーザー ID に書き込みアクセス許可を付与します。	

		a.	sudo chown -R <user>:<group> /var/lib/cassandra
		b.	sudo chown -R <user>:<group> /var/log/cassandra
		現在のユーザー コンテキストを使用するには、<user> と <group> を $USER と $GROUP に置き換えます。
6.	次のコマンドを使用して、apache-cassandra-[version]/bin ディレクトリから Cassandra を開始します。

		$ ./cassandra

このコマンドは、Cassandra ノードをバックグラウンド プロセスとして開始します。-cassandra "f" を使用すると、プロセスがフォアグラウンド モードで開始されます。

ログに mx4j エラーが記録される場合があります。Cassandra は mx4j がなくても問題なく動作しますが、Cassandra のインストールを管理するには mx4j が必要です。次の手順に進む前に、Cassandra プロセスを終了してください。

**手順 3. mx4j のインストール**

	a)	mx4j のダウンロード: wget [http://sourceforge.net/projects/mx4j/files/MX4J%20Binary/3.0.2/mx4j-3.0.2.tar.gz/download](http://sourceforge.net/projects/mx4j/files/MX4J%20Binary/3.0.2/mx4j-3.0.2.tar.gz/download) -O mx4j.tar.gz
	b)	tar -zxvf mx4j.tar.gz
	c)	cp mx4j-23.0.2/lib/*.jar ~/apache-cassandra-<version>/lib
	d)	rm -rf mx4j-23.0.2
	e)	rm mx4j.tar.gz
この段階で Cassandra プロセスを再起動します。

**手順 4. インストールした Cassandra のテスト**

Cassandra の bin ディレクトリから次のコマンドを実行して、thrift クライアントで接続します。

	cassandra-cli -h localhost -p 9160

**手順 5. Cassandra の外部接続の有効化**

既定では、Cassandra はループバック アドレスをリッスンするようにセットアップされているだけです。したがって外部接続をするにはこれの変更が必須になります。

"conf/cassandra.yaml" を編集して、**listen_address** および **rpc_address** をサーバーの IP アドレスまたはホスト名に変更し、現在のノードが外部のロード バランサーに加えて他のノードにも見えるようにします。

クラスターのすべてのノードについて、手順 1. から手順 5. までを繰り返します。

これで、個々の VM すべてに必要なソフトウェアが用意されたため、今度はシードを構成してノード間の通信を確立します。マルチノード クラスター構成の詳細については、[http://wiki.apache.org/cassandra/MultinodeCluster](http://wiki.apache.org/cassandra/MultinodeCluster) にある情報を参照してください。

**手順 6. マルチノード クラスターのセットアップ**

cassandra.yaml を編集して、すべての VM について次のプロパティを変更します。

**a)	cluster_name**

既定のクラスター名は "Test Cluster" に設定されています。これをアプリケーションを反映した名前に変更します。たとえば "AppStore" になります。インストール時にテスト用として既にクラスターを "Test Cluster" という名前で開始した場合は、クラスター名が一致しないというエラーになります。このエラーを修正するには、/var/lib/cassandra/data/system ディレクトリにあるファイルをすべて削除します。

**b)	seeds**

ここで指定した IP アドレスは、新しいノードによってリング トポロジについて知るために使用されます。コンマ区切り形式 ("*host1*,*host2*") で、最も信頼できるノードをシードとして設定します。たとえば、"hk-ub1,hk-ub2" になります。

この演習の主眼ではないため、シード サーバーによる既定のトークンをそのまま受け入れます。最適なトークン生成については、python スクリプト
([http://wiki.apache.org/cassandra/GettingStarted](http://wiki.apache.org/cassandra/GettingStarted)) を参照してください。

すべてのノードで Cassandra を再起動して、上記の変更を適用します。

**手順 7. マルチノード クラスターのテスト**

Cassandra の bin ディレクトリにインストールされた nodetool はクラスター操作に役立ちます。nodetool を使用してクラスターのセットアップを確認します。次のコマンドを実行します。

	$ bin/nodetool -h <hostname> -p 7199 ring

構成が正しい場合、下に示すような情報が表示されます (3 ノード クラスターの例)。

<table>
	<tr>
		<td>Address</td>
		<td>DC</td>
		<td>Rack</td>
		<td>ステータス</td>
		<td>State</td>
		<td>Load</td>
		<td>Owns</td>
		<td>Token</td>
	</tr>
	<tr>
		<td></td>
		<td></td>	
		<td></td>	
		<td></td>	
		<td></td>	
		<td></td>	
		<td></td>	
		<td>149463697837832744402916220269706844972</td>	
	</tr>
	<tr>
		<td>10.26.196.68</td>
		<td>datacenter1</td>	
		<td>rack1</td>	
		<td>Up</td>	
		<td>Normal</td>	
		<td>15.69 KB</td>	
		<td>25.98%</td>	
		<td>114445918355431753244435008039926455424</td>	
	</tr>
	<tr>
		<td>10.26.198.81</td>
		<td>datacenter1</td>	
		<td>rack1</td>	
		<td>Up</td>	
		<td>Normal</td>	
		<td>15.69 KB</td>	
		<td>53.44%</td>	
		<td>70239176883275351288292106998553981501</td>	
	</tr>
	<tr>
		<td>10.26.198.84</td>
		<td>datacenter1</td>	
		<td>rack1</td>	
		<td>Up</td>	
		<td>Normal</td>	
		<td>18.35 KB</td>	
		<td>25.98%</td>	
		<td>149463697837832744402916220269706844972</td>	
	</tr>
</table>

この段階で、クラスターは、「Linux クラスターの展開」タスクで作成したクラウド サービス URL (最初の VM の作成時に指定した DNS 名) を介して Thrift クライアントが使える状態になっています。

##<a id="task3"> </a>タスク 3: Node.js からの Cassandra クラスターへのアクセス##

先のタスクで説明した手順に従って Azure に Linux VM を 1 つ作成します。Cassandra クラスターにアクセスするクライアントとして使用するため、この VM はスタンドアロン VM にしてください。この VM から Cassandra クラスターに接続する前に、GitHub から Node.js、NPM および [cassandra-client](https://github.com/racker/node-cassandra-client) をインストールします。

**手順 1. Node.js と NPM のインストール**

a)	前提条件をインストールします。

	sudo apt-get install g++ libssl-dev apache2-utils make
b)	GitHub にあるソースをコンパイルしてインストールします。リポジトリを複製する前に、git コア ランタイムをインストールする必要があります。

	sudo apt-get install git-core
c)	Node リポジトリを複製します。

	git clone git://github.com/joyent/node.git
d)	このコマンドは "node" という名前のディレクトリを作成します。次のコマンドを実行して、node.js をコンパイルしてインストールします。

	cd node
	./configure
	make
	sudo make install

e)	次のコマンドを実行して、安定版バイナリから NPM をインストールします。

	curl http://npmjs.org/install.sh | sh

**手順 2. cassandra-client パッケージのインストール**

	npm cassandra-client 

**手順 3. Cassandra ストレージの準備**

Cassandra ストレージでは、KEYSPACE および COLUMNFAMILY という概念が使用されます。これはおおよそ RDBMS 用語の DATABASE 構造および TABLE 構造に相当します。KEYSAPCE は 1 組の COLUMNFAMILY 定義を含みます。各 COLUMNFAMILY は 1 組の行を含み、各行はいくつかの列を含みます (次の図を参照)。

![行と列](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux3.png)

先に展開した Cassandra クラスターを使用して、このデータ構造を作成し照会することで、node.js アクセスの方法を示します。顧客データを保存するためにクラスターの基本的な準備を実行する単純な node.js スクリプトを作成します。スクリプトで示した技法は、node.js Web アプリケーションまたは Web サービスでも容易に利用できます。コード断片はしくみを示すことだけを目的としており、実際のソリューションで使用する場合、コードには改善の余地 (セキュリティ、ログ、拡張性など) が大いにあることに留意してください。

スクリプトのスコープで必要な変数を定義し、cassandra-client モジュールから PooledConnection をインクルードし、よく使用するキー スペース名およびキー スペース接続パラメーターを定義します。

	casdemo.js: 
	var pooledCon = require('cassandra-client').PooledConnection;
	var ksName = "custsupport_ks";
	var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
	                     keyspace: ksName, use_bigints: false };

顧客データを保存する準備として、まず次のスクリプト例を使用して KEYSPACE を作成する必要があります。

	casdemo.js: 
	function createKeyspace(callback){
	   var cql = 'CREATE KEYSPACE ' + ksName + ' WITH 
	   strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
	   var sysConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'],  
	                         keyspace: 'system', use_bigints: false };
	   var con = new pooledCon(sysConOptions);
	   con.execute(cql,[],function(err) {
	   if (err) {
	     console.log("Failed to create Keyspace: " + ksName);
	     console.log(err);
	   }
	   else {
	     console.log("Created Keyspace: " + ksName);
	     callback(ksConOptions, populateCustomerData);
	   }
	   });
	   con.shutdown();
	} 
	
createKeysapce 関数は、引数としてコールバック関数をとります。これは、KEYSPACE が列ファミリ作成の前提条件であり、関数内で COLUMNFAMILY 作成関数を実行するためです。アプリケーションの KEYSPACE 定義のために "system" KEYSPACE に接続する必要があることに注意してください。コード断片では、クラスターとのやり取りのために [Cassandra Query Language (CQL)](http://cassandra.apache.org/doc/cql/CQL.html) が一貫して使用されています。前に示しているスクリプトで作成した CQL にはパラメーター マーカーがないため、PooledConnection.execute() メソッドでは空のパラメーター コレクション ("[]") を使用しています。

キー スペースが正常に作成されると、次に示している createColumnFamily() 関数が実行されて、必要な COLUMNFAMILY 定義が作成されます。

	casdemo.js: 
	//Creates COLUMNFAMILY
	function createColumnFamily(ksConOptions, callback){
	  var params = ['customers_cf','custid','varint','custname',
	                'text','custaddress','text'];
	  var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
	var con =  new pooledCon(ksConOptions);
	  con.execute(cql,params,function(err) {
	      if (err) {
	         console.log("Failed to create column family: " + params[0]);
	         console.log(err);
	      }
	      else {
	         console.log("Created column family: " + params[0]);
	         callback();
	      }
	  });
	  con.shutdown();
	} 

パラメーター化された CQL テンプレートを params オブジェクトと組み合わせて、COLUMNFAMILY 作成用の有効な CQL を生成します。COLUMNFAMILY が正常に作成されると、指定されたコールバック、この場合は populateCustomerData() が非同期コール チェーンの一部として呼び出されます。

	casdemo.js: 
	//populate Data
	function populateCustomerData() {
	   var params = ['John','Infinity Dr, TX', 1];
	   updateCustomer(ksConOptions,params);
	
	   params = ['Tom','Fermat Ln, WA', 2];
	   updateCustomer(ksConOptions,params);
	}
	
	//update will also insert the record if none exists
	function updateCustomer(ksConOptions,params)
	{
	  var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where 
	             custid=?';
	  var con = new pooledCon(ksConOptions);
	  con.execute(cql,params,function(err) {
	      if (err) console.log(err);
	      else console.log("Inserted customer : " + params[0]);
	  });
	  con.shutdown();
	}

populateCustomerData() は、COLUMNFAMILY つまり customers_cf に 2 行挿入します。Cassandra Query Language では、UPDATE 操作時にレコードが既に存在しない場合、レコードが挿入されるため、INSERT CQL ステートメントは冗長です。

ここまでで、createKeyspace()、createColumnFamily()、populateCustomerData() と続くコールバック チェーンを作成しました。今度は、次のコード断片を使ってコードを実行します。

	casdemo.js:
	var pooledCon = require('cassandra-client').PooledConnection;
	var ksName = "custsupport_ks";
	var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
	                     keyspace: ksName, use_bigints: false };
	
	createKeyspace(createColumnFamily);
	//rest of the not shown

シェル プロンプトから次のコマンドを実行して、スクリプトを実行します。

	//the following command will create the KEYSPACE, COLUMNFAMILY and //inserts two customer records
	$ node casdemo.js

readCustomer() メソッドは Azure でホストされたクラスターにアクセスして、CQL クエリの実行後に取得した JSON 断片を表示します。

	casdemo.js: 
	//read the two rows inserted above
	function readCustomer(ksConOptions)
	{
	  var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
	  var con = new pooledCon(ksConOptions);
	  con.execute(cql,[],function(err,rows) {
	      if (err) 
	         console.log(err);
	      else 
	         for (var i=0; i<rows.length; i++)
	            console.log(JSON.stringify(rows[i]));
	    });
	   con.shutdown();
	} 

下に示したように casdemo.js を変更して、上の関数を追加し、先に呼び出した createKeyspace() メソッドをコメントにして、上の関数を呼び出します。

	casdemo.js: 
	var pooledCon = require('cassandra-client').PooledConnection;
	var ksName = "custsupport_ks";
	var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
	                     keyspace: ksName, use_bigints: false };
	
	//createKeyspace(createColumnFamily);
	readCustomer(ksConOptions)
	//rest of the code below not shown
		
##<a id="conclusion"> </a>まとめ##

Azure の仮想マシン機能を利用すると、Linux (Microsoft パートナーの提供するイメージ) および Windows 仮想マシンを作成して、既存のサーバー製品およびアプリケーションを一切変更することなく移行できます。この記事で取り上げた Cassandra NoSQL データベース サーバーはその一例です。この記事でセットアップした Cassandra クラスターは、Azure でホストしているクラウド サービス、サード パーティのパブリック クラウドおよびプライベート クラウドで Windows OS 環境および Linux OS 環境の両方からアクセスできます。この記事では、クライアントとして node.js を使用しましたが、Cassandra は .NET や Java などの言語環境からもアクセスできます。

[概要]: #overview
[Cassandra の展開方式]: #schematic
[複合展開]: #composite
[仮想マシンの展開]: #deployment
[タスク 1: Linux クラスターの展開]: #task1
[タスク 2: 各仮想マシンでの Cassandra のセットアップ]: #task2
[タスク 3: Node.js からの Cassandra クラスターへのアクセス]: #task3
[まとめ]: #conclusion




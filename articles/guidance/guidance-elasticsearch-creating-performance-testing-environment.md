<properties
   pageTitle="Elasticsearch のパフォーマンス テスト環境の作成 | Microsoft Azure"
   description="Elasticsearch クラスターのパフォーマンスをテストするための環境を設定する方法について説明します。"
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Azure での Elasticsearch のパフォーマンス テスト環境の作成

これは[一連の記事の一部](guidance-elasticsearch.md)です。

このドキュメントでは、Elasticsearch クラスターのパフォーマンスをテストするための環境を設定する方法について説明します。この構成は、「[Tuning Data Ingestion Performance for Elasticsearch on Azure][]」で説明するデータ取り込みおよびクエリ ワークロードのパフォーマンスのテストに使用されました。

パフォーマンス テストのプロセスでは、テスト用に構成された (Elasticsearch クラスターに属していない) 一連の専用 VM を使用するマスター/下位構成にプラグインの [Standard Set](http://jmeter-plugins.org/wiki/StandardSet/) をインストールして、[Apache JMeter](http://jmeter.apache.org/) を使用しました。

[PerfMon Server Agent](http://jmeter-plugins.org/wiki/PerfMonAgent/) を各 Elasticsearch ノードにインストールしました。以下のセクションでは、JMeter で独自のパフォーマンス テストを実施できるように、テスト環境を再作成する手順について説明します。これらの手順は、Azure 仮想ネットワークを使用して接続されたノードを含む Elasticsearch クラスターを既に作成していることを前提としています。

また、このテスト環境は、単一の Azure リソース グループを使用して管理された一連の Azure VM としても実行されます。

Elasticsearch クラスターの内部の状況をより簡単に監視および分析できるように、[Marvel](https://www.elastic.co/products/marvel) もインストールして構成しました。JMeter の統計がパフォーマンスのピークまたは谷間を示していた場合、変動の原因を特定する際に Marvel から入手できる情報が非常に役立つ場合があります。

次の図は、システム全体の構造を示しています。

![elasticsearch-arch](./media/guidance-elasticsearch/performance-structure.png)

次の点に注意してください。

- JMeter マスター VM では、JMeter コンソールの GUI 環境を提供するために Windows Server を実行します。JMeter マスター VM は、テスト担当者がテストの作成、テストの実行、結果の表示に使用できる GUI (*jmeter* アプリケーション) を提供します。この VM は、テストを構成する要求を実際に送信する JMeter サーバー VM と連携します。

- JMeter 下位 VM では、Ubuntu Server (Linux) を実行します。これらの VM には GUI は不要です。JMeter サーバー VM では、Elasticsearch クラスターに要求を送信するために JMeter Server ソフトウェア (*jmeter server* アプリケーション) を実行します。

- 専用のマスター ノードは使用しましたが、専用のクライアント ノードは使用しませんでした。

- クラスターのデータ ノードの数は、テスト対象となるシナリオによって異なることがあります。

- Elasticsearch クラスターのすべてのノードで、実行時のパフォーマンスを監視する Marvel と、後で分析するための監視データを収集する JMeter Server Agent を実行します。

- Elasticsearch 2.0.0 以降をテストするときは、データ ノードのいずれかで Kibana も実行します。これは、Elasticsearch 2.0.0 以降で実行される Marvel のバージョンで必要となります。

## 仮想マシンの Azure リソース グループの作成

JMeter マスターは、パフォーマンス データを収集するために、Elasticsearch クラスターの各ノードに直接接続できる必要があります。JMeter の VNet が Elasticsearch クラスターの VNet と異なる場合は、パブリック IP アドレスを使用して各 Elasticsearch ノードを構成する必要があります。Elasticsearch の構成でこの点が問題になる場合は、同じリソース グループを使用して Elasticsearch クラスターと同じ VNet に JMeter VM を実装することを検討してください。その場合、この最初の手順は省略できます。

まず、[リソース グループを作成](../articles/resource-group-portal/#create-resource-group-and-resources)します。このドキュメントでは、リソース グループの名前を *JMeterPerformanceTest* と想定しています。Elasticsearch クラスターと同じ VNet で JMeter VM を実行する場合は、新しいリスース グループを作成するのではなく、そのクラスターと同じリソース グループを使用します。

## JMeter マスター仮想マシンの作成

次に、*Windows Server 2008 R2 SP1* イメージを使用して、[Windows VM を作成](../articles/virtual-machines-windows-tutorial/)します。パフォーマンス テストを実行できる十分なコア数とメモリを提供する VM サイズを選択することをお勧めします。2 コア以上で 3.5 GB 以上の RAM (A2 Standard 以上) のマシンが理想的です。

<!-- TODO add info on why disabling diagnostics is positive -->

診断を無効にすることをお勧めします。ポータルで VM を作成するときに、*[設定]* ブレードの *[診断]* の *[監視]* セクションで診断を無効にします。その他の設定は、既定値のままにしておきます。

ポータルで[リソース グループを調べて](../articles/resource-group-portal/#browse-resource-groups)、VM とすべての関連リソースが正常に作成されていることを確認します。一覧に表示されるリソースは、VM、ネットワーク セキュリティ グループ、パブリック IP アドレス (すべて同じ名前)、および VM の名前に基づく名前が付けられたネットワーク インターフェイスとストレージ アカウントで構成されます。

## JMeter 下位仮想マシンの作成

次に、*Ubuntu Server 14.04 LTS* イメージを使用して、[Linux VM を作成](../articles/virtual-machines-linux-tutorial-portal-rm/)します。JMeter マスター VM と同様に、パフォーマンス テストを実行できる十分なコア数とメモリを提供する VM サイズを選択します。2 コア以上で 3.5 GB 以上の RAM (A2 Standard 以上) のマシンが理想的です。

この場合も、診断を無効にすることをお勧めします。

下位 VM は必要な数だけ作成できます。

## JMeter 下位 VM への JMeter Server のインストール

JMeter 下位 VM は Linux を実行しており、既定では、リモート デスクトップ接続 (RDP) を開いて下位 VM に接続することはできません。代わりに、各 VM で [PuTTY を使用してコマンド ライン ウィンドウを開く](../articles/virtual-machines-linux-how-to-log-on/)ことができます。

下位 VM のいずれかに接続したら、bash を使用して JMeter をセットアップします。

まず、JMeter を実行するために必要な Java ランタイム環境をインストールします。

```bash
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

次に、zip ファイルとしてパッケージ化された JMeter ソフトウェアをダウンロードします。

```bash
wget http://apache.mirror.anlx.net/jmeter/binaries/apache-jmeter-2.13.zip
```

unzip コマンドをインストールし、このコマンドを使用して JMeter ソフトウェアを展開します。ソフトウェアは **apache-jmeter-2.13** という名前のフォルダーにコピーされます。

```bash
sudo apt-get install unzip
unzip apache-jmeter-2.13.zip
```

JMeter の実行可能ファイルを保持する *bin* ディレクトリに変更し、*jmeter-server* プログラムと *jmeter* プログラムを実行可能にします。

```bash
cd apache-jmeter-2.13/bin
chmod u+x jmeter-server
chmod u+x jmeter
```

次に、現在のフォルダーにある `jmeter.properties` ファイルを編集する必要があります (*vi* や *vim* など、使い慣れたテキスト エディターを使用します)。次の行を見つけます。

```yaml
...
client.rmi.localport=0
...
server.rmi.localport=4000
...
```

これらの行をコメント解除し (先頭の ## 文字を削除)、次のように変更したら、ファイルを保存してエディターを閉じます。

```yaml
...
client.rmi.localport=4441
...
server.rmi.localport=4440
```

次のコマンドを実行して、着信 TCP トラフィックのポート 4441 を開きます (先ほど、このポートでリッスンするように *jmeter-server* を構成しました)。

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4441 -j ACCEPT
```

JMeter のプラグインの標準コレクションが含まれた zip ファイルをダウンロードし、ファイルを解凍します (これらのプラグインは、パフォーマンス監視カウンターを提供します)。この場所でファイルを解凍すると、適切なフォルダーにプラグインが配置されます。

LICENSE ファイルの置き換えを求められたら、「A」(すべて対象) と入力します。

```bash
wget http://jmeter-plugins.org/downloads/file/JMeterPlugins-Standard-1.3.0.zip
unzip JMeterPlugins-Standard-1.3.0.zip
```

`nohup` を使用して、バックグラウンドで JMeter Server を起動します。このコマンドは、プロセス ID と、リモート オブジェクトが作成され、コマンドの受信を開始する準備ができていることを示すメッセージを表示して応答します。

```bash
nohup bin/jmeter-server &
```

> [AZURE.NOTE] VM をシャットダウンすると、JMeter Server プログラムは終了します。VM に接続し、もう一度手動で再起動する必要があります。別の方法として、`/etc/rc.local` ファイル (*exit 0* コマンドの前) に次のコマンドを追加することで、起動時に *jmeter-server* コマンドを自動的に実行するようにシステムを構成することもできます。

```bash
sudo -u <username> bash << eoc
cd /home/<username>/apache-jmeter-2.13/bin
nohup ./jmeter-server &
eoc
```

`<username>` を実際のログイン名に置き換えます。

テストの実行中に JMeter Server の進行状況を監視できるように、ターミナル ウィンドウを開いたままにしておくと便利です。

JMeter 下位 VM ごとにこれらの手順を繰り返す必要があります。

## Elasticsearch ノードへの JMeter Server Agent のインストール

この手順は、Elasticsearch ノードにログイン アクセスできることを前提としています。ARM テンプレートを使用してクラスターを作成した場合は、「[Elasticsearch トポロジ](guidance-elasticsearch-running-on-azure.md#elasticsearch-topologies)」の図に示すように、ジャンプ ボックス VM を介して各ノードに接続できます。PuTTY を使用してジャンプ ボックスに接続することもできます。

そこから、*ssh* コマンドを使用して Elasticsearch クラスターの各ノードにログインできます。

Elasticsearch ノードのいずれかに管理者としてログインします。bash コマンド プロンプトで次のコマンドを入力して、JMeter Server Agent を保持するためのフォルダーを作成し、そのフォルダーに移動します。

```bash
mkdir server-agent
cd server-agent
```

次のコマンドを実行して *unzip* コマンドをインストールし (まだインストールしていない場合)、JMeter Server Agent ソフトウェアをダウンロードして解凍します。

```bash
sudo apt-get install unzip
wget http://jmeter-plugins.org/downloads/file/ServerAgent-2.2.1.zip
unzip ServerAgent-2.2.1.zip
```
 
次のコマンドを実行してファイアウォールを構成し、TCP トラフィックがポート 4444 を通過できるようにします (これは、JMeter Server Agent が使用するポートです)。

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4444 -j ACCEPT
```

次のコマンドを実行して、JMeter Server Agent をバックグラウンドで起動します。

```bash
nohup ./startAgent.sh &
```

JMeter Server Agent は、エージェントが起動し、ポート 4444 でリッスンしていることを示すメッセージで応答します。Enter キーを押してコマンド プロンプトを取得し、次のコマンドを実行します。`<nodename>` を実際のノードの名前に置き換えます。ノードの名前がわからない場合は、`hostname` コマンドを実行することで確認できます。

```bash
telnet <nodename> 4444
```

このコマンドにより、ローカル マシンでポート 4444 への telnet 接続が開きます。この接続を使用して、JMeter Server Agent が正常に実行されていることを確認できます。

JMeter Server Agent が実行されていない場合、次の応答が返されます。

`*telnet: Unable to connect to remote host: Connection refused*.`

JMeter Server Agent が実行されており、ポート 4444 が正しく構成されている場合は、次の応答が返されます。

![](./media/guidance-elasticsearch/performance-telnet-server.png)

> [AZURE.NOTE] telnet セッションでは、接続後にプロンプトが表示されることはありません。

telnet セッションで、次のコマンドを入力します。

``` 
test
```

JMeter Server Agent が構成され、正常にリッスンしていれば、コマンドを受信したことが示され、*Yep* というメッセージが返されます。

> [AZURE.NOTE] 他のコマンドを入力して、パフォーマンス監視データを取得できます。たとえば、`metric-single:cpu:idle` コマンドを使用すると、CPU がアイドル状態である時間の現在の割合を取得できます (これはスナップショットです)。コマンドの一覧については、「[PerfMon Server Agent](http://jmeter-plugins.org/wiki/PerfMonAgent/)」ページをご覧ください。

telnet セッションで、次のコマンドを入力してセッションを終了し、bash コマンド プロンプトに戻ります。

``` 
exit
```

> [AZURE.NOTE] JMeter 下位 VM と同様に、ログアウトした場合や、このマシンをシャットダウンして再起動した場合は、`startAgent.sh` コマンドを使用して JMeter Server Agent を手動で再起動する必要があります。JMeter Server Agent を自動的に起動する場合は、`/etc/rc.local` ファイルの末尾の *exit 0* コマンドの前に次のコマンドを追加します。`<username>` を実際のログイン名に置き換えます。

```bash
sudo -u <username> bash << eoc
cd /home/<username>/server-agent
nohup ./startAgent.sh &
eoc
```

`<username>` を実際のログイン名に置き換えます。

Elasticsearch クラスターの他のすべてのノードに対して、このプロセス全体を繰り返すことができます。また、次に示すように、`scp` コマンドを使用して server-agent フォルダーとその内容を他のすべてのノードにコピーし、`ssh` コマンドを使用して JMeter Server Agent を起動することもできます。`<username>` を実際のユーザー名に置き換え、`<nodename>` をコピー先のノードの名前に置き換えて、ソフトウェアを実行します (各コマンドを実行するときに、パスワードの入力を求められる場合があります)。

```bash
scp -r ~/server-agent <username>@<nodename>:~
ssh <nodename> sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4444 -j ACCEPT
ssh <nodename> -n -f 'nohup ~/server-agent/startAgent.sh'
```

## JMeter マスター VM での JMeter のインストールと構成

Azure ポータルで、*[リソース グループ]* をクリックします。*[リソース グループ]* ブレードで、JMeter マスター VM と下位 VM が含まれたリソース グループをクリックします。*[リソース グループ]* ブレードで、JMeter マスター VM をクリックします。仮想マシン ブレードで、ツールバーの *[接続]* をクリックします。Web ブラウザーから要求されたら、RDP ファイルを開きます。Windows によって VM へのリモート デスクトップ接続が作成されます。メッセージが表示されたら、VM のユーザー名とパスワードを入力します。

VM で、Internet Explorer を使用して「[Download Java for Windows](http://www.java.com/en/download/ie_manual.jsp)」ページに移動します。指示に従って Java インストーラーをダウンロードし、実行します。

Web ブラウザーで、「[Download Apache JMeter](http://jmeter.apache.org/download_jmeter.cgi)」ページに移動し、最新のバイナリが含まれた zip をダウンロードします。VM 上のアクセスしやすい場所に zip を保存します。

[JMeter のカスタム プラグイン](http://jmeter-plugins.org/)のサイトに移動し、プラグインの Standard Set をダウンロードします。前の手順で JMeter をダウンロードしたフォルダーに zip を保存します。

Windows エクスプローラーで、apache-jmeter-*xx* zip ファイル (*xx* は JMeter の現在のバージョン) があるフォルダーに移動し、現在のフォルダーにファイルを展開します (apache-jmeter-*xxx* というサブフォルダーが作成されます)。

JMeterPlugins-Standard-*yyy*.zip ファイル (*yyy* はプラグインの現在のバージョン) 内のファイルを apache-jmeter-*xxx* フォルダーに展開します。これにより、JMeter の適切なフォルダーにプラグインが追加されます (lib フォルダーを安全にマージできます。メッセージが表示されたら、ライセンス ファイルと readme ファイルを上書きします)。

apache-jmeter-*xxx*/bin フォルダーに移動し、メモ帳を使用して jmeter.properties ファイルを編集します。`jmeter.properties` ファイルで、*Remote hosts and RMI configuration* というラベルが付けられたセクションを見つけます。ファイルのこのセクションで、次の行を見つけます。

```yaml
remote_hosts=127.0.0.1
```

この行の IP アドレス 127.0.0.1 を、各 JMeter 下位サーバーの IP アドレスまたはホスト名のコンマ区切りのリストに置き換えて変更します。次に例を示します。

```yaml
remote_hosts=JMeterSub1,JMeterSub2
```

次の行を見つけ、この行の先頭にある `#` 文字を削除して、client.rmi.localport 設定の値

```yaml
#client.rmi.localport=0
```

を次のように変更します。

```yaml
client.rmi.localport=4440
```

このファイルを保存して、メモ帳を閉じます。

Windows ツールバーで、*[スタート]*、*[管理ツール]* の順にクリックし、*[セキュリティが強化された Windows ファイアウォール]* をクリックします。*[セキュリティが強化された Windows ファイアウォール]* ウィンドウの左側のウィンドウで、*[受信の規則]* を右クリックし、*[新しい規則]* をクリックします。

*新規の受信の規則**ウイザード* の *[規則の種類]* ページで、*[ポート]* を選択し、*[次へ]* をクリックします。*[プロトコルおよびポート]* ページで *[TCP]* を選択します。次に、*[特定のローカル ポート]* を選択し、テキスト ボックスに「`4440-4444`」と入力して、*[次へ]* をクリックします。*[操作]* ページで、*[接続を許可する]* を選択し、*[次へ]* をクリックします。*[プロファイル]* ページでは、すべてのオプションをオンのままにして、*[次へ]* をクリックします。*[名前]* ページで、*[名前]* ボックスに「*JMeter*」と入力し、*[完了]* をクリックします。*[セキュリティが強化された Windows ファイアウォール]* ウィンドウを閉じます。

Windows エクスプローラーで、apache-jmeter-*xx*/bin フォルダーの *jmeter* Windows バッチ ファイルをダブルクリックして GUI を起動します。次のようなユーザー インターフェイスが表示されます。

![](./media/guidance-elasticsearch/performance-image17.png)

メニュー バーの *[Run]* をクリックし、*[Remote Start]* をクリックして、2 つの JMeter 下位マシンが表示されていることを確認します。

![](./media/guidance-elasticsearch/performance-image18.png)

これで、パフォーマンス テストを開始する準備が整いました。

## Marvel のインストールと構成

クラスターの構築時に、MARVEL パラメーターと KIBANA パラメーターを true に設定すると、Azure の Elasticsearch クイックスタート テンプレートによって、Marvel の適切なバージョンが自動的にインストールされ、構成されます。

![](./media/guidance-elasticsearch/performance-image19.png)

Marvel を既存のクラスターに追加する場合は、インストールを手動で実行する必要があります。以下の手順で説明するように、このプロセスは、使用している Elasticsearch のバージョンが 1.7.x と 2.x のどちらであるかによって異なります。

### Elasticsearch 1.73 以前での Marvel のインストール

Elasticsearch 1.7.3 以前を使用している場合は、クラスターの "すべてのノードで" 次の手順を実行します。

- ノードにログインし、Elasticsearch ホーム ディレクトリに移動します。Linux では、通常のホーム ディレクトリは `/usr/share/elasticsearch` です。

-  次のコマンドを実行して、Elasticsearch の Marvel プラグインをダウンロードし、インストールします。

```bash
sudo bin/plugin -i elasticsearch/marvel/latest
```

- ノードで Elasticsearch を停止し、再起動します。

```bash
sudo service elasticsearch restart
```

- Marvel が正しくインストールされていることを確認するには、Web ブラウザーを開き、URL `http://<server>:9200/_plugin/marvel` に移動します。`<server>` を、クラスターの任意の Elasticsearch サーバーの名前または IP アドレスに置き換えます。次のようなページが表示されていることを確認します。

![](./media/guidance-elasticsearch/performance-image20.png)


### Elasticsearch 2.0.0 以降での Marvel のインストール

Elasticsearch 2.0.0 以降を使用している場合は、クラスターの "すべてのノードで" 次の手順を実行します。

ノードにログインし、Elasticsearch ホーム ディレクトリ (通常は `/usr/share/elasticsearch`) に移動します。次のコマンドを実行して、Elasticsearch の Marvel プラグインをダウンロードし、インストールします。

```bash
sudo bin/plugin install license
sudo bin/plugin install marvel-agent
```

ノードで Elasticsearch を停止し、再起動します。

```bash
sudo service elasticsearch restart
```

次の手順では、Elasticsearch 2.0.0 または Elasticsearch 2.0.1 を使用している場合は `<kibana-version>` を 4.2.2 に置き換え、Elasticsearch 2.1.0 以降を使用している場合は 4.3.1 に置き換えます。Elasticsearch 2.0.0 または Elasticsearch 2.0.1 を使用している場合は `<marvel-version>` を 2.0.0 に置き換え、Elasticsearch 2.1.0 以降を使用している場合は 2.1.0 に置き換えます。クラスターの "いずれかのノードで" 次の手順を実行します。

ノードにログインし、[Elasticsearch ダウンロード Web サイト](https://www.elastic.co/downloads/past-releases)から、使用している Elasticsearch のバージョンに適した Kibana のビルドをダウンロードしてパッケージを展開します。

```bash
wget https://download.elastic.co/kibana/kibana/kibana-<kibana-version>-linux-x64.tar.gz
tar xvzf kibana-<kibana-version>-linux-x64.tar.gz
```

ポート 5601 を開いて受信要求を受け入れます。

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 5601 -j ACCEPT
```

kibana config フォルダー (`kibana-<kibana-version>-linux-x64/config`) に移動し、`kibana.yml` ファイルを編集して次の行を追加します。`<server>` を、クラスターの Elasticsearch サーバーの名前または IP アドレスに置き換えます。

```yaml
elasticsearch.url: "http://<server>:9200"
```

kibana bin フォルダー (`kibana-<kibana-version>-linux-x64/bin`) に移動し、次のコマンドを実行して Marvel プラグインを Kibana に統合します。

```bash
sudo ./kibana plugin --install elasticsearch/marvel/<marvel-version>
```

Kibana を起動します。

```bash
sudo nohup ./kibana &
```

Marvel のインストールを確認するには、Web ブラウザーを開き、URL `http://<server>:5601/app/marvel` に移動します。`<server>` を、Kibana を実行しているサーバーの名前または IP アドレスに置き換えます。

次のようなページが表示されていることを確認します (クラスターの名前は、図に示されているものとは異なる可能性があります)。

![](./media/guidance-elasticsearch/performance-image21.png)

クラスターに対応するリンク (上の図では elasticsearch210) をクリックします。次のようなページが表示されます。

![](./media/guidance-elasticsearch/performance-image22.png)

[Tuning Data Ingestion Performance for Elasticsearch on Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md

<!---HONumber=AcomDC_0224_2016-->
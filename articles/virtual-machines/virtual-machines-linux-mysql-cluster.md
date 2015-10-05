<properties
	pageTitle="負荷分散セットを使用して MySQL をクラスター化する |Microsoft Azure"
	description="Azure 上のクラシック デプロイ モデルを使用して作成された負荷分散された可用性の高い Linux MySQL クラスターをセットアップする"
	services="virtual-machines"
	documentationCenter=""
	authors="bureado"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/14/2015"
	ms.author="jparrel"/>

# 負荷分散セットを使用して Linux の MySQL をクラスター化する

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]この記事では、クラシック デプロイメント モデルを使用したリソースの作成について説明します。

Linux ベースの高可用性サービスを Microsoft Azure 上にデプロイするには、さまざまな方法が利用できます。この記事の目的はそれぞれの方法を詳細に説明することですが、MySQL サーバーの高可用性についての初歩的な知識も説明します。この記事の内容を紹介するビデオが [Channel 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL) で視聴できます。

ここでは、DRBD、Corosync、Pacemaker をベースとする、シェアード ナッシング、2 ノード、1 マスターの MySQL 高可用性ソリューションについて概説します。MySQL を同時に実行しているノードは 1 つだけです。また、DRBD リソースに対して同時に読み書きできるノードも 1 つに制限されます。

Microsoft Azure の負荷分散セットを使用すると、ラウンド ロビン、エンドポイント検出、VIP の削除と適切な回復などの機能が提供されるため、LVS のような VIP ソリューションは必要ありません。VIP はグローバルにルーティング可能な IPv4 アドレスで、最初にクラウド サービスを作成するときに、Microsoft Azure によって割り当てられます。

他にも NBD Cluster、Percona、Galera などの MySQL 用アーキテクチャやいくつかのミドルウェア ソリューション (VM として利用できるものが [VM Depot](http://vmdepot.msopentech.com) に 1 つ以上掲載されています) が利用できる可能性があります。これらのソリューションが、(マルチキャストまたはブロードキャストではなく) ユニキャストでのレプリケーションが可能で、共有ストレージや複数のネットワーク インターフェイスに依拠しないものであれば、Microsoft Azure 上で簡単にデプロイできます。

もちろん、これらのクラスタリング アーキテクチャは、他の PostgreSQL や OpenLDAP などの製品にも同様の方法で拡張できます。たとえば、ここで挙げたシェアード ナッシング方式の負荷分散手順は、マルチマスターの OpenLDAP でのテストに成功しています。その内容は、マイクロソフトの Channel 9 ブログで見ることができます。

## 準備

作業に入る前に、少なくとも 2 つの VM (この例では、XS を使用)、1 つのネットワーク、1 つのサブネット、1 つのアフィニティ グループ、1 つの可用性セットを作成できるサブスクリプションを取得している Microsoft Azure のアカウントが必要です。また、クラウド サービスと同じリージョンに新しい VHD を作成し、それを Linux の VM に接続できるようにしておくことも必要です。

### テスト環境

- Ubuntu 13.10
  - DRBD
  - MySQL サーバー
  - Corosync および Pacemaker

### アフィニティ グループ

Azure ポータルにログインし、下にスクロールして、[設定] でこのソリューション用の新しいアフィニティ グループを作成します。後で作成したリソースは、このアフィニティ グループに割り当てられます。

### ネットワーク

ネットワークを作成し、その中にサブネットを作成します。ここでは、中に /24 のサブネットを 1 つだけ含む 10.10.10.0/24 ネットワークを選択します。

### 仮想マシン

ギャラリーの動作保証済み Ubuntu イメージを使用して最初の Ubuntu 13.10 の VM を作成し、`hadb01` と名付けます。このとき、hadb という名前の新しいクラウド サービスを作成します。このような名前にしたのは、リソースを追加するときにこのサービスが持つ共有、負荷分散という性質を表すためです。ポータルを使用すると、`hadb01` は簡単に作成できます。SSH エンドポイントは自動的に作成されます。ネットワークは、先ほど作成したネットワークを選択します。VM 用の新しい可用性セットの作成も選択します。

1 つ目の VM を作成したら (厳密に言えば、クラウド サービスを作成したら)、`hadb02` という名前の 2 つ目の VM を作成します。2 つ目の VM も、ポータルのギャラリーのイメージを使用して Ubuntu 13.10 の VM を作成します。ただし、新しいクラウド サービスを作成するのではなく、先ほど作成したクラウド サービス `hadb.cloudapp.net` を選択して使用します。ネットワークと可用性セットは自動的に選択されます。SSH エンドポイントも作成されます。

2 つの VM の作成が完了したら、`hadb01` (TCP 22) および `hadb02` の SSH ポート (Azure が自動的に割り当てます) をメモします。

### ストレージの接続

両方の VM に新しいディスクを接続し、それぞれに新しい 5 GB のディスクを作成します。これらのディスクは、オペレーティング システムのメイン ディスクとして使用されている VHD コンテナーでホストされます。ディスクを作成して接続すると、カーネルが新しいデバイスを認識する (通常は `/dev/sdc` で、`dmesg` で出力をチェックできます) ため、Linux を再起動する必要はありません。

さらに、それぞれの VM で、`cfdisk` (プライマリ、Linux パーティション) を使用して新しいパーティションを作成し、新しいパーティション テーブルを書き込みます。**このパーティションには、ファイルシステムを作成しないでください。**

## クラスターのセットアップ

両方の Ubuntu VM に Corosync、Pacemaker、および DRBD をインストールするには、APT を使用する必要があります。`apt-get` コマンドを使用します。

    sudo apt-get install corosync pacemaker drbd8-utils.

**この時点では MySQL をインストールしないでください。**Debian および Ubuntu のインストール スクリプトは、`/var/lib/mysql` の MySQL のデータ ディレクトリを初期化します。しかし、このディレクトリは DRBD のファイルシステムに置き換わるため、MySQL は後でインストールする必要があります。

この時点で、両 VM とも 10.10.10.0/24 サブネットのアドレスを使用していること、および、相互に名前で Ping を送信できることを、(`/sbin/ifconfig` を使用して) 確認する必要もあります。また、必要に応じて、`ssh-keygen` および `ssh-copy-id` を使用して、両 VM がパスワードを求められることなく SSH 経由で通信できることを確認します。

### DRBD のセットアップ

基盤の `/dev/sdc1` パーティションを使用する DRBD リソースを作成して、`/dev/drbd1` リソースを作成します。このリソースは、ext3 を使用することによりフォーマット可能で、プライマリ ノードとしてもセカンダリ ノードとしても使用できます。このリソースを作成するには、`/etc/drbd.d/r0.res` を開き、次のリソース定義をコピーします。両方の VM でこの操作を行います。

    resource r0 {
      on `hadb01` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.4:7789;
        meta-disk internal;
      }
      on `hadb02` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.5:7789;
        meta-disk internal;
      }
    }

この後、両方の VM で `drbdadm` を使用して DRBD リソースを初期化します。

    sudo drbdadm -c /etc/drbd.conf role r0
    sudo drbdadm up r0

最後に、プライマリ (`hadb01`) 上で、DRBD リソースの所有権 (プライマリ) を強制適用します。

    sudo drbdadm primary --force r0

両方の VM で、/proc/drbd (`sudo cat /proc/drbd`) の内容をチェックする場合には、`hadb01` の `Primary/Secondary` および `hadb02` の `Secondary/Primary` がこの時点のソリューションと一致していることを確認する必要があります。5 GB ディスクは、お客様への課金なしで 10.10.10.0/24 ネットワーク上で同期されます。

ディスクが同期していれば、`hadb01` 上にファイルシステムを作成できます。テストのために ext2 を使用しましたが、次の手順を実行すると、ext3 ファイルシステムが作成されます。

    mkfs.ext3 /dev/drbd1

### DRBD リソースのマウント

`hadb01` 上に DRBD リソースをマウントします。Debian 系の Linux は、`/var/lib/mysql` を MySQL のデータ ディレクトリとして使用します。まだ MySQL をインストールしていないため、このディレクトリを次のようにして作成し、DRBD リソースをマウントします。`hadb01` の場合:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## MySQL のセットアップ

`hadb01` 上に MySQL をインストールします。

    sudo apt-get install mysql-server

`hadb02` の場合には、2 つの選択肢があります。1 つは、MySQL サーバーをインストールすることです。これにより、/var/lib/mysql が作成されるため、このディレクトリを新しいデータ ディレクトリに割り当てて、その中にあるものを削除します。`hadb02` の場合:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

もう 1 つの選択肢は、`hadb02` にフェールオーバーしてから、そこに MySQL サーバーをインストールすることです (既存のインストールがあれば、インストール スクリプトはそれを認識してそのまま保持します)。

`hadb01` の場合:

    sudo drbdadm secondary –force r0

`hadb02` の場合:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

ここで、DRBD のフェールオーバーを計画していない場合、1 つ目の選択肢は簡単ですがスマートさに欠けるかもしれません。このセットアップを行うと、次のように、MySQL データベースの操作を開始できます。`hadb02` の場合 (DRBD にとってアクティブなサーバーであればどのサーバーでも可):

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.* TO root;

**警告**: この最後のステートメントは、このテーブルのルート ユーザーに対する認証を無効にします。この部分は、製品レベルの GRANT ステートメントに置き換えて、単なる説明用として含める必要があります。

また、この記事の目的は VM の外部からクエリを実行することですが、その場合には、MySQL 用のネットワークを有効にする必要があります。両方の VM で、`/etc/mysql/my.cnf` を開き、`bind-address` に移動し、それを 127.0.0.1 から 0.0.0.0 に変更します。このファイルを保存した後で、現在のプライマリで `sudo service mysql restart` を発行します。

### MySQL 負荷分散セットの作成

Azure ポータルに戻って `hadb01` VM を表示し、さらにエンドポイントを表示します。ここで、新しいエンドポイントを作成します。ドロップダウンから MySQL (TCP 3306) をクリックし、*[新しい負荷分散セットを作成する]* チェック ボックスをオンにします。次に、負荷分散されたエンドポイント `lb-mysql` を呼び出します。時間は最低値の 5 (秒) に減らしますが、それ以外のほとんどのオプションはそのままにします。

エンドポイントを作成したら、`hadb02` を表示し、さらにエンドポイントを表示します。ここで新しいエンドポイントを作成しますが、`lb-mysql` を選択して、ドロップダウン メニューで MySQL をクリックします。この手順では、Azure CLI も使用できます。

これで、クラスターの手動操作に必要なものはすべて揃いました。

### 負荷分散セットのテスト

テストは、外部のマシンから、MySQL クライアントやアプリケーション (たとえば、Azure Website を実行している phpMyAdmin) を使用して実行できます。ここでは、別の Linux ボックスで MySQL のコマンド ライン ツールを使用します。

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### 手動によるフェールオーバー

フェールオーバーのシミュレーションができる状態になりました。MySQL をシャットダウンし、DRBD のプライマリを切り替え、MySQL を再起動します。

hadb01 の場合:

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

hadb02 の場合:

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

手動でフェールオーバーすると、リモート クエリを繰り返すことができます。正しく動作することを確認します。

## Corosync のセットアップ

Corosync は、クラスターの基盤となるインフラストラクチャで、Pacemaker を動作させるために必要です。Heartbeat v1 と v2 のユーザー (および他の Ultramonkey などの方法) から見れば、Corosync には Heartbeat の CRM の一部の機能しかありません。一方、Pacemaker には、より Hearbeat に近い機能が残されています。

Azure で Corosync を使用する場合、Microsoft Azure のネットワークがユニキャスト通信しかサポートしないのに対して、Corosync は、ユニキャストよりもブロードキャスト、ブロードキャストよりもマルチキャストを優先するという大きな制約があります。

ただし、Corosync にはユニキャスト動作モードがあり、実際上の制約は、各ノードのノード間通信が*自動的には*行われないため、各ノードの IP アドレスを含む構成ファイルでノードを定義する必要があることです。Corosync のユニキャスト用サンプル ファイルを使用し、バインド アドレス、ノード リスト、およびログ ディレクトリ (Ubuntu は `/var/log/corosync` を使用しますが、サンプル ファイルは `/var/log/cluster` を使用します) を変更するだけで、クォーラム ツールを有効にできます。

**次に示す `transport: udpu` ディレクティブ、および手動で定義したノードの IP アドレスを書き留めてください**。

両方のノードの `/etc/corosync/corosync.conf` について:

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

両方の VM でこの構成ファイルをコピーし、両方のノードで Corosync を起動します。

    sudo service start corosync

サービスを開始するとすぐに、現在のリング内にクラスターが構築される必要があります。また、クォーラムも構成される必要があります。この機能をチェックするには、ログを確認するかまたは次のコードで確認します。

    sudo corosync-quorumtool –l

次のイメージに近い出力が必要です。

![corosync-quorumtool -l sample output](media/virtual-machines-linux-mysql-cluster/image001.png)

## Pacemaker のセットアップ

Pacemaker は、クラスターを利用してリソースを監視し、プライマリがダウンしたときにリソースをセカンダリに切り替える時期を定義します。リソースは、利用できるスクリプト セット、LSB (init 風) スクリプトなどの選択肢から定義できます。

Pacemaker が DRBD リソース、マウント ポイント、および MySQL サービスを "所有" するようにします。プライマリに不具合が発生したとき、Pacemaker によって DRBD の有効/無効、マウント/マウントの解除、MySQL の起動/停止を正しい順序で切り替えることができれば、セットアップは完了したことになります。

初めて Pacemaker をインストールする場合には、次のような簡単な構成にします。

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

`sudo crm configure show` を実行してチェックします。ここで、次のリソースを使用してファイル (たとえば、`/tmp/cluster.conf`) を作成します。

    primitive drbd_mysql ocf:linbit:drbd \
          params drbd_resource="r0" \
          op monitor interval="29s" role="Master" \
          op monitor interval="31s" role="Slave"

    ms ms_drbd_mysql drbd_mysql \
          meta master-max="1" master-node-max="1" \
            clone-max="2" clone-node-max="1" \
            notify="true"

    primitive fs_mysql ocf:heartbeat:Filesystem \
          params device="/dev/drbd/by-res/r0" \
          directory="/var/lib/mysql" fstype="ext3"

    primitive mysqld lsb:mysql

    group mysql fs_mysql mysqld

    colocation mysql_on_drbd \
           inf: mysql ms_drbd_mysql:Master

    order mysql_after_drbd \
           inf: ms_drbd_mysql:promote mysql:start

    property stonith-enabled=false

    property no-quorum-policy=ignore

次に、このファイルを前述の構成に読み込みます (1 つのノードで行うだけで十分です)。

    sudo crm configure
      load update /tmp/cluster.conf
      commit
      exit

両方のノードで、ブート時に Pacemaker が起動することも確認します。

    sudo update-rc.d pacemaker defaults

数秒後、`sudo crm_mon –L` を使用して、ノードの 1 つがクラスターのマスターになり、すべてのリソースを実行していることを確認します。mount と ps を使用して、リソースが実行中であることを確認できます。

次のスクリーンショットは、1 つのノードが停止している状態の `crm_mon` を示しています (Control-C で終了)。

![crm\_mon node stopped](media/virtual-machines-linux-mysql-cluster/image002.png)

次のスクリーンショットでは、両方のノードのうち、1 つがマスター、もう 1 つがスレーブとなっています。

![crm\_mon operational master/slave](media/virtual-machines-linux-mysql-cluster/image003.png)

## テスト

これで、自動フェールオーバー シミュレーションの準備が完了しました。このシミュレーションを実行する場合、ソフト方式とハード方式の 2 つの方法があります。ソフト方式は、クラスターのシャットダウン機能 (``crm_standby -U `uname -n` -v on``) を利用します。マスターでこの機能を使用し、スレーブが引き継ぎます。この機能は、必ずオフに戻してください (そうしないと、crm\_mon は、1 つのノードがスタンバイ中であると通知します)。

ハード方式は、ポータル経由でプライマリ VM (hadb01) をシャットダウンするか、この VM の実行レベル (停止、シャットダウンなど) を変更します。さらに、マスターがダウンしているという通知を Corosync と Pacemaker に送る必要があります。このテストはメンテナンス時に有用ですが、単に VM をフリーズさせるだけでも同じ状況を強制的に作り出すこともできます。

## STONITH

物理デバイスを制御する STONITH スクリプトの代わりに、Azure CLI 経由で VM のシャットダウンを発行することができます。ベースとして `/usr/lib/stonith/plugins/external/ssh` を使用し、クラスターの構成で STONITH を有効にできます。Azure CLI をグローバルにインストールし、発行設定/プロファイルをクラスターのユーザー用に読み込む必要があります。

このリソースのサンプル コードは [GitHub](https://github.com/bureado/aztonith) で入手できます。`sudo crm configure` に次のコードを追加することにより、クラスターの構成を変更する必要があります。

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

**注:** このスクリプトは、up/down のチェックは実行しません。元の SSH リソースには、15 の ping チェックがありますが、Azure VM の復旧時間はさらに大きな可変性を持ちます。

## 制限事項

次の制限事項が適用されます。

- ノードをシャットダウンする場合、Pacemaker で DRBD をリソースとして管理する linbit DRBD リソース スクリプトは、ノードがスタンバイを始めたばかりでも、`drbdadm down` を使用します。マスターへの書き込み中はスレーブは DRBD リソースと同期しないため、このような動作は適切ではありません。マスターのフェールオーバーが正しく行われなかった場合、スレーブは古いファイルシステムの状態を引き継ぐ可能性があります。こうした状況は、次の 2 つの方法により解決する可能性があります。
  - ローカルの (クラスター化されていない) ウォッチドッグを介してすべてのクラスター ノードに `drbdadm up r0` を強制適用する。
  - linbit DRBD スクリプトを編集し、`/usr/lib/ocf/resource.d/linbit/drbd` の `down` が呼び出されないようにする。
- 負荷分散の応答には、少なくとも 5 秒必要なため、アプリケーションはクラスター対応であると共に、タイムアウトに寛容である必要があります。アプリケーション内キュー、クエリ ミドルウェアなど、他のアーキテクチャが役立つ可能性もあります。
- 適切なペースで書き込みが実行されるためには MySQL の調整が必要です。また、メモリ損失を最小化するため、できるだけ頻繁にキャッシュをディスクにフラッシュします。
- DRBD はデバイスをリプリケートするためのメカニズムとして、仮想スイッチで VM を相互に接続しているため、書き込み性能は、仮想スイッチでの接続状態に左右されます。

<!---HONumber=Sept15_HO4-->
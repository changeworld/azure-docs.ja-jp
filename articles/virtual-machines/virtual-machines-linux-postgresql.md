<properties
	pageTitle="Linux を実行する Microsoft Azure Virtual Machine への PostgreSQL のインストールと構成"
	description="Azure の Ubuntu または CentOS 仮想マシン (VM) に PostgreSQL をインストールして構成する方法を説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
  tags=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="linux"
	ms.workload="infrastructure-services"
	ms.date="06/04/2015"
	ms.author="mingzhan"/>


#Microsoft Azure での PostgreSQL のインストールと構成

PostgreSQL は、Oracle や DB2 に似た高機能のオープン ソース データベースです。PostgreSQL には、完全な ACID 準拠、信頼性の高いトランザクション処理、複数バージョンの同時実行制御など、エンタープライズ対応の機能が含まれます。また、ANSI SQL や SQL/MED などの標準をサポートします (Oracle、MySQL、MongoDB、その他多くの外部データ ラッパーを含みます).12 以上の手続き型言語、GIN および GIST のインデックス、空間データ、および JSON またはキーと値に基づくアプリケーションに対する NoSQL に似た複数の機能などのサポートにより、高度な拡張が可能です。

この記事では、Linux を実行している Azure Virtual Machine に PostgreSQL をインストールして構成する方法を説明します。

> [Azure.NOTE]このチュートリアルを実行するには、Linux を実行する Microsoft Azure Virtual Machine が既に存在している必要があります。続行する前に、[Azure Linux VM チュートリアル](virtual-machines-linux-tutorial.md)を見て Linux VM を作成およびセットアップしてください。

(この例では、PostgreSQL ポートとしてポート 1999 を使用します。)

## PostgreSQL のインストール

putty を使用して作成した Linux VM に接続します。初めて Azure Linux VM を使用する場合、putty を使用して Linux VM に接続する方法については、[こちら](virtual-machines-linux-use-ssh-key.md)を参照してください。

1. 次のコマンドを実行して、ルート (admin) に切り替えます。

		$ sudo su -

2. 一部のディストリビューションには、PostgreSQL をインストールする前にインストールする必要がある依存アプリケーションがあります。次の一覧でお使いのディストリビューションを確認し、適切なコマンドを実行してください。

	- Redhat:

			# yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

	- Debian:

 			# apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  

	- Suse:

			# zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

3. PostgreSQL をルート ディレクトリにダウンロードし、パッケージを解凍します。

		# wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/

		# tar jxvf  postgresql-9.3.5.tar.bz2

	上に示したのは例です。詳細なダウンロード アドレスは、[こちら](https://ftp.postgresql.org/pub/source/)を参照してください。

4. ビルドを開始するには、以下のコマンドを実行します。

		# cd postgresql-9.3.5

		# ./configure --prefix=/opt/postgresql-9.3.5

5. ドキュメント (HTML およびマニュアル ページ) や追加モジュール (contrib) など、ビルドできるものをすべてビルドする場合は、代わりに次のコマンドを実行します。

		# gmake install-world

	次の確認メッセージが表示されます。

		PostgreSQL, contrib, and documentation successfully made. Ready to install.

## PostgreSQL の構成

1. (省略可能) シンボリック リンクを作成して、バージョン番号を含まないように PostgreSQL の参照を短縮します。

		# ln -s /opt/pgsql9.3.5 /opt/pgsql

2. データベース用のディレクトリを作成します。

		# mkdir -p /opt/pgsql_data

3. root 以外のユーザーを作成し、そのユーザーのプロファイルを変更します。この新しいユーザーに切り替えます (この例では *postgres*)。

		# useradd postgres

		# chown -R postgres.postgres /opt/pgsql_data

		# su - postgres

    >[Azure.NOTE]セキュリティ上の理由から、PostgreSQL ではデータベースの初期化、開始、またはシャットダウンに root 以外のユーザーを使用します。


4. *bash\_profile* を編集し、次のコマンドを入力します。以下の行を *bash\_profile* ファイルの最後に追加します。

		cat >> ~/.bash_profile <<EOF
		export PGPORT=1999
		export PGDATA=/opt/pgsql_data
		export LANG=en_US.utf8
		export PGHOME=/opt/pgsql
		export PATH=\$PATH:\$PGHOME/bin
		export MANPATH=\$MANPATH:\$PGHOME/share/man
		export DATA=`date +"%Y%m%d%H%M"`
		export PGUSER=postgres
		alias rm='rm -i'
		alias ll='ls -lh'
		EOF

5. *bash\_profile* ファイルを実行します。

		$ source .bash_profile

6. 次のコマンドでインストールを検証します。

		$ which psql

	インストールが成功した場合は、次の応答が表示されます。

		/opt/pgsql/bin/psql

7. PostgreSQL のバージョンをチェックすることもできます。

		$ psql -V

8. ディスクを初期化します。

		$ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W

	次の出力が表示されます。

![image](./media/virtual-machines-linux-postgresql/no1.png)

## PostgreSQL のセットアップ

<!--	[postgres@ test ~]$ exit -->

次のコマンドを実行します。

	# cd /root/postgresql-9.3.5/contrib/start-scripts

	# cp linux /etc/init.d/postgresql

/etc/init.d/postgresql ファイルの 2 つの変数を変更します。prefix には、PostgreSQL のインストール パス **/opt/pgsql** を設定します。PGDATA には、PostgreSQL のデータ ストレージ パス **/opt/pgsql\_data** を設定します。

	# sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

	# sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/virtual-machines-linux-postgresql/no2.png)

実行可能なようにファイルを変更します。

	# chmod +x /etc/init.d/postgresql

PostgreSQL を開始します。

	# /etc/init.d/postgresql start

PostgreSQL のエンドポイントがオンかどうかを確認します。

	# netstat -tunlp|grep 1999

次の出力が表示されます。

![image](./media/virtual-machines-linux-postgresql/no3.png)

## Postgres データベースへの接続

次に、postgres ユーザーに再び切り替えます。

	# su - postgres

Postgres データベースを作成します。

	$ createdb events

先に作成したイベント データベースに接続します。

	$ psql -d events

## Postgres テーブルを作成および削除する方法

データベースに接続したので、そこにテーブルを作成できます。

たとえば、サンプルの Postgres テーブルを新しく作成するには、次のコマンドを実行します。

	CREATE TABLE potluck (name VARCHAR(20),	food VARCHAR(30),	confirmed CHAR(1), signup_date DATE);

次の列名と制限で 4 列のテーブルを設定しました。

1. “name” 列は、VARCHAR コマンドによって、20 文字以下に制限されています。
2. "food" 列は各自が持ってくる食品アイテムを示します。VARCHAR によってこのテキストは 30 文字以下に制限されます。
3. “confirmed” 列は、ユーザーが料理に対する RSVP を持っているかどうかを記録します。許容される値は、"Y" と "N" です。
4. “date” 列は、イベントにサインアップした日付を示します。Postgres では、日付を yyyy-mm-dd と記述する必要があります。

テーブルが正常に作成された場合、次が表示されます。

![image](./media/virtual-machines-linux-postgresql/no4.png)

次のコマンドを使用して、テーブルの構造をチェックすることもできます。

![image](./media/virtual-machines-linux-postgresql/no5.png)

### テーブルにデータを追加する

最初に、行に情報を挿入します。

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

次のように出力されます。

![image](./media/virtual-machines-linux-postgresql/no6.png)

テーブルにさらに人を追加することもできます。オプションをいくつか次に示します。独自に作成することもできます。

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

	INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### テーブルの表示

テーブルを表示するには、次のコマンドを使用します。

	select * from potluck;

出力は次のようになります。

![image](./media/virtual-machines-linux-postgresql/no7.png)

### テーブルのデータの削除

テーブル内のデータを削除するには、次のコマンドを使用します。

	delete from potluck where name=’John’;

これにより、"John" 行のすべての情報が削除されます。出力は次のようになります。

![image](./media/virtual-machines-linux-postgresql/no8.png)

### テーブルのデータの更新

テーブル内のデータを更新するには、次のコマンドを使用します。この例では、Sandy が参加を確認したので、RSVP を "N" から "Y" に変更します。

 	UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


##PostgreSQL に関する詳細
Microsoft Azure Linux 仮想マシンへの PostgreSQL のインストールは以上で完了です。Microsoft Azure の使用をお楽しみください。PostgreSQL に関する詳細については、[こちら](http://www.postgresql.org/)を参照してください。

<!---HONumber=August15_HO6-->
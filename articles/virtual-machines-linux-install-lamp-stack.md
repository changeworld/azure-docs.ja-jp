<properties linkid="manage-linux-common-tasks-lampstack" urlDisplayName="LAMP スタックのインストール" pageTitle="Linux 仮想マシンへの LAMP スタックのインストール" metaKeywords="" description="Azure 上の Linux 仮想マシン (VM) に LAMP スタックをインストールする方法について説明します。Ubuntu または CentOS 上でインストールできます。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Azure 上の Linux 仮想マシンへの LAMP スタックのインストール" authors="" solutions="" manager="" editor="" />




#Azure 上の Linux 仮想マシンへの LAMP スタックのインストール

LAMP スタックは次のさまざまな要素で構成されています。

- **L**inux - オペレーティング システム
- **A**pache - Web サーバー
- **M**ySQL - データベース サーバー
- **P**HP - プログラミング言語


##Ubuntu へのインストール

次のパッケージをインストールする必要があります。

- `apache2`
- `mysql-server`
- `php5`
- `php5-mysql`
- `libapache2-mod-auth-mysql`
- `libapache2-mod-php5`
- `php5-xsl`
- `php5-gd`
- `php-pear`

このインストールは次の 1 つの `apt-get install` コマンドで実行できます。

	apt-get install apache2 mysql-server php5 php5-mysql libapache2-mod-auth-mysql libapache2-mod-php5 php5-xsl php5-gd php-pear


##CentOS へのインストール

次のパッケージをインストールする必要があります。

- `httpd`
- `mysql`
- `mysql-server`
- `php`
- `php-mysql`

このインストールは次の 1 つの `yum install` コマンドで実行できます。

	yum install httpd mysql mysql-server php-php-mysql


設定
----------

1. **Apache** を設定します。

	1. Apache Web サーバーの再起動が必要になります。次のコマンドを実行します。

			sudo /etc/init.d/apache2 restart

	2. インストールが実行されていることを確認します。ブラウザーで、[http://localhost](http://localhost) をポイントします。"It works!" と表示されます。

2. **MySQL** を設定します。
	1. 次のコマンドを実行して MySQL の root パスワードを設定します。
	
			mysqladmin -u root -p password yourpassword

	2. `mysql` または各種の MySQL クライアントを使用してコンソールにログインします。

3. **PHP** を設定します。

	1. 次のコマンドを実行して Apache PHP Module を有効にします。

			sudo a2enmod php5

	2. 次のコマンドを実行して Apache を再起動します。

			sudo service apache2 restart


##参考資料

Ubuntu 上での LAMP スタックの設定については多くのリソースがあります。

- [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)
- [http://fedorasolved.org/server-solutions/lamp-stack](http://fedorasolved.org/server-solutions/lamp-stack)


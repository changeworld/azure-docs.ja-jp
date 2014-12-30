<properties urlDisplayName="Install LAMP stack" pageTitle="Linux 仮想マシンへの LAMP スタックのインストール" metaKeywords="" description="Learn how to install the LAMP stack on a Linux virtual machine (VM) in Azure. You can install on Ubuntu or CentOS." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install the LAMP Stack on a Linux virtual machine in Azure" authors="szark" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="11/18/2014" ms.author="szark" />



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

`apt-get update` を実行してパッケージのローカル リストを更新したら、これらのパッケージを 1 つの `apt-get install` コマンドでインストールできます。

	# sudo apt-get update
	# sudo apt-get install apache2 mysql-server php5 php5-mysql

上記のコマンドを実行すると、これらのパッケージとその他のいくつかの依存関係をインストールするよう求められます。続行するには、y キーと Enter キーの順に押し、後続の指示に従って MySQL の管理パスワードを設定します。

これにより、MySQL で PHP を使用する上で必要最小限の PHP 拡張機能がインストールされます。パッケージとして利用可能な他の PHP 拡張機能を表示するには、下記のコマンドを実行します。

	# apt-cache search php5


##CentOS と Oracle Linux へのインストール

次のパッケージをインストールする必要があります。

- `httpd`
- `mysql`
- `mysql-server`
- `php`
- `php-mysql`

これらのパッケージは 1 つの `yum install` コマンドでインストールできます。

	# sudo yum install httpd mysql mysql-server php php-mysql

上記のコマンドを実行すると、これらのパッケージとその他のいくつかの依存関係をインストールするよう求められます。続行するには、y キーと Enter キーの順に押します。

これにより、MySQL で PHP を使用する上で必要最小限の PHP 拡張機能がインストールされます。パッケージとして利用可能な他の PHP 拡張機能を表示するには、下記のコマンドを実行します。

	# yum search php


## SUSE Linux Enterprise Server へのインストール

次のパッケージをインストールする必要があります。

- apache2
- mysql
- apache2-mod_php53
- php53-mysql

これらのパッケージは 1 つの `zypper install` コマンドでインストールできます。

	# sudo zypper install apache2 mysql apache2-mod_php53 php53-mysql

上記のコマンドを実行すると、これらのパッケージとその他のいくつかの依存関係をインストールするよう求められます。続行するには、y キーと Enter キーの順に押します。

これにより、MySQL で PHP を使用する上で必要最小限の PHP 拡張機能がインストールされます。パッケージとして利用可能な他の PHP 拡張機能を表示するには、下記のコマンドを実行します。

	# zypper search php


設定
----------

1. **Apache** を設定します。

	- 次のコマンドを実行して Apache Web サーバーが起動されたことを確認します。

		- Ubuntu & SLES: `sudo service apache2 restart`

		- CentOS & Oracle: `sudo service httpd restart`

	- Apache は既定でポート 80 をリッスンします。Apache サーバーにリモートでアクセスするには、エンドポイントを開く必要がある場合があります。詳細な手順については、[エンドポイントの構成](http://azure.microsoft.com/ja-jp/documentation/articles/virtual-machines-set-up-endpoints/) に関するドキュメントを参照してください。

	- これで、Apache が実行されていること、およびコンテンツを返していることを確認できます。ブラウザーで `http://[MYSERVICE].cloudapp.net` にアクセスします。**[MYSERVICE]** は仮想マシンが配置されているクラウド サービスの名前です。一部のディストリビューションでは、"It works!" とだけ表示された既定の Web ページが表示される場合があります。または、追加のドキュメントへのリンクや Apache サーバーの構成に関するコンテンツを掲載した、より詳細な Web ページが表示される場合もあります。

2. **MySQL** を設定します。

	- Ubuntu の場合この手順は不要で、mysql-server パッケージのインストール時に MySQL `root` パスワードが求められます。

	- 他のディストリビューションの場合、次のコマンドを実行して MySQL の root パスワードを設定します。

			# mysqladmin -u root -p password yourpassword

	- これにより、`mysql` または `mysqladmin` ユーティリティを使用して MySQL を管理できます。


##参考資料

Ubuntu 上での LAMP スタックの設定については多くのリソースがあります。

- [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

<!--HONumber=35_1-->

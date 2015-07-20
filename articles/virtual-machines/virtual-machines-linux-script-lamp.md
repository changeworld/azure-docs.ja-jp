<properties
	pageTitle="Azure カスタム スクリプト拡張機能を使って Linux アプリケーションをデプロイする"
	description="Azure カスタム スクリプト拡張機能を使って Linux 仮想マシンにアプリケーションをデプロイする方法について説明します。"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	services="virtual-machines"
	authors="gbowerman"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2015"
	ms.author="guybo"/>

#Linux 向けに Azure カスタム スクリプト拡張機能を使って LAMP アプリをデプロイする#

Linux 向け Azure カスタム スクリプト拡張機能では、Python や Bash など仮想マシンでサポートされているスクリプト言語で記述された任意のコードを実行する仮想マシンをカスタマイズする方法が提供されます。これによって、非常に柔軟に複数のマシンにアプリケーションを自動的にデプロイすることが可能になります。

カスタム スクリプト拡張機能は、Azure ポータル、PowerShell、Azure コマンド ライン インターフェイス (Azure CLI) を使ってデプロイできます。

ここでは、Azure CLI を使って簡単な LAMP アプリケーションを Ubuntu にデプロイしていきます。

## 前提条件

ここでは Ubuntu 14.04 を実行する 2 つの Azure 仮想マシンを作成します。それぞれ *script-vm* と *lamp-vm* という名前にします。自身の環境で行うときは、それぞれ固有の名前を付けてください。1 つは CLI コマンド向け、もう 1 つは LAMP アプリのデプロイに使用します。

また、Azure ストレージ アカウントとアカウントにアクセスするキーが必要になります (Azure ポータルで取得できます)。

Azure での Linux VM の作成については、「[Linux を実行する仮想マシンの作成](virtual-machines-linux-tutorial.md)」をご覧ください。

ここで使用するインストール コマンドは Ubuntu を前提としていますが、どのサポートされたディストリビューションに対しても基本的な手順は同じです。

仮想マシン *script-vm* では、Azure に接続できる Azure CLI がインストールされていることが必要です。詳細については、[Azure コマンド ライン インターフェイスのインストールと構成](../xplat-cli.md)に関するページを参照してください。

## スクリプトのアップロード

ここでは、カスタム スクリプト拡張機能によってリモート仮想マシンでスクリプトが実行され、LAMP スタックがインストールされて PHP ページが作成されます。Azure BLOB のようにアップロードすれば、どこからでもスクリプトにアクセスできます。

**スクリプト**

次のスクリプトでは、LAMP スタックを Ubuntu にインストールし (MySQL のサイレント インストールの設定を含む)、簡単な PHP ファイルを記述して Apache を起動します。

	#!/bin/bash
	# set up a silent install of MySQL
	dbpass="mySQLPassw0rd"

	export DEBIAN_FRONTEND=noninteractive
	echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
	echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

	# install the LAMP stack
	apt-get -y install apache2 mysql-server php5 php5-mysql  

	# write some PHP
	echo <center><h1>My Demo App</h1><br/></center> > /var/www/html/phpinfo.php
	echo <\?php phpinfo()\; \?> >> /var/www/html/phpinfo.php

	# restart Apache
	apachectl restart

**アップロード**

スクリプトを *lamp_install.sh* などとしてテキスト ファイルに保存し、Azure ストレージ アカウントにアップロードします。Azure CLI を使えば簡単にできます。次の例では、ファイルを「scripts」という名前のストレージ コンテナーにアップロードします。注: コンテナーが存在しない場合は、まずコンテナーを作成する必要があります。

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

また、Azure ストレージからスクリプトをダウンロードする方法を記述した JSON ファイルも作成します。*public_config.json* という名前でこのファイルを保存します (「mystorage」をご利用のストレージ アカウントの名前に置き換えてください)。

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## 拡張機能のデプロイ

これで、Azure CLI を使ってリモート仮想マシンに Linux カスタム スクリプト拡張機能をデプロイする準備ができました。

    azure vm extension set -c "./public_config.json" lamp-vm CustomScriptForLinux Microsoft.OSTCExtensions 1.*

これによって、*lamp-vm* という名前の仮想マシンで *lamp_install.sh* スクリプトがダウンロードされて実行されます。

アプリに Web サーバーが含まれる場合は、リモート仮想マシンで HTTP リスニング ポートが開かれていることをご確認ください。

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## 監視とトラブルシューティング

リモート仮想マシンのログファイルを参照すれば、カスタム スクリプトの実行の進行状況を確認できます。*lamp-vm* に SSH で通信し、ログ ファイルの内容を確認します。

    cd /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.3.0.0/
    tail -f extension.log

カスタム スクリプト拡張機能の実行が終了すると、作成した PHP ページを参照できるようになります。ここでは *http://lamp-vm.cloudapp.net/phpinfo.php* となります。

## その他のリソース

同じ基本的な手順で、より複雑なアプリをデプロイすることも可能です。この例では、インストール スクリプトは Azure ストレージのパブリック BLOB として保存されています。インストール スクリプトを [Shared Access Signature](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS) を使ったセキュアな BLOB として保存すると、より安全です。

Azure CLI、Linux、カスタム スクリプト拡張機能の詳細については、次をご覧ください。

[Linux VM カスタム タスクをカスタム スクリプト拡張機能を使って自動化する](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Azure Linux 拡張機能 (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Azure での Linux とオープン ソース コンピューティング](virtual-machines-linux-opensource.md)
 

<!---HONumber=July15_HO2-->
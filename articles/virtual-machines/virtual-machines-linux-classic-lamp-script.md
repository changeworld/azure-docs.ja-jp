<properties
	pageTitle="Linux VM での CustomScript 拡張機能の使用 | Microsoft Azure"
	description="CustomScript 拡張機能を使って、クラシック デプロイ モデルを使用して作成された Azure 上の Linux Virtual Machines にアプリケーションをデプロイする方法について説明します。"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	services="virtual-machines-linux"
	authors="gbowerman"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="multiple"
	ms.tgt_pltfrm="linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2015"
	ms.author="guybo"/>

#Linux 向けに Azure カスタム スクリプト拡張機能を使って LAMP アプリをデプロイする#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。


Linux 向け Microsoft Azure カスタム スクリプト拡張機能では、Python や Bash など、仮想マシン (VM) でサポートされているスクリプト言語で記述された任意のコードを実行する VM をカスタマイズすることができます。これによって、非常に柔軟に複数のマシンにアプリケーションを自動的にデプロイすることが可能になります。

カスタム スクリプト拡張機能は、Azure クラシック ポータル、Windows PowerShell、Azure コマンド ライン インターフェイス (Azure CLI) を使ってデプロイできます。

この記事では、Azure CLI を使って、クラシック デプロイ モデルを使用して作成された Ubuntu VM に単純な LAMP アプリケーションをデプロイします。

## 前提条件

この例では、まず、Ubuntu 14.04 以降を実行している 2 つの Azure VM を作成します。VM の名前は *script-vm* と *lamp-vm* です。自分で VM を作成するときは、固有の名前を付けてください。1 つは CLI コマンドの実行に使用し、もう 1 つは LAMP アプリのデプロイに使用します。

また、Azure ストレージ アカウントとアカウントにアクセスするキーが必要になります (Azure クラシック ポータルで取得できます)。

Azure での Linux VM の作成については、「[Linux を実行する仮想マシンの作成](virtual-machines-linux-cli-create.md)」をご覧ください。

インストール コマンドでは Ubuntu が想定されますが、サポートされる Linux ディストリビューションに応じてインストールを調整できます。

仮想マシン script-vm では、Azure に接続できる Azure CLI がインストールされていることが必要です。詳細については、[Azure コマンド ライン インターフェイスのインストールと構成](../xplat-cli-install.md)に関するページを参照してください。

## スクリプトのアップロード

ここでは、カスタム スクリプト拡張機能を使ってリモート VM でスクリプトを実行し、LAMP スタックをインストールして PHP ページを作成します。Azure BLOB のようにアップロードすれば、どこからでもスクリプトにアクセスできます。

### スクリプトの概要

スクリプトの例では、LAMP スタックを Ubuntu にインストールし (MySQL のサイレント インストールの設定を含む)、簡単な PHP ファイルを記述して Apache を起動します。

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

### スクリプトのアップロード

スクリプトを *lamp\_install.sh* などとしてテキスト ファイルに保存し、Azure Storage にアップロードします。Azure CLI を使えば簡単にできます。次の例では、ファイルを「scripts」という名前のストレージ コンテナーにアップロードします。コンテナーが存在しない場合は、まずコンテナーを作成する必要があります。

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

また、Azure Storage からスクリプトをダウンロードする方法を記述した JSON ファイルも作成します。*public\_config.json* という名前でこのファイルを保存します (「mystorage」をご利用のストレージ アカウントの名前に置き換えてください)。

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## 拡張機能のデプロイ

次のコマンドを使用して、Azure CLI を使ってリモート VM に Linux カスタム スクリプト拡張機能をデプロイします。

    azure vm extension set -c "./public_config.json" lamp-vm CustomScriptForLinux Microsoft.OSTCExtensions 1.*

前のコマンドによって、*lamp\_install.sh* スクリプトが *lamp-vm* という名前の VM にダウンロードされて実行されます。

アプリに Web サーバーが含まれる場合は、次のコマンドを使用して、リモート VM で HTTP リスニング ポートが開かれていることを確認してください。

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## 監視とトラブルシューティング

リモート VM 上のログ ファイルを確認し、カスタム スクリプトの実行状況を確認できます。次のコマンドを使用して、*lamp-vm* に SSH で通信し、ログ ファイルの内容を確認します。

    cd /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.3.0.0/
    tail -f extension.log

CustomScript 拡張機能を実行すると、参照用に作成した PHP ページを参照できます。たとえば、この記事の PHP ページは **http://lamp-vm.cloudapp.net/phpinfo.php* です。

## その他のリソース

同じ基本的な手順で、より複雑なアプリをデプロイすることも可能です。この例では、インストール スクリプトは Azure Storage のパブリック BLOB として保存されています。インストール スクリプトを [Shared Access Signature](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS) を使ったセキュアな BLOB として保存すると、より安全です。

次に、Azure CLI、Linux、およびカスタム スクリプト拡張機能のその他のリソースをリストします。

[Linux VM カスタム タスクをカスタム スクリプト拡張機能を使って自動化する](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Azure Linux 拡張機能 (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Azure での Linux とオープン ソース コンピューティング](virtual-machines-linux-opensource-links.md)

<!---HONumber=AcomDC_0323_2016-->
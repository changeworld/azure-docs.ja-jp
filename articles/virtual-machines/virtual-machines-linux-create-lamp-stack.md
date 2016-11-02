<properties
	pageTitle="Linux 仮想マシンへの LAMP のデプロイ | Microsoft Azure"
	description="Linux VM に LAMP スタックをインストールする方法を説明します。"
	services="virtual-machines-linux"
	documentationCenter="virtual-machines"
	authors="jluk"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="NA"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="jluk"/>

# Azure への LAMP スタックのデプロイ
この記事では、Apache Web サーバー、MySQL、および PHP (LAMP スタック) を Azure にデプロイする方法について説明します。Azure アカウント ([無料試用版を入手してください](https://azure.microsoft.com/pricing/free-trial/)) と、[Azure アカウントに接続している](../xplat-cli-connect.md) [Azure CLI](../xplat-cli-install.md) が必要になります。

この記事で取り上げる LAMP のインストール方法は 2 つあります。

## クイック コマンドの概要

1) 新しい VM に LAMP をデプロイする

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

2) 既存の VM に LAMP をデプロイする

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## 新しい VM に LAMP をデプロイするチュートリアル

まず、VM が含まれる新しい[リソース グループ](../resource-group-overview.md)を作成します。

    $ azure group create uniqueResourceGroup westus
    info:    Executing command group create
    info:    Getting resource group uniqueResourceGroup
    info:    Creating resource group uniqueResourceGroup
    info:    Created resource group uniqueResourceGroup
    data:    Id:                  /subscriptions/########-####-####-####-############/resourceGroups/uniqueResourceGroup
    data:    Name:                uniqueResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

VM 自体を作成するには、[GitHub のこちら](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app)にある既に作成済みの Azure Resource Manager テンプレートを使用できます。

    $ azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json uniqueResourceGroup uniqueLampName

さらに入力を求める応答画面が表示されます。

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    storageAccountNamePrefix: lampprefix
    location: westus
    adminUsername: someUsername
    adminPassword: somePassword
    mySqlPassword: somePassword
    dnsLabelPrefix: azlamptest
    info:    Initializing template configurations and parameters
    info:    Creating a deployment
    info:    Created template deployment "uniqueLampName"
    info:    Waiting for deployment to complete
    data:    DeploymentName     : uniqueLampName
    data:    ResourceGroupName  : uniqueResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          :
    data:    Mode               : Incremental
    data:    CorrelationId      : d51bbf3c-88f1-4cf3-a8b3-942c6925f381
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    DeploymentParameters :
    data:    Name                      Type          Value
    data:    ------------------------  ------------  -----------
    data:    storageAccountNamePrefix  String        lampprefix
    data:    location                  String        westus
    data:    adminUsername             String        someUsername
    data:    adminPassword             SecureString  undefined
    data:    mySqlPassword             SecureString  undefined
    data:    dnsLabelPrefix            String        azlamptest
    data:    ubuntuOSVersion           String        14.04.2-LTS
    info:    group deployment create command OK

これで、LAMP が既にインストールされている Linux VM が作成されました。必要に応じて、「LAMP が正常にインストールされていることを確認する」に移動してインストールを確認できます。

## LAMP を既存の VM にデプロイするチュートリアル

Linux VM の作成についてサポートが必要な場合は、Linux VM を作成する方法を[こちら](./virtual-machines-linux-quick-create-cli.md)に移動して確認します。次に、Linux VM に SSH で接続する必要があります。SSH キーの作成についてサポートが必要な場合は、Linux または Mac で SSH キーを作成する方法を[こちら](./virtual-machines-linux-mac-create-ssh-keys.md)に移動して確認します。SSH キーが既にある場合は、先に進み、`ssh username@uniqueDNS` を使用して Linux VM に SSH で接続します。

Linux VM では作業しているため、Debian ベースのディストリビューションに LAMP スタックをインストールする方法について説明します。Linux ディストリビューションによって、コマンドが微妙に異なる場合があります。

#### Debian または Ubuntu へのインストール

次のパッケージをインストールする必要があります: `apache2`、`mysql-server`、`php5`、および `php5-mysql`。これらのパッケージは、直接取得するか、Tasksel を使用することでインストールできます。両方のオプションの手順を以下に示します。インストールする前に、パッケージの一覧をダウンロードして更新する必要があります。

    user@ubuntu$ sudo apt-get update
    
##### 個々のパッケージ
apt-get の使用

	user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### Tasksel の使用
または、Tasksel をダウンロードすることができます。これは、複数の関連パッケージを一連のタスクとしてシステムにインストールするための、Debian/Ubuntu ツールです。

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

上記のいずれかのオプションを実行すると、これらのパッケージとその他のいくつかの依存関係をインストールするよう求められます。続行するには、y キーと Enter キーの順に押し、後続の指示に従って MySQL の管理パスワードを設定します。これにより、MySQL で PHP を使用する上で必要最小限の PHP 拡張機能がインストールされます。

![][1]

パッケージとして利用可能な他の PHP 拡張機能を表示するには、下記のコマンドを実行します。

	user@ubuntu$ apt-cache search php5


#### info.php ドキュメントを作成する

コマンドラインに `apache2 -v`、`mysql -v`、または `php -v` を入力して、使用している Apache、MySQL、PHP のバージョンを確認できるようになりました。

さらにテストしたい場合は、簡単な PHP 情報ページを作成して、ブラウザーで表示できます。Nano テキスト エディターで次のコマンドを使用して新しいファイルを作成します。

    user@ubuntu$ sudo nano /var/www/html/info.php

GNU Nano テキスト エディターに次の行を追加します。

    <?php
    phpinfo();
    ?>

保存してテキスト エディターを終了します。

すべての新規インストールを有効にするために、次のコマンドで Apache を再起動します。

    user@ubuntu$ sudo service apache2 restart

## LAMPが正常にインストールされていることを確認する

http://youruniqueDNS/info.php に移動して、作成した PHP 情報ページをブラウザーでチェックできます。次のように表示されます。

![][2]

http://youruniqueDNS/ に移動して Apache2 Ubuntu の既定のページを表示することで、Apache インストールを確認できます。次のような結果が表示されます。

![][3]

これで、Azure VM 上に LAMP スタックを設定できました。

## 次のステップ

LAMP スタックで Ubuntu のドキュメントを確認します。

- [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/virtual-machines-linux-deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/virtual-machines-linux-deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/virtual-machines-linux-deploy-lamp-stack/apachesuccesspage.png

<!---HONumber=AcomDC_0824_2016-->
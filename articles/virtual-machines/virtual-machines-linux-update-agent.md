<properties
	pageTitle="GitHub から Azure Linux エージェントを更新する | Microsoft Azure"
	description="Azure の Linux VM で使用する Azure Linux エージェントを Github の最新バージョンに更新する方法について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="mingzhan"/>


# VM 上の Azure Linux エージェントを GitHub で最新バージョンに更新する方法

Azure 上の Linux VM の [Azure Linux エージェント](https://github.com/Azure/WALinuxAgent)を更新するには、既に次の環境が整っている必要があります。

1. Linux VM が Azure で実行されている
2. SSH を使用してその Linux VM に接続している

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


> [AZURE.NOTE]Windows コンピューターでこのタスクを実行する場合は、Putty を使用して Linux マシンに SSH 接続できます。詳細については、「[Linux を実行する仮想マシンにログオンする方法](virtual-machines-linux-how-to-log-on.md)」を参照してください。

Azure での動作保証済み Linux ディストリビューションは、そのリポジトリに Azure Linux エージェント パッケージを格納しているため、可能な場合は、そのディストリビューションのリポジトリを確認して最新のバージョンをインストールしてください。

Ubuntu の場合は、次のように入力するだけでかまいません。

    #sudo apt-get install walinuxagent

また CentOS に次のように入力します。

    #sudo yum install waagent

Oracle Linux の場合は、`/etc/yum.repo.d/public-yum-ol6.repo` または `/etc/yum.repo.d/public-yum-ol7.repo` ファイルでアドオンのリポジトリが有効になっていることを確認してください。

    #sudo yum install WALinuxAgent

通常、必要な手順はこれですべてですが、何らかの理由がある場合は、これを https://github.com から直接インストールする必要があります。その場合は、次の手順を実行してください。


## wget のインストール

SSH を使用して VM にログインします。

コマンド ラインで「`#sudo yum install wget`」と入力して、wget をインストールします (Redhat、CentOS、Oracle Linux Version 6.4、6.5 のように既定ではインストールされないディストリビューションもあります)。


## 最新版のダウンロード

[GitHub の Azure Linux エージェントのリリース](https://github.com/Azure/WALinuxAgent/releases)が記載されている Web ページを開き、最新バージョンを見つけます。(「`#waagent --version`」と入力すると最新のバージョンを検索できます)。

###バージョン 2.0.x の場合は次のように入力します:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

   次は、バージョン 2.0.14 を例として使用しています。

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent  

###バージョン 2.1.x 以降の場合は次のように入力します:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
    #unzip WALinuxAgent-[version].zip
    #cd WALinuxAgent-[version]

   次の行は、バージョン 2.1.0 を例として使用しています。

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
    #unzip WALinuxAgent-2.1.0.zip  
    #cd WALinuxAgent-2.1.0

##Linux エージェントのインストール

###バージョン 2.0.x の場合は次を使用します:

 waagent を実行可能にする

    #chmod +x waagent

 新しい実行可能ファイルを /usr/sbin にコピーする

  ほとんどの Linux では、次のコマンドを使用します。

      #sudo cp waagent /usr/sbin

  CoreOs の場合は、次のコマンドを使用します。

    #sudo cp waagent /usr/share/oem/bin/

###バージョン 2.1.x の場合は次を使用します:

パッケージ `setuptools` を先にインストールする必要がある場合は、[こちら](https://pypi.python.org/pypi/setuptools)をご覧ください。次に以下を実行します。

    #sudo python setup.py install

## waagent サービスを再起動する

ほとんどの Linux ディストリビューションでは、次のコマンドを使用します。

    #sudo service waagent restart

Ubuntu の場合は、次のコマンドを使用します。

    #sudo service walinuxagent restart

CoreOs の場合は、次のコマンドを使用します。

    #sudo systemctl restart waagent

## Azure Linux エージェントのバージョンを確認する

    #waagent -version

CoreOS では、上記のコマンドが機能しない場合があります。

これで、Linux エージェントのバージョンが新しいバージョンに更新されたことが確認できます。

Azure Linux エージェントの詳細については、[Azure Linux エージェントの README](https://github.com/Azure/WALinuxAgent) を参照してください。

<!---HONumber=Oct15_HO4-->
<properties
	pageTitle="Azure Linux エージェントを Github で最新バージョンに更新する方法"
	description="Azure の Linux VM で使用する Azure Linux エージェントを Github で更新する方法について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="mingzhan"/>


# Azure Linux エージェントを Github で最新バージョンに更新する方法

[Azure Linux エージェント](https://github.com/Azure/WALinuxAgent)を更新するには、既に次の環境が整っている必要があります。

1. Linux VM が Azure で実行されている
2. SSH を使用してその Linux VM に接続している

> [AZURE.NOTE]Windows コンピューターでこのタスクを実行する場合は、Putty を使用して Linux マシンに SSH 接続できます。詳細については、「[Linux を実行する仮想マシンにログオンする方法](virtual-machines-linux-how-to-log-on.md)」を参照してください。

Azure での動作保証済み Linux ディストリビューションは、そのリポジトリに Azure Linux エージェント パッケージを格納しているため、可能な場合は、そのディストリビューションのリポジトリを確認して最新のバージョンをインストールしてください。

Ubuntu の場合は、次のように入力するだけでかまいません。
     
    #sudo apt-get install waagent

また CentOS に次のように入力します。

    #sudo yum install waagent

Oracle Linux の場合は、`/etc/yum.repo.d/public-yum-ol6.repo` または `/etc/yum.repo.d/public-yum-ol7.repo` ファイルでアドオンのリポジトリが有効になっていることを確認してください。

    #sudo yum install WALinuxAgent

通常、必要な手順はこれですべてですが、何らかの理由がある場合は、これを https://github.com から直接インストールする必要があります。その場合は、次の手順を実行してください。


## wget のインストール

SSH を使用して VM にログインします。

コマンド ラインで「`#sudo yum install wget`」と入力して、wget をインストールします (Redhat、CentOS、Oracle Linux Version 6.4、6.5 のように既定ではインストールされないディストリビューションもあります)。


## 最新版のダウンロード

[Github の Azure Linux エージェントのリリース](https://github.com/Azure/WALinuxAgent/releases)が記載されている Web ページを開き、2.0.12 などの最新バージョンを見つけます (「`#waagent --version`」と入力すると最新のバージョンを検索できます)。

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

次の行は、バージョン 2.0.12 を例として使用しています。

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.12/waagent  

## waagent を実行可能にする

    #chmod +x waagent

## 新しい実行可能ファイルを /usr/sbin にコピーする
    
    #sudo cp waagent /usr/sbin

CoreOs の場合は、次のコマンドを使用します。

    #sudo cp waagent /usr/share/oem/bin/
 
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



 

<!---HONumber=August15_HO6-->
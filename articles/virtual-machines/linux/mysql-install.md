---
title: Azure で MySQL を Linux VM にセットアップする | Microsoft Docs
description: Azure 上の Linux 仮想マシン (Ubuntu または Red Hat ファミリ OS) に MySQL スタックをインストールする方法について説明します
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 153bae7c-897b-46b3-bd86-192a6efb94fa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: 21ad3f9baf4b8e117f881d9a36fc606af04e17a5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158438"
---
# <a name="how-to-install-mysql-on-azure"></a>MySQL を Azure でインストールする方法
この記事では、Linux を実行している Azure 仮想マシンに MySQL をインストールして構成する方法を説明します。


> [!NOTE]
> このチュートリアルを実行するには、Linux を実行する Microsoft Azure Virtual Machine が既に存在している必要があります。 続行する前に、[Azure Linux VM チュートリアル](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を見て、VM 名を `mysqlnode`、ユーザー名を `azureuser` として、Linux VM を作成およびセットアップしてください。
> 
> 

この例では、MySQL ポートとしてポート 3306 を使用します。  

この記事では、例として MySQL5.6 のインストールにパッケージのリポジトリを使用します。 実際、MySQL5.6 は、MySQL5.5 よりもパフォーマンスが向上しています。  詳細情報は [こちら](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/)です。

## <a name="install-mysql56-on-ubuntu"></a>Ubuntu で MySQL 5.6 をインストールする
Ubuntu を実行している Linux VM を使用します。


### <a name="install-mysql"></a>MySQL をインストールする

次のように `root` ユーザーに切り替えて、MySQL Server 5.6 をインストールします。

```bash  
sudo su -
```

mysql-server 5.6 をインストールする

```bash  
apt-get update
apt-get -y install mysql-server-5.6
```

  
インストール中に、MySQL ルート パスワードを設定するよう求めるダイアログ ウィンドウが表示されるので、パスワードを設定します。
  
![image](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

確認のため、もう一度パスワードを入力します。

![image](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

### <a name="sign-in"></a>サインイン
  
MySQL サーバー のインストールが完了したら、MySQL サービスが自動的に開始されます。 `root` ユーザーを使用し、パスワードを入力して MySQL Server にサインインできます。

```bash  
mysql -uroot -p
```


### <a name="manage-the-mysql-service"></a>MySQL サービスを管理する

MySQL サービスの状態を取得する

```bash   
service mysql status
```
  
MySQL サービスを開始する

```bash  
service mysql start
```
  
MySQL サービスを停止する

```bash  
service mysql stop
```
  
MySQL サービスを再起動する

```bash  
service mysql restart
```

## <a name="install-mysql-on-red-hat-os-centos-oracle-linux"></a>Red Hat OS、CentOS、Oracle Linux に MySQL をインストールする
ここでは CentOS または Oracle Linux と Linux VM を使用します。

### <a name="add-the-mysql-yum-repository"></a>MySQL yum リポジトリを追加する
    
次のように `root` ユーザーに切り替えます:

```bash  
sudo su -
```

MySQL リリース パッケージをダウンロードして、インストールします。

```bash  
wget https://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
yum localinstall -y mysql-community-release-el6-5.noarch.rpm
```

### <a name="enable-the-mysql-repository"></a>MySQL リポジトリを有効にする
MySQL リポジトリを有効にして MySQL5.6 パッケージをダウンロードするため、以下のファイルを編集する

```bash  
vim /etc/yum.repos.d/mysql-community.repo
```

  
このファイルの各値を以下のように更新します。

```  
\# *Enable to use MySQL 5.6*
  
[mysql56-community]
name=MySQL 5.6 Community Server
  
baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
enabled=1
  
gpgcheck=1
  
gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```

### <a name="install-mysql"></a>MySQL をインストールする 

リポジトリから MySQL をインストールします。

```bash  
yum install mysql-community-server
```
  
MySQL RPM パッケージとすべての関連するパッケージがインストールされます。


## <a name="manage-the-mysql-service"></a>MySQL サービスを管理する
  
MySQL サーバーのサービスの状態を確認する:

```bash  
service mysqld status\
```
  
MySQL サーバーの既定のポートが実行されているかどうかを確認する:

```bash  
netstat  –tunlp|grep 3306
```

MySQL サーバーを起動する:

```bash
service mysqld start
```

MySQL サーバーを停止する:

```bash
service mysqld stop
```

次回システム起動時に MySQL を開始するよう設定する:

```bash
chkconfig mysqld on
```

## <a name="install-mysql-on-suse-linux"></a>MySQL を SUSE Linux にインストールする

ここでは、OpenSUSE と Linux 仮想マシンを使用します。

### <a name="download-and-install-mysql-server"></a>MySQL Server をダウンロードしてインストールする
  
以下のコマンドを使用し、ユーザー `root` に切り替えます。  

```bash  
sudo su -
```
  
MySQL Server パッケージをダウンロードしてインストールします。

```bash  
zypper update
zypper install mysql-server mysql-devel mysql
```

### <a name="manage-the-mysql-service"></a>MySQL サービスを管理する
  
MySQL サーバーの状態を確認する:

```bash  
rcmysql status
```
  
MySQL サーバーの既定のポートを確認する:

```bash  
netstat  –tunlp|grep 3306
```

MySQL サーバーを起動する:

```bash
rcmysql start
```

MySQL サーバーを停止する:

```bash
rcmysql stop
```

次回システム起動時に MySQL を開始するよう設定する:

```bash
insserv mysql
```

## <a name="next-step"></a>次のステップ
詳細については、[MySQL](https://www.mysql.com/) Web サイトを参照してください。


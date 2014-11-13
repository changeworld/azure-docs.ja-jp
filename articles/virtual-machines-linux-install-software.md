<properties urlDisplayName="Install software on VM" pageTitle="Linux 仮想マシンへのソフトウェアのインストール - Azure" metaKeywords="" description="CentOS/Red Hat または Ubuntu を使用して Azure 上の Linux 仮想マシンにソフトウェアをインストールする方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Azure 上の Linux 仮想マシンへのソフトウェアのインストール" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Azure 上の Linux 仮想マシンへのソフトウェアのインストール

Linux ディストリビューションでは、ソフトウェア "パッケージ" を使用してソフトウェアをインストールするのが一般的です。これらのパッケージは通常、`apt` や `yum` のような一連のコマンドを使用して管理します。また、パッケージを使用せずにソフトウェアをインストールすることもできます。たとえば、ソース コードの *tarball* を使用する場合です。

ここでは、いくつかの一般的な Linux ディストリビューションのパッケージ マネージャーを使用してソフトウェアをインストールする方法について説明します。インストールの手順は使用する Linux ディストリビューションによって異なります。

**注:** 環境のセットアップ方法に応じて、ここで示しているコマンドの実行 (`sudo` を使用) には root 権限が必要になる場合があります。

## CentOS/Red Hat

CentOS にはパッケージ管理用に `yum` が付属しています。このツールを使用すると、パッケージのインストール、アンインストール、更新、インストール済みパッケージの一覧表示などが可能です。これらのコマンドの構文については、この後の説明を参照してください。

### Installing

このコマンドでは、パッケージおよびそれと依存関係のあるパッケージがすべてインストールされます。依存関係に応じて、複数のパッケージがインストールされる場合があります。

    yum install [package name]

### アンインストール

このコマンドでは、マシンからパッケージがアンインストールされます。ただし、依存関係は削除されないことに注意してください。

    yum remove [package name]

### 更新しています...

このコマンドでは、パッケージが最新バージョンに更新されます。パッケージを更新するには、そのパッケージがインストールされている必要があります。

    yum update [package name]

### インストール済みパッケージの一覧表示

このコマンドでは、マシン上のすべてのインストール済みパッケージが一覧表示されます。

    yum list installed

## Ubuntu

Ubuntu にはパッケージ管理用に `apt` (Advanced Packaging Tool) が付属しています。このツールを使用すると、パッケージのインストール、アンインストール、更新、インストール済みパッケージの一覧表示などが可能です。これらのコマンドの構文については、この後の説明を参照してください。

### Installing

このコマンドでは、パッケージおよびそれと依存関係のあるパッケージがすべてインストールされます。依存関係に応じて、複数のパッケージがインストールされる場合があります。

    apt-get install [package name]

### アンインストール

このコマンドでは、マシンからパッケージがアンインストールされます。ただし、依存関係は削除されないことに注意してください。

    apt-get remove [package name]

### 更新/アップグレード

新しいバージョンにアップグレードするには、2 つのコマンドを使用する必要があります。1 つはパッケージのインデックスを更新するコマンド、もう 1 つはパッケージをアップグレードするコマンドです。次のコマンドを実行して、パッケージのインデックスを更新します。

    apt-get update

パッケージのインデックスが更新されたら、次のコマンドを実行してパッケージをアップグレードします。

    apt-get upgrade

<properties
   pageTitle="Azure CLI を使用して Azure 仮想マシンを作成する方法 |Microsoft Azure"
   description="このトピックでは、任意のプラットフォームに Azure CLI をインストールする方法、Azure CLI を使って Azure アカウントに接続する方法、Azure CLI で仮想マシンを作成する方法について説明します。"
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="dlepow"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure-services"
   ms.date="06/09/2015"
   ms.author="danlep"/>

# Azure Command-Line Interface (Azure CLI) を使用して VM を作成する
Azure CLI は、任意のプラットフォームから Azure インフラストラクチャを管理する優れた方法です。

Azure CLI をインストールして Azure サブスクリプションを持つことで、いたずらに仮想マシンを作成せずに済みます。手順を追って見ていきましょう。Azure アカウントをお持ちでない場合は、[無料版を取得してください](http://azure.microsoft.com/pricing/free-trial/)。

## Azure CLI のインストール

[Azure CLI のインストール](../xplat-cli.md#install)の手順に従ってください。

## Azure CLI を使用して Azure へ接続する

Azure CLI のインストールを個人の Azure アカウント、仕事用アカウント、学校用アカウントに紐付けることができます。相違点を理解して選択するには、「[Azure サブスクリプションに関連付ける方法](../xplat-cli.md#configure)」を参照してください。

## Azure で VM を作成および接続する

イメージを選択 (またはアップロード) し、`azure vm create` コマンドを使って仮想マシンを作成します

1. コマンドラインでイメージを選択するには、`azure vm image list` コマンドを使って利用できる仮想マシンのイメージの一覧を取得します。非常に多くのイメージがあるため、`more` を使用して結果をページングしたり、`grep` (Linux) や `findstr` (Windows) を使用してフィルター処理できます。たとえば、Linux で Ubuntu のイメージを探している場合、次のようなコマンドを使用します。

        azure vm image list | grep Ubuntu

    これでもまだ多くのイメージの一覧が表示されますので、さらにバージョンを指定して範囲を絞り込むことができます。

        azure vm image list | grep Ubuntu-14_10

    ここで、イメージを選択して `show` コマンドを使ってプロパティを詳細に表示できます。

        azure vm image show b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-ja-jp-30GB

2. 仮想マシンのイメージを選択したら、`vm create` コマンドを使ってイメージを作成します。このコマンドには多くのオプションがあります。`help` コマンドを使用して一覧表示できます。

        vm create --help

    手順 1 のイメージとあわせて、仮想マシンを作成するために必要なキーの引数には location、DNS name、user name があります。

    認証を行うには、パスワードを指定するか (コマンドラインまたは対話形式で)、証明書を使用します。パスワードの場合、8 文字以上で、大文字小文字の両方と、特殊文字 (!@#$%^&+= など) を少なくとも 1 文字含む必要があります。コマンドラインで使う場合、引用符やエスケープの特殊文字でパスワードを囲む方法も良いでしょう。

    location を選択するには、`vm location list` コマンドを使って地域を指定できます。

  選択する DNS 名は固有である必要があります (`dnsname.cloudapp.net` にマッピングされます)。コマンドラインで別にマシン名を指定していなければ、マシン名と同じになります。

    The Linux example below creates a VM in West US, opens the default SSH port 22 (the -e argument), and creates a user called `myadminuser`:

        azure vm create -e -l "West US"  my-new-cli-vm b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-ja-jp-30GB "myadminuser" "myAdm1n@passwd"

## 次のステップ

仮想マシンを実際に使用してみましょう。

上記の例では既定の SSH ポートを開いているため、仮想マシンが実行されれば簡単に接続できます。Linux コマンドラインの場合:

    ssh myadminuser@my-new-cli-vm.cloudapp.net

Azure CLI を使って Azure インフラストラクチャを管理する多くの具体例については、[Azure CLI コマンド リファレンスに関するページ](../virtual-machines-command-line-tools.md)を参照してください。

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png

<!---HONumber=July15_HO3-->
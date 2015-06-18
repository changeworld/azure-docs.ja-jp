<properties
   pageTitle="xplat-cli を使った Azure 仮想マシンの作成方法"
   description="このトピックでは、任意のプラットフォームに xplat-cli をインストールする方法、xplat-cli を使って Azure アカウントに接続する方法、xplat-cli で仮想マシンを作成する方法について説明します。"
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure-services"
   ms.date="02/20/2015"
   ms.author="rasquill"/>

# Azure クロス プラットフォーム コマンド ライン インターフェイス (xplat-cli) を使用して仮想マシンを作成します。
Xplat-cli は、任意のプラットフォームから Azure インフラストラクチャを管理する優れた方法です。

Xplat-cli をインストールして Azure サブスクリプションを持つことで、いたずらに仮想マシンを作成せずにすみます。手順を追って見ていきましょう。Azure アカウントをお持ち出ない場合、[無料版を取得](http://azure.microsoft.com/pricing/free-trial/)してください。

## xplat-cli のインストール

次の手順に従って、[xplat-cli](http://azure.microsoft.com/documentation/articles/xplat-cli/#install) をインストールします。

## xplat-cli を使って Azure に接続します。

xplat-cli のインストールを個人の Azure アカウント、仕事用アカウント、学校用アカウントに紐付けることができます。相違点を理解して選択するには、「[Azure サブスクリプションに関連付ける方法](http://azure.microsoft.com/documentation/articles/xplat-cli/#configure)」をご覧ください。

## Azure での仮想マシンの作成と接続

イメージを選択 (またはアップロード) し、 `azure vm create` コマンドを使って仮想マシンを作成します

1. コマンドラインでイメージを選択するには、 `azure vm image list` コマンドを使って利用できる仮想マシンのイメージの一覧を取得します。非常に多くのイメージがあるため、 `more` で結果をページングしたり、 `grep` (Linux) や  `findstr` (Windows) でフィルターしたりできます。たとえば、Linux で Ubuntu のイメージを探している場合、次のようなコマンドを使用します。

        azure vm image list | grep Ubuntu

    これでもまだ多くのイメージの一覧が表示されますので、さらにバージョンを指定して範囲を絞り込むことができます。

        azure vm image list | grep Ubuntu-14_10

    ここで、イメージを選択して  `show` コマンドを使ってプロパティを詳細に表示できます。

        azure vm image show b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-ja-jp-30GB

2. 仮想マシンの作成

    仮想マシンのイメージを選択したら、 `vm create` コマンドを使ってイメージを作成します。このコマンドには多くのオプションがあります。help コマンドで一覧を表示できます。

        vm create --help

    手順 1 のイメージとあわせて、仮想マシンを作成するために必要なキーの引数には location、DNS name、user name があります。

    認証を行うには、パスワードを指定するか (コマンドラインまたは対話形式で)、証明書を使用します。パスワードの場合、8 文字以上で、大文字小文字の両方と、特殊文字 (!@#$%^&+= など) を少なくとも 1 文字含む必要があります。コマンドラインで使う場合、引用符やエスケープの特殊文字でパスワードを囲む方法も良いでしょう。

    location を選択するには、 `vm location list` コマンドを使って地域を指定できます。

  選択する DNS 名は固有である必要があります ( `dnsname.cloudapp.net` にマッピングされます)。コマンドラインで別にマシン名を指定していなければ、マシン名と同じになります。  

    下記の Linux の例では、US 西部の location で仮想マシンを作成しています。また、既定の SSH ポート 22 (-e 引数) を開き、 `myadminuser` というユーザーを作成しています。

        azure vm create -e -l "West US"  my-new-cli-vm b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-ja-jp-30GB "myadminuser" "myAdm1n@passwd"

## 次のステップ

仮想マシンを実際に使用してみましょう。 

上記の例では既定の SSH ポートを開いているため、仮想マシンが実行されれば簡単に接続できます。Linux コマンドラインの場合:

    ssh myadminuser@my-new-cli-vm.cloudapp.net

xplat-cli を使って Azure インフラストラクチャを管理する多くの具体例については、「[Mac と Linux 用 Azure コマンド ライン ツール](http://azure.microsoft.com/documentation/articles/virtual-machines-command-line-tools/)」をご覧ください。

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png

<!--HONumber=47-->
 
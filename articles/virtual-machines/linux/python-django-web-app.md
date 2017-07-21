---
title: "Azure Linux VM での Django を使用した Python Web アプリ | Microsoft Docs"
description: "Linux VM を使って Azure で Django ベースの Web アプリをホストする方法について説明します。"
services: virtual-machines-linux
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-resource-manager
ms.assetid: 00ad4c2c-4316-4f9a-913f-f7f49b158db7
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: python
ms.topic: article
ms.date: 05/31/2017
ms.author: huvalo
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 6e2ab8c7da7496d0e2b567a4bdc9341adcf01552
ms.contentlocale: ja-jp
ms.lasthandoff: 06/22/2017


---
# <a name="django-hello-world-web-app-on-a-linux-vm"></a>Linux VM での Django Hello World Web アプリ
> [!div class="op_single_selector"]
> * [Windows](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [Mac/Linux](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

<br>

このチュートリアルでは、Azure Virtual Machines の Linux で Django ベースの Web サイトをホストする方法を説明します。 このチュートリアルは Azure の使用経験がなくても読むことができます。 このチュートリアルを最後まで読むと、Django ベースのアプリケーションをクラウドで稼働させることができます。

以下の項目について説明します。

* Django をホストするように Azure の仮想マシンを設定します。 このチュートリアルでは **Linux** での方法について説明しますが、Azure でホストされている Windows Server VM にも同じ方法を使うことができます。 
* 新しい Django アプリケーションを Linux で作成します。

このチュートリアルでは、基本的な Hello World Web アプリケーションをビルドする方法を示します。 このアプリケーションを Azure の仮想マシンでホストします。

次に示すのは完成したアプリケーションのスクリーンショットです。

![Azure の Hello World ページが表示されているブラウザー ウィンドウ](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

[!INCLUDE [create-account-and-vms-note](../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>Django をホストするための Azure 仮想マシンを作成して設定する

1. Ubuntu Server 14.04 LTS ディストリビューションで Azure 仮想マシンを作成する方法については、「[Azure Portal で Linux 仮想マシンを作成する](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 SSH 公開キーの代わりにパスワード認証を使うこともできます。
2. ポート 80 への受信 HTTP トラフィックを許可するようにネットワーク セキュリティ グループを編集する方法については、「[Azure Portal を使用したネットワーク セキュリティ グループの作成](../../virtual-network/virtual-networks-create-nsg-arm-pportal.md)」をご覧ください。
3. (省略可能) 既定では、新しい仮想マシンに完全修飾ドメイン名 (FQDN) は含まれていません。  FQDN を使って VM を作成する方法については、「[Windows VM 用の Azure Portal での完全修飾ドメイン名の作成](../windows/portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 このチュートリアルを行うために、このステップは必要ありません。

## <a id="setup"> </a>開発環境を設定する
> [!NOTE]
> Python をインストールする必要がある場合、またはクライアント ライブラリを使いたい場合は、[Python インストール ガイド](../../python-how-to-install.md)をご覧ください。

Ubuntu Linux VM には Python 2.7 はプレインストールされていますが、Apache または Django は付属していません。 VM に接続して Apache および Django をインストールするには、次の手順のようにします。

1. 新しいターミナル ウィンドウを開きます。
2. Azure VM に接続するには、次のコマンドを入力します。 FQDN を作成していない場合は、Azure Portal の仮想マシン概要に表示されるパブリック IP アドレスを使って接続できます。
   
       $ ssh yourusername@yourVmUrl
3. Django をインストールするには、次のコマンドを入力します。
   
       $ sudo apt-get install python-setuptools python-pip
       $ sudo pip install django
4. mod-wsgi で Apache をインストールするには、次のコマンドを入力します。
   
       $ sudo apt-get install apache2 libapache2-mod-wsgi

## <a name="create-a-new-django-app"></a>新しい Django アプリを作成する
1. SSH を使って VM にアクセスするには、前のセクションで使ったターミナル ウィンドウを開きます。
2. 新しい Django プロジェクトを作成するには、次のコマンドを入力します。
   
       $ cd /var/www
       $ sudo django-admin.py startproject helloworld
   
   `django-admin.py` スクリプトによって、Django ベースの Web サイトの基本的な構造が生成されます。
   
   * `helloworld/manage.py` を使うと、Django ベースの Web サイトに対するホスティングの開始や停止を実行できます。
   * `helloworld/helloworld/settings.py` には、アプリケーションの Django の設定が含まれています。
   * `helloworld/helloworld/urls.py` には、各 URL とそのビューとの間のマッピング コードが含まれています。
3. /var/www/helloworld/helloworld ディレクトリに、views.py という名前の新しいファイルを作成します。 このファイルには、"Hello World" ページをレンダリングするビューが含まれます。 コード エディターで、次のコマンドを入力します。
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. urls.py ファイルの内容を次のコマンドに置き換えます。
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-apache"></a>Apache を設定する
1. /etc/apache2/sites-available/helloworld.conf フォルダーに、Apache 仮想ホスト構成ファイルを作成します。 内容に次の値を設定します。 *yourVmName* は、使っているマシンの実際の名前に置き換えます (例: *pyubuntu*)。
   
       <VirtualHost *:80>
       ServerName yourVmName
       </VirtualHost>
       WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
       WSGIPythonPath /var/www/helloworld
2. サイトをアクティブにするには、次のコマンドを使います。
   
       $ sudo a2ensite helloworld
3. Apache を再起動するには、次のコマンドを使います。
   
       $ sudo service apache2 reload
4. ブラウザーに Web ページを読み込みます。
   
   ![Azure の Hello World ページが表示されているブラウザー ウィンドウ](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

## <a name="shut-down-your-azure-virtual-machine"></a>Azure 仮想マシンをシャットダウンする
このチュートリアルが終了したら、チュートリアル用に作成した Azure VM をシャットダウンするか削除することをお勧めします。 他のチュートリアルのためにリソースが解放され、Azure の使用料金がかからなくなります。



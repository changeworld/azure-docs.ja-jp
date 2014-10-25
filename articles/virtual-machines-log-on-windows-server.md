<properties linkid="manage-windows-howto-logon" urlDisplayName="Log on to a VM" pageTitle="Log on to a virtual machine running Windows Server" metaKeywords="Azure logging on vm, vm portal" description="Learn to use the Azure Management Portal to log on to a virtual machine running Windows Server." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="08/05/2014" ms.author="kathydav"></tags>

# Windows Server が実行されている仮想マシンにログオンする方法

Windows Server オペレーティング システムが実行されている仮想マシンについては、管理ポータルの [接続] ボタンを使用して、リモート デスクトップ接続を開始します。

> [WACOM.NOTE] ユーザー名またはパスワードをリセットする必要がある場合、または仮想マシンで RDP を有効にする必要がある場合、[VMAccess][VMAccess] 拡張機能を使用してそれらを実行できます。要件やトラブルシューティングのヒントについては、「[RDP または SSH で Azure 仮想マシンに接続する][RDP または SSH で Azure 仮想マシンに接続する]」を参照してください。

1.  まだサインインしていない場合は、[Azure の管理ポータル][Azure の管理ポータル]にサインインします。

2.  **[仮想マシン]** をクリックし、適切な仮想マシンを選択します。

3.  コマンド バーで、**[接続]** をクリックします。

    ![仮想マシンへのログオン][仮想マシンへのログオン]

4.  **[開く]** をクリックして、仮想マシン用に自動的に作成されたリモート デスクトップ プロトコル ファイルを使用します。

5.  **[接続]** をクリックして続行します。

    ![接続の続行][接続の続行]

6.  仮想マシンで管理アカウントのユーザー名とパスワードを入力して、**[OK]** をクリックします。これは仮想マシンの作成時に指定したユーザー名とパスワードとなります。ただし仮想マシンがドメイン コントローラーである場合を除きます。ドメイン コントローラーである場合は、このドメインのドメイン コントローラー アカウントのユーザー名とパスワードを入力します。

7.  **[はい]** をクリックして、目的の仮想マシンであることを確認します。

    ![目的の仮想マシンであることを確認][目的の仮想マシンであることを確認]

    これで、仮想マシンを他のサーバーとまったく同様に扱うことができます。

  [VMAccess]: http://go.microsoft.com/fwlink/p/?LinkId=396856
  [RDP または SSH で Azure 仮想マシンに接続する]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Azure の管理ポータル]: http://manage.windowsazure.com
  [仮想マシンへのログオン]: ./media/virtual-machines-log-on-windows-server/connectwindows.png
  [接続の続行]: ./media/virtual-machines-log-on-windows-server/connectpublisher.png
  [目的の仮想マシンであることを確認]: ./media/virtual-machines-log-on-windows-server/connectverify.png

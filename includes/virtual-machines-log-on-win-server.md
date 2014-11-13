<properties linkid="manage-windows-howto-logon" urlDisplayName="Log on to a VM" pageTitle="Windows Server が実行されている仮想マシンへのログオン" metaKeywords="Azure logging on vm, vm portal" description="Windows Server 2008 R2 が実行されている仮想マシンに Azure の管理ポータルを使用してログオンする方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Windows Server が実行されている仮想マシンにログオンする方法" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

> [WACOM.NOTE] 要件とトラブルシューティングのヒントについては、「[RDP または SSH を使用した Azure 仮想マシンへの接続][RDP または SSH を使用した Azure 仮想マシンへの接続]」を参照してください。

1.  まだサインインしていない場合は、[Azure の管理ポータル][Azure の管理ポータル]にサインインします。

2.  **[仮想マシン]** をクリックし、適切な仮想マシンを選択します。

3.  コマンド バーで、**[接続]** をクリックします。

    ![仮想マシンへのログオン][仮想マシンへのログオン]

4.  **[開く]** をクリックして、仮想マシン用に自動的に作成されたリモート デスクトップ プロトコル ファイルを使用します。

5.  **[接続]** をクリックして接続処理を続行します。

    ![接続の続行][接続の続行]

6.  仮想マシンで管理アカウントのユーザー名とパスワードを入力して、**[OK]** をクリックします。

7.  **[はい]** をクリックして、目的の仮想マシンであることを確認します。

    ![目的の仮想マシンであることを確認][目的の仮想マシンであることを確認]

    これで、仮想マシンを他のサーバーとまったく同様に扱うことができます。

  [RDP または SSH を使用した Azure 仮想マシンへの接続]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Azure の管理ポータル]: http://manage.windowsazure.com
  [仮想マシンへのログオン]: ./media/virtual-machines-log-on-win-server/connectwindows.png
  [接続の続行]: ./media/virtual-machines-log-on-win-server/connectpublisher.png
  [目的の仮想マシンであることを確認]: ./media/virtual-machines-log-on-win-server/connectverify.png

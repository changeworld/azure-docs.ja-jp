<properties urlDisplayName="Log on to a VM" pageTitle="Azure 上で Linux を実行する仮想マシンへのログオン" metaKeywords="Azure Linux vm, Linux SSH" description="Linux を実行する Azure の仮想マシンに Secure Shell (SSH) クライアントを使用してログオンする方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Linux を実行する仮想マシンにログオンする方法" authors="kathydav" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Linux を実行する仮想マシンにログオンする方法

Linux オペレーティング システムを実行する仮想マシンへのログオンには、Secure Shell (SSH) クライアントを使用します。

仮想マシンにログオンするときに使用するコンピューターには、SSH クライアントをインストールする必要があります。SSH クライアント プログラムの選択肢は多数あります。たとえば、次のプログラムを選択できます。

-   Windows オペレーティング システムを実行しているコンピューターの場合、PuTTY などの SSH クライアントを使用できます。詳細については、[PuTTY Download Page (PuTTY のダウンロード ページ)][PuTTY Download Page (PuTTY のダウンロード ページ)] を参照してください。
-   Linux オペレーティング システムを実行しているコンピューターの場合、OpenSSH などの SSH クライアントを使用できます。詳細については、「[OpenSSH (英語)][OpenSSH (英語)]」を参照してください。

> [WACOM.NOTE] さらなる要件とトラブルシューティングのヒントについては、「[RDP または SSH で Azure 仮想マシンに接続する][RDP または SSH で Azure 仮想マシンに接続する]」を参照してください。

ここでは、PuTTY プログラムを使用して仮想マシンにアクセスする手順を示します。

1.  [管理ポータル][管理ポータル]で**ホスト名**と**ポート情報**を検索します。仮想マシンのダッシュボードから必要な情報を見つけることができます。仮想マシン名をクリックし、ダッシュボードの **[概要]** セクションで **[SSH の詳細]** を探します。

    ![SSH の詳細の取得][SSH の詳細の取得]

2.  PuTTY プログラムを開きます。

3.  ダッシュボードから収集したホスト名とポート情報を入力し、**[開く]** をクリックします。

    ![PuTTY を開く][PuTTY を開く]

4.  マシンの作成時に指定したアカウントを使用して仮想マシンにログオンします。既定のユーザー名は azureuser です。

    ![仮想マシンへのログオン][仮想マシンへのログオン]

> [WACOM.NOTE] VMAccess 拡張機能では、SSH キーやパスワードを忘れてしまった場合にリセットできます。ユーザー名を忘れた場合は、拡張機能を使用して sudo 権限で新しいユーザー名を作成できます。手順については、「[VMAccess 拡張機能を使用して Linux VM のログイン資格情報をリセットする][VMAccess 拡張機能を使用して Linux VM のログイン資格情報をリセットする]」を参照してください。

これで、仮想マシンを他のサーバーとまったく同様に扱うことができます。

<!-- LINKS -->

  [PuTTY Download Page (PuTTY のダウンロード ページ)]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
  [OpenSSH (英語)]: http://www.openssh.org/
  [RDP または SSH で Azure 仮想マシンに接続する]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [管理ポータル]: http://manage.windowsazure.com
  [SSH の詳細の取得]: ./media/virtual-machines-linux-how-to-log-on/sshdetails.png
  [PuTTY を開く]: ./media/virtual-machines-linux-how-to-log-on/putty.png
  [仮想マシンへのログオン]: ./media/virtual-machines-linux-how-to-log-on/sshlogin.png
  [VMAccess 拡張機能を使用して Linux VM のログイン資格情報をリセットする]: http://go.microsoft.com/fwlink/p/?LinkId=512138

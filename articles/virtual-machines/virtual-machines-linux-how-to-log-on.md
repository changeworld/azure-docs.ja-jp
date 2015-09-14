<properties
	pageTitle="Azure 上で Linux を実行する仮想マシンへのログオン"
	description="Linux を実行する Azure の仮想マシンに Secure Shell (SSH) クライアントを使用してログオンする方法について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="rasquill"/>




#Linux を実行する仮想マシンにログオンする方法 #

Linux オペレーティング システムを実行する仮想マシンへのログオンには、Secure Shell (SSH) クライアントを使用します。

仮想マシンにログオンするときに使用するコンピューターには、SSH クライアントをインストールする必要があります。SSH クライアント プログラムの選択肢は多数あります。たとえば、次のプログラムを選択できます。

- Windows オペレーティング システムを実行しているコンピューターの場合、PuTTY などの SSH クライアントを使用できます。詳細については、[PuTTY Download Page (PuTTY のダウンロード ページ)](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) を参照してください。
- Linux オペレーティング システムを実行しているコンピューターの場合、OpenSSH などの SSH クライアントを使用できます。詳細については、「[OpenSSH (英語)](http://www.openssh.org/)」を参照してください。

>[AZURE.NOTE]要件やトラブルシューティングのヒントについては、「[RDP または SSH を使用した Azure 仮想マシンへの接続](http://go.microsoft.com/fwlink/p/?LinkId=398294)」を参照してください。

ここでは、PuTTY プログラムを使用して仮想マシンにアクセスする手順を示します。

1. **管理ポータル**で**ホスト名**と[ポート情報](http://manage.windowsazure.com)を検索します。仮想マシンのダッシュボードから必要な情報を見つけることができます。仮想マシン名をクリックし、ダッシュボードの **[概要]** セクションで **[SSH の詳細]** を探します。

	![SSH の詳細の取得](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2. PuTTY プログラムを開きます。

3. ダッシュボードから収集したホスト名とポート情報を入力し、**[開く]** をクリックします。

	![PuTTY を開く](./media/virtual-machines-linux-how-to-log-on/putty.png)

4. マシンの作成時に指定したアカウントを使用して仮想マシンにログオンします。ユーザー名とパスワードを使用して仮想マシンを作成する方法の詳細については、「[Linux を実行する仮想マシンの作成](virtual-machines-linux-tutorial.md)」を参照してください。

	![仮想マシンへのログオン](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

>[AZURE.NOTE]VMAccess 拡張機能では、SSH キーやパスワードを忘れた場合にリセットできます。ユーザー名を忘れた場合は、拡張機能を使用して sudo 権限で新しいユーザー名を作成できます。手順については、「[Linux 仮想マシンのパスワードまたは SSH をリセットする方法]」を参照してください。

これで、仮想マシンを他のサーバーとまったく同様に扱うことができます。

<!-- LINKS -->
[Linux 仮想マシンのパスワードまたは SSH をリセットする方法]: http://go.microsoft.com/fwlink/p/?LinkId=512138

<!---HONumber=September15_HO1-->
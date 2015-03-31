<properties services="virtual-machines" title="Windows Server が実行されている仮想マシンにログオンする方法" authors="KBDAzure" solutions="" manager="timlt" editor="tysonn" />

>[AZURE.NOTE] ユーザー名またはパスワードをリセットする必要がある場合、または仮想マシンで RDP を有効にする必要がある場合、[VMAccess](http://go.microsoft.com/fwlink/p/?LinkId=396856) 拡張機能を使用できます。ログオンの要件やトラブルシューティングのヒントについては、[RDP または SSH を使用した Azure 仮想マシンへの接続](http://go.microsoft.com/fwlink/p/?LinkId=398294) をご覧ください。

1. まだサインインしていない場合は、[Azure の管理ポータル](http://manage.windowsazure.com)にサインインします。

2. **[仮想マシン]** をクリックし、適切な仮想マシンを選択します。

3. コマンド バーで、**[接続]** をクリックします。

	![Log on to the virtual machine](./media/virtual-machines-log-on-win-server/connectwindows.png)

4. **[開く]** をクリックして、仮想マシン用に自動的に作成されたリモート デスクトップ プロトコル ファイルを使用します。
	
5. **[接続]** をクリックして続行します。

	![Continue with connecting](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. 仮想マシンで管理アカウントの資格情報を入力し、**[OK]** をクリックします。 

 >[AZURE.TIP] 通常、これは仮想マシンの作成時に指定したユーザー名とパスワードとなります。ユーザー名のドメイン情報が正しいことをご確認ください。

>- VM がユーザーの組織のドメインに属している場合は、ユーザー名にそのドメインの名前が含まれていることをご確認ください。
- VM がドメインに属していない場合は、ドメイン名を削除して行を '\' で開始するか、VM 名をドメイン名として使用します。たとえば、 `\MyUserName` や  `MyTestVM\MyUserName` となります。 
- VM がドメイン コントローラーである場合っは、そのドメインのドメイン管理者アカウントのユーザー名とパスワードを入力します。

**[はい]** をクリックして、目的の仮想マシンであることを確認します。

![Verify the identity of the machine](./media/virtual-machines-log-on-win-server/connectverify.png)

これで、仮想マシンをリモートで使用して作業できます。


<!--HONumber=47-->

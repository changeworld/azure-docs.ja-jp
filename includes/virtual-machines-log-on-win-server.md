<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="KBDAzure" solutions="" manager="timlt" editor="tysonn" />

4. **[接続]** をクリックすると、リモート デスクトップ プロトコル ファイル (.rdp ファイル) が作成され、ダウンロードされます。**[開く]** をクリックしてこのファイルを使用します。

5. リモート デスクトップ ウィンドウで、**[接続]** をクリックして続行します。

	![接続の続行](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Windows セキュリティ ウィンドウで、仮想マシンの管理者アカウントの資格情報を入力し、**[OK]** をクリックします。

 	>[AZURE.TIP]通常、これには仮想マシンの作成時に指定したユーザー名とパスワードを使用します。ユーザー名のドメイン情報が正しいことをご確認ください。
	>
	>- 仮想マシンがユーザーの組織のドメインに属している場合は、ユーザー名にそのドメインの名前が含まれていることをご確認ください。
	>- 仮想マシンがドメインに属していない場合は、ドメイン名を削除して行を '' で開始するか、VM 名をドメイン名として使用します。たとえば、`\MyUserName` または `MyTestVM\MyUserName` です。
	>- 仮想マシンがドメイン コントローラーである場合は、そのドメインのドメイン管理者アカウントのユーザー名とパスワードを入力します。

7.	**[はい]** をクリックして、目的の仮想マシンであることを確認し、ログオンを完了します。

	![目的の仮想マシンであることを確認](./media/virtual-machines-log-on-win-server/connectverify.png)

<!---HONumber=Sept15_HO3-->
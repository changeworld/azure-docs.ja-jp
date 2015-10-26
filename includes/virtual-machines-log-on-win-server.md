<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="cynthn" solutions="" manager="timlt" editor="tysonn" />

4. **[接続]** をクリックすると、リモート デスクトップ プロトコル ファイル (.rdp ファイル) が作成され、ダウンロードされます。**[開く]** をクリックしてこのファイルを使用します。

5. リモート デスクトップ ウィンドウで、**[接続]** をクリックして続行します。

	![接続の続行](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Windows セキュリティ ウィンドウで、仮想マシンのアカウントの資格情報を入力し、**[OK]** をクリックします。

 	ほとんどの場合は、仮想マシンの作成時に指定したローカル アカウントのユーザー名とパスワードです。この場合、ドメインは仮想マシンの名前です。これを *vmname*&#92;*username* の形式で入力します。
	
	仮想マシンがユーザーの組織のドメインに属している場合は、ユーザー名にそのドメインの名前が *Domain*&#92;*Username* の形式で含まれていることをご確認ください。さらに、アカウントは、管理者グループに属しているか、または VM へのリモート アクセス特権が付与されている必要があります。
	
	仮想マシンがドメイン コントローラーである場合は、そのドメインのドメイン管理者アカウントのユーザー名とパスワードを入力します。

7.	**[はい]** をクリックして、目的の仮想マシンであることを確認し、ログオンを完了します。

	![目的の仮想マシンであることを確認](./media/virtual-machines-log-on-win-server/connectverify.png)

<!---HONumber=Oct15_HO3-->
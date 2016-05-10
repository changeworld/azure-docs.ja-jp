<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="cynthn" solutions="" manager="timlt" editor="tysonn" />

4. **[接続]** をクリックすると、リモート デスクトップ プロトコル ファイル (.rdp ファイル) が作成され、ダウンロードされます。**[開く]** をクリックしてこのファイルを使用します。

	![Screenshot of the downloaded .rdp file.](./media/virtual-machines-log-on-win-server/open-rdp.png)

5. .rdp の発行元が不明であることを示す警告が表示されます。問題はありません。リモート デスクトップ ウィンドウで、**[接続]** をクリックして続行します。

	![Screenshot of a warning about an unknown publisher.](./media/virtual-machines-log-on-win-server/rdp-warn.png)

6. Windows セキュリティ ウィンドウで、仮想マシンのアカウントの資格情報を入力し、**[OK]** をクリックします。

 	通常、資格情報は仮想マシンの作成時に指定したローカル アカウントのユーザー名とパスワードです。この場合、ドメインは仮想マシンの名前です。これを *vmname*&#92;*username* の形式で入力します。
	
	仮想マシンがユーザーの組織のドメインに属している場合は、ユーザー名にそのドメインの名前が *Domain*&#92;*Username* の形式で含まれていることを確認してください。また、アカウントは管理者グループに属しているか、VM へのリモート アクセス特権が付与されている必要があります。
	
	仮想マシンがドメイン コントローラーである場合は、そのドメインのドメイン管理者アカウントのユーザー名とパスワードを入力します。

7.	**[はい]** をクリックして、目的の仮想マシンであることを確認し、ログオンを完了します。

	![VM の ID の検証に関するメッセージが表示されているスクリーンショット。](./media/virtual-machines-log-on-win-server/cert-warning.png)

<!---HONumber=AcomDC_0504_2016-->
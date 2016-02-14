<properties
	pageTitle="Windows VM にログインする | Microsoft Azure"
	description="Azure ポータルを使用し、リソース マネージャー デプロイメント モデルで作成された Windows 仮想マシンにログオンします。"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="cynthn"/>


# Azure ポータルを使用して Windows 仮想マシンにログオンする


Azure ポータルの **[接続]** ボタンを使用して、リモート デスクトップ セッションを開始し、Windows VM にログオンします。


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-log-on-windows-server.md)。


## 仮想マシンへの接続

1. Azure ポータルにサインインします。

2. **[Virtual Machines]** をクリックし、仮想マシンを選択します。

3. ページの下部にあるコマンド バーで、**[接続]** をクリックします。

	![仮想マシンへのログオン](./media/arm_log_on_windows_vm/rm_windows_connect.png)
	

4. **[接続]** をクリックすると、リモート デスクトップ プロトコル ファイル (.rdp ファイル) が作成され、ダウンロードされます。**[開く]** をクリックしてこのファイルを使用します。

	![仮想マシンへのログオン](./media/arm_log_on_windows_vm/rm_connect_rdp_file.png)
	
5. リモート デスクトップ ウィンドウで、**[接続]** をクリックして続行します。

	![接続の続行](./media/arm_log_on_windows_vm/rm_connect_unknown.png)

	
6. Windows セキュリティ ウィンドウで、仮想マシンのアカウントの資格情報を入力し、**[OK]** をクリックします。

 	資格情報は、ほとんどの場合、仮想マシンの作成時に指定したローカル アカウントのユーザー名とパスワードです。ドメインは仮想マシンの名前です。これは *vmname*&#92;*username* の形式で入力されます。
	
	仮想マシンがユーザーの組織のドメインに属している場合は、ユーザー名にそのドメインの名前が *Domain*&#92;*Username* の形式で含まれていることを確認してください。アカウントは管理者グループに属しているか、VM へのリモート アクセス特権が付与されている必要があります。
	
	仮想マシンがドメイン コントローラーである場合は、そのドメインのドメイン管理者アカウントのユーザー名とパスワードを入力します。

7.	**[はい]** をクリックして、目的の仮想マシンであることを確認し、ログオンを完了します。

	![目的の仮想マシンであることを確認](./media/arm_log_on_windows_vm/rm_connect_cert.png)

## 次のステップ

接続時に問題が発生した場合、[Windows ベースの Azure Virtual Machine へのリモート デスクトップ接続に関するトラブルシューティング](virtual-machines-troubleshoot-remote-desktop-connections.md)に関するページを参照してください。

<!---HONumber=AcomDC_0204_2016-->
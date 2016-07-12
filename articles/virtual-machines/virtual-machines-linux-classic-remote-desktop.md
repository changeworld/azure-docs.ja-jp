<properties
	pageTitle="リモート デスクトップを Linux VM にアタッチする | Microsoft Azure"
	description="リモート デスクトップをインストールして、Microsoft Azure Linux VM に接続するように構成する方法を説明します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/01/2016"
	ms.author="mingzhan"/>


#リモート デスクトップを使用して Microsoft Azure Linux VM に接続する

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


##概要

RDP (リモート デスクトップ プロトコル) は、Windows の専用プロトコルです。RDP を使用して Linux VM (仮想マシン) をリモート接続するにはどうすればよいでしょうか。

このガイドを読めば、その答えがわかります。 RDP は、Microsoft Azure Linux VM に xrdp をインストールして構成するのに役立ちます。また、これには Windows コンピューターからリモート デスクトップを使用して接続できます。このガイドでは、例として Ubuntu または OpenSUSE を実行する Linux VM を使用します。

xrdp はオープン ソースの RDP サーバーで、これを使用すると、Windows コンピューターからリモート デスクトップを使用して Linux サーバーに接続できます。このパフォーマンスは、VNC (Virtual Network Computing) よりも優れています。VNC には "JPEG" 品質と動作が遅いという欠点があるのに対し、RDP は高速かつ明瞭です。


> [AZURE.NOTE] Linux を実行している既存の Microsoft Azure VM が必要です。Linux VM を作成してセットアップするには、[Azure Linux VM のチュートリアル](virtual-machines-linux-classic-createportal.md)を参照してください。


##リモート デスクトップ用のエンドポイントを作成する
このドキュメントでは、リモート デスクトップに既定のエンドポイント 3389 を使用します。そのため、次のように、Linux VM に対してリモート デスクトップとしてエンドポイント 3389 を設定します。


![イメージ](./media/virtual-machines-linux-classic-remote-desktop/no1.png)


VM にエンドポイントを設定する方法がわからない場合は、[ガイダンス](virtual-machines-linux-classic-setup-endpoints.md)を参照してください。


##Gnome デスクトップをインストールする

Putty を使用して Linux VM に接続し、`Gnome Desktop` をインストールします。

Ubuntu の場合は、次のコマンドを使用します。

	#sudo apt-get update
	#sudo apt-get install ubuntu-desktop


OpenSUSE の場合は、次のコマンドを使用します。

	#sudo zypper install gnome-session

##xrdp をインストールする

Ubuntu の場合は、次のコマンドを使用します。

	#sudo apt-get install xrdp

OpenSUSE の場合は、次のコマンドを使用します。

> [AZURE.NOTE] 次のコマンドでは、OpenSUSE のバージョンを現在使用中のバージョンに更新してください。`OpenSUSE 13.2` のコマンドの例を次に示します。

	#sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


##xrdp を開始して起動時に xdrp サービスを設定する

OpenSUSE の場合は、次のコマンドを使用します。

	#sudo systemctl start xrdp
	#sudo systemctl enable xrdp

Ubuntu の場合は、インストール後の自動起動時に xrdp が開始され、有効になります。

##Ubuntu 12.04LTS 以降のバージョンの Ubuntu を使用している場合に xfce を使用する

現在の xrdp では Ubuntu 12.04LTS 以降のバージョンの Ubuntu から Gnome デスクトップへの接続がサポートされていないため、代わりに `xfce` のデスクトップを使用します。

`xfce` をインストールするには、次のコマンドを使用します。

    #sudo apt-get install xubuntu-desktop

`xfce` を有効にするには、次のコマンドを使用します。

    #echo xfce4-session >~/.xsession

構成ファイル `/etc/xrdp/startwm.sh` を編集するには、次のコマンドを使用します。

    #sudo vi /etc/xrdp/startwm.sh   

行 `/etc/X11/Xsession` の前に行 `xfce4-session` を追加します。

xrdp サービスを再起動するには、次のコマンドを使用します。

    #sudo service xrdp restart


##Windows コンピューターから Linux VM を接続する
Windows コンピューターでリモート デスクトップ クライアントを起動し、Linux VM の DNS 名を入力するか、Azure クラシック ポータルで VM の`Dashboard`に移動して [`Connect`] をクリックすると、Linux VM に接続され、次のようなログイン ウィンドウが表示されます。

![イメージ](./media/virtual-machines-linux-classic-remote-desktop/no2.png)

Linux VM の `user` と `password` を使用してログインすると、Microsoft Azure Linux VM からリモート デスクトップを今すぐ利用できるようになります。


##次へ
xrdp の使用の詳細については、[こちら](http://www.xrdp.org/)を参照してください。

<!---HONumber=AcomDC_0629_2016-->
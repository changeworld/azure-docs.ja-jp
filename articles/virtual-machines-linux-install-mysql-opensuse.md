<properties linkid="manage-linux-common-task-mysql-virtual-machine" urlDisplayName="MySQL のインストール" pageTitle="Azure 上の Linux 仮想マシンへの MySQL のインストール" metaKeywords="Azure vm OpenSUSE, Linux vm" description="OpenSUSE Linux を実行する Azure の仮想マシンを作成し、SSH または PuTTY を使用して MySQL をその仮想マシンにインストールする方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Azure 上で OpenSUSE Linux を実行する仮想マシンへの MySQL のインストール" authors="" solutions="" manager="" editor="" />




#Azure 上で OpenSUSE Linux を実行する仮想マシンへの MySQL のインストール

[MySQL](http://www.mysql.com) は広く普及しているオープン ソースの SQL データベースです。[Azure の管理ポータル][AzurePreviewPortal]では、OpenSUSE Linux を実行する仮想マシンをイメージ ギャラリーから作成できます。この仮想マシンには、MySQL データベースをインストールして構成することができます。

このチュートリアルでは、次のことを説明します。

- 管理ポータルを使用してギャラリーから OpenSUSE Linux 仮想マシンを作成する方法。

- SSH または PuTTY を使用して仮想マシンに接続する方法。

- 仮想マシンに MySQL をインストールする方法。

##OpenSUSE Linux を実行する仮想マシンの作成

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

##仮想マシンへの MySQL のインストールと実行

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

##まとめ

このチュートリアルでは、OpenSUSE Linux 仮想マシンを作成し、SSH または PuTTY を使用してその仮想マシンにリモート接続する方法について説明しました。Linux 仮想マシンに MySQL をインストールして構成する方法についても説明しました。MySQL の詳細については、[MySQL のドキュメント](http://dev.mysql.com/doc/)を参照してください。

[AzurePreviewPortal]: http://manage.windowsazure.com


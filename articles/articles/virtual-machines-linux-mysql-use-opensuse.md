<properties linkid="virtual-machines-linux-mysql-use-opensuse" urlDisplayName="MongoDB のインストール" pageTitle="Azure 上で CentOS Linux を実行する仮想マシンへの MongoDB のインストール" metaKeywords="Azure、MongoDB" description="Azure の仮想マシンに MongoDB をインストールする方法について説明します。" metaCanonical="" services="" documentationCenter="" title="Azure 上で CentOS Linux を実行する仮想マシンへの MongoDB のインストール" authors="" solutions="" manager="" editor="" />

#Azure 上で OpenSUSE Linux を実行する仮想マシンへの MySQL のインストール

[MySQL][MySQL] は広く普及しているオープン ソースの SQL データベースです。[Azure 管理ポータル][AzurePortal]では、OpenSUSE Linux を実行する仮想マシンをイメージ ギャラリーから作成できます。この仮想マシンには、MySQL データベースをインストールして構成することができます。

このチュートリアルでは、次のことを説明します。

- 管理ポータルを使用してギャラリーから OpenSUSE Linux 仮想マシンを作成する方法。
- SSH または PuTTY を使用して仮想マシンに接続する方法。
- 仮想マシンに MySQL をインストールする方法。

##仮想マシン プレビュー機能へのサインアップ

仮想マシンを作成するには、Azure 仮想マシン プレビュー機能にサインアップする必要があります。Azure アカウントを持っていない場合は、無料の試用アカウントにサインアップすることもできます。

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## OpenSUSE Linux を実行する仮想マシンの作成

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

##仮想マシンへの MySQL のインストールと実行

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

##まとめ
このチュートリアルでは、OpenSUSE Linux を実行する仮想マシンを作成し、SSH または PuTTY を使用してその仮想マシンにリモート接続する方法について説明しました。Linux 仮想マシンに MySQL をインストールして構成する方法についても説明しました。MySQL の詳細については、[MySQL のドキュメント][MySQLDocs]を参照してください。

[MySQLDocs]: http://dev.mysql.com/doc/
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com


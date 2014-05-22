<properties linkid="store-mongodb-virtual-machines-linux-centos" urlDisplayName="MongoDB のインストール" pageTitle="Azure 上で CentOS Linux を実行する仮想マシンへの MongoDB のインストール" metaKeywords="Azure, MongoDB" description="Azure の仮想マシンに MongoDB をインストールする方法について説明します。" metaCanonical="" services="" documentationCenter="" title="Azure 上で CentOS Linux を実行する仮想マシンへの MongoDB のインストール" authors="" solutions="" manager="" editor="" />

# Azure 上で CentOS Linux を実行する仮想マシンへの MongoDB のインストール

[MongoDB][MongoDB] は、高いパフォーマンスを特徴とし、広く普及しているオープン ソースの NoSQL データベースです。[Azure の管理ポータル][AzurePortal]では、CentOS Linux を実行する仮想マシンをイメージ ギャラリーから作成できます。この仮想マシンには、MongoDB データベースをインストールして構成することができます。

学習内容:

- 管理ポータルを使用して CentOS Linux を実行する仮想マシンをギャラリーから作成する方法。
- SSH または PuTTY を使用して仮想マシンに接続する方法。
- 仮想マシンに MongoDB をインストールする方法。

## 仮想マシン プレビュー機能へのサインアップ

仮想マシンを作成するには、Azure 仮想マシン プレビュー機能にサインアップする必要があります。Azure アカウントを持っていない場合は、無料の試用アカウントにサインアップすることもできます。

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## CentOS Linux を実行する仮想マシンの作成

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal](../includes/create-and-configure-centos-vm-in-portal.md)]

##仮想マシンへの MongoDB のインストールと実行

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm](../includes/install-and-run-mongo-on-centos-vm.md)]

##まとめ
このチュートリアルでは、Linux 仮想マシンを作成し、SSH または PuTTY を使用してその仮想マシンにリモート接続する方法について説明しました。Linux 仮想マシンに MongoDB をインストールして構成する方法についても説明しました。MongoDB の詳細については、[MongoDB のドキュメント][MongoDocs]を参照してください。

[MongoDocs]: http://www.mongodb.org/display/DOCS/Home
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com


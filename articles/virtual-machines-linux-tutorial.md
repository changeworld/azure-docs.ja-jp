<properties 
	pageTitle="Azure 上で Linux を実行する仮想マシンの作成" 
	description="Azure のイメージを使用して、Linux を実行する Azure 仮想マシン (VM) を作成する方法について説明します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/19/2015" 
	ms.author="kathydav"/>

#Linux を実行する仮想マシンの作成 

Azure の管理ポータルのイメージ ギャラリーを使用すると、Linux を実行する仮想マシンを簡単に作成できます。このガイドでは、Azure の使用経験がない方を対象に、その手順を説明します。

> [AZURE.NOTE] このチュートリアルは、Azure VM の使用経験がなくても完了できますが、Azure アカウントは必要です。数分で無料の試用アカウントを作成することができます。詳細については、[Azure アカウントの作成](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/) を参照してください。 

このチュートリアルの内容: 

- [Azure での仮想マシンについて] []
- [仮想マシンを作成する方法] []
- [仮想マシンを作成後、ログオンする方法] []
- [新しい仮想マシンにデータ ディスクを接続する方法] []

**重要**: このチュートリアルでは、仮想ネットワークに接続されていない仮想マシンを作成します。仮想マシンで仮想ネットワークを使用する場合、仮想マシンの作成時に仮想ネットワークを指定する必要があります。仮想ネットワークの詳細については、[仮想ネットワークの概要](http://go.microsoft.com/fwlink/p/?LinkID=294063) を参照してください。

## <a id="virtualmachine"> </a>Azure での仮想マシンについて ##

Azure の仮想マシンは、制御と管理が可能なクラウド上のサーバーです。Azure 上に仮想マシンを作成した後、仮想マシンは必要に応じていつでも削除と再作成が可能で、社内のサーバーとまったく同様にアクセスできます。仮想マシンの作成には、仮想ハード ディスク (VHD) ファイルが使用されます。仮想マシンには、次の種類の VHD が使用されます。

- **イメージ** - 新しい仮想マシンを作成するテンプレートとして使用される VHD。イメージがテンプレートなのは、実行中の仮想マシンとは違って、コンピューター名やユーザー アカウント設定のような具体的な設定がなされていないためです。イメージを使って仮想マシンを作成した場合、新しい仮想マシン用のオペレーティング システム ディスクが自動的に作成されます。
- **ディスク** - ディスクは、起動してオペレーティング システムの実行中バージョンとしてマウントできる VHD です。イメージは、プロビジョニングするとディスクになります。イメージを使って仮想マシンを作成すると、ディスクが常に作成されます。仮想ハードウェアに接続され、サービスの一部として実行されている VHD はディスクです。

イメージを使用して仮想マシンを作成するためには、次の選択肢があります。

- Azure の管理ポータルのイメージ ギャラリーに用意されているイメージを使って仮想マシンを作成します。
- イメージを格納した .vhd ファイルを作成し、それを Azure にアップロードして、そのイメージを使って仮想マシンを作成します。カスタム イメージの作成とアップロードの詳細については、[Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード](/ja-jp/manage/linux/common-tasks/upload-a-vhd/) を参照してください。

各仮想マシンは、その仮想マシンだけがクラウド サービス内に配置されているか、他の仮想マシンと共にグループ化されクラウド サービス内に配置されています。同じクラウド サービス内に複数の仮想マシンを配置すると、仮想マシン間の相互通信、仮想マシン間でのネットワーク トラフィックの負荷分散、仮想マシンの高可用性を有効にできます。クラウド サービスと仮想マシンの詳細については、[Azure 入門](http://go.microsoft.com/fwlink/p/?LinkId=311926) に関するページの「実行モデル」のセクションを参照してください。

## <a id="custommachine"> </a>仮想マシンを作成する方法 ##

このチュートリアルでは、**[ギャラリーから]** を使って仮想マシンを作成します。 **[簡易作成]** を使う場合よりも選べるオプションが多いためです。必要に応じて、接続されたリソース、DNS 名、ネットワーク接続を選択できます。

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../includes/virtual-machines-create-LinuxVM.md)]

[AZURE.INCLUDE [virtual-machines-Linux-tutorial-log-on-attach-disk](../includes/virtual-machines-Linux-tutorial-log-on-attach-disk.md)]

> [AZURE.NOTE] 識別用の SSH キーを使用して Linux 仮想マシンに接続することもできます。SSH キーとの接続を構成する方法の詳細については、[Azure 上の Linux における SSH の使用方法] を参照してください(./virtual-machines-linux-use-ssh-key/)。

##次のステップ 

Azure 上の Linux の詳細については、次の記事を参照してください。

- [Azure での Linux 入門](http://azure.microsoft.com/documentation/articles/introduction-linux/)

- [Mac および Linux 用 Azure コマンド ライン ツールの使用方法](http://azure.microsoft.com/documentation/articles/xplat-cli/)

- [Azure の仮想マシンの構成設定について](http://msdn.microsoft.com/library/azure/dn763935.aspx)

- [Azure の Linux 用 Docker 仮想マシン拡張機能](../virtual-machines-docker-vm-extension/)


[次のステップ]: #next
[Azure での仮想マシンについて]: #virtualmachine
[仮想マシンを作成する方法]: #custommachine
[仮想マシンを作成後、ログオンする方法]: #logon
[新しい仮想マシンにデータ ディスクを接続する方法]: #attachdisk


<!--HONumber=42-->

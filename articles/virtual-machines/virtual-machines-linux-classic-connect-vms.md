<properties
	pageTitle="クラウド サービスで Linux VM を接続する | Microsoft Azure"
	description="クラシック デプロイ モデルで作成された Linux 仮想マシンを、Azure クラウド サービスまたは仮想ネットワークに接続します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="cynthn"/>

# 仮想ネットワークまたはクラウド サービスで構成されるクラシック デプロイ モデルで作成された Linux 仮想マシンに接続します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

クラシック デプロイ モデルで作成された Linux 仮想マシンは、常にクラウド サービス内に配置されます。クラウド サービスはコンテナーとして機能し、インターネット経由で仮想マシンにアクセスするために、一意のパブリック DNS 名、パブリック IP アドレス、一連のエンドポイントを提供します。クラウド サービスは仮想ネットワークに配置できますが、必須ではありません。[Windows 仮想マシンを仮想ネットワークまたはクラウド サービスと接続する](virtual-machines-windows-classic-connect-vms.md)こともできます。

クラウド サービスが仮想ネットワーク内に含まれていない場合は、*スタンドアロン* クラウド サービスといいます。スタンドアロン クラウド サービス内にある仮想マシンが他の仮想マシンと通信するには、他の仮想マシンのパブリック DNS 名を使用する必要があり、そのトラフィックはインターネットを介して送信されます。クラウド サービスが仮想ネットワークにある場合、そのクラウド サービス内にある仮想マシンは、トラフィックをインターネットに送信することなく、仮想ネットワーク内の他のすべての仮想マシンと通信できます。

同じスタンドアロン クラウド サービスに仮想マシンを配置する場合は、負荷分散と可用性セットを引き続き使用できます。詳細については、[仮想マシンの負荷分散](virtual-machines-linux-load-balance.md)に関するページと、「[仮想マシンの可用性管理](virtual-machines-linux-manage-availability.md)」を参照してください。ただし、仮想マシンをサブネットでまとめることや、スタンドアロン クラウド サービスをオンプレミスのネットワークに接続することはできません。次に例を示します。

[AZURE.INCLUDE [virtual-machines-common-classic-connect-vms](../../includes/virtual-machines-common-classic-connect-vms.md)]

## 次のステップ

仮想マシンを作成したら、サービスやワークロードがデータを格納するための場所として、[データ ディスクを追加する](virtual-machines-linux-classic-attach-disk.md)ことをお勧めします。

<!---HONumber=AcomDC_0706_2016-->
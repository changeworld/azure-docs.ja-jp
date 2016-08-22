<properties
 pageTitle="A8、A9、A10、A11 VM サイズと Linux について | Microsoft Azure"
 description="Linux VM の Azure A8、A9、A10、A11 という多くのコンピューティング処理を要するサイズの背景情報および使用上の注意事項について説明します。"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="08/04/2016"
 ms.author="danlep"/>

# A8、A9、A10、A11 コンピューティング集中型インスタンスについて 

この記事では、*コンピューティング集中型*インスタンスとも呼ばれる Azure A8、A9、A10、A11 インスタンスの背景情報と使用上の考慮事項を示します。この記事は、Linux VM のこれらのインスタンスの使用方法について説明していますが、[Windows VM](virtual-machines-windows-a8-a9-a10-a11-specs.md) にも適用されます。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## RDMA ネットワークへのアクセス

1 つのクラウド サービスまたは可用性セット内で、次のサポートされる Linux HPC ディストリビューションとサポートされる MPI 実装のいずれかを実行するサイズ A8 と A9 Linux VM のクラスターは、Linux MPI アプリケーションを実行する Azure 内の RDMA ネットワークにアクセスできます。デプロイのオプションとサンプルの構成手順については、「[MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](virtual-machines-linux-classic-rdma-cluster.md)」を参照してください。

* **ディストリビューション** - Azure Marketplace イメージからデプロイされた、SUSE Linux Enterprise Server (SLES) 12 for HPC、SLES 12 for HPC (Premium)、CentOS ベースの 7.1 HPC、または CentOS ベースの 6.5 HPC

* **MPI** - Intel MPI Library 5.x

    >[AZURE.NOTE] Intel MPI 5.1 は、Marketplace の CentOS ベースの HPC イメージに既にインストールされています。SLES 12 HPC VM で Intel MPI を使用するには、別にインストールする必要があります。

現在、Azure Linux RDMA ドライバーがインストールされるのは、RDMA 対応の SLES 12 HPC と CentOS HPC イメージを Azure Marketplace からデプロイした場合のみです。デプロイした他の Linux VM にドライバーをインストールすることはできません。

>[AZURE.NOTE]Marketplace から CentOS ベースの HPC イメージでは、**yum** 構成ファイルでのカーネルの更新は無効にされています。これは、Linux RDMA ドライバーが RPM パッケージとして配布されており、カーネルが更新された場合にドライバーの更新プログラムが機能しない可能性があるためです。


## SLES 12 の RDMA ドライバーの更新プログラム
SLES 12 HPC イメージに基づいて VM を作成したら、RDMA ネットワーク接続のために VM の RDMA ドライバーを更新する必要があります。

>[AZURE.IMPORTANT]この手順は、すべての Azure リージョンの SLES 12 HPC VM デプロイに**必要です**。CentOS ベースの 7.1 HPC または 6.5 HPC VM をデプロイした場合、この手順は必要ありません。

ドライバーを更新する前に、すべての **zypper** プロセス、または VM の SUSE リポジトリ データベースをロックするプロセスを停止します。そうしないと、ドライバーが正しく更新されない場合があります。

クライアント コンピューターで次の Azure CLI コマンド セットのいずれかを実行して、各 VM の Linux RDMA ドライバーを更新します。

**従来のデプロイ モデルでプロビジョニングされた SLES 12 HPC VM の場合**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**Resource Manager デプロイメント モデルでプロビジョニングされた SLES 12 HPC VM の場合**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

>[AZURE.NOTE]ドライバーのインストールには時間がかかる場合があります。また、コマンドは出力なしで終了します。更新後、VM は再起動され、数分で使用できるようになります。

### ドライバー更新プログラムのサンプル スクリプト

SLES 12 HPC VM のクラスターがある場合、クラスターのすべてのノードのドライバー更新をスクリプトにすることができます。たとえば、次のスクリプトは、8 ノード クラスター内のドライバーを更新します。

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## HPC Pack および Linux を使用する場合の考慮事項

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) は、Microsoft の無料の HPC クラスターおよびジョブ管理ソリューションです。最新リリースの HPC Pack 2012 R2 では、Azure VM にデプロイされ、Windows Server ヘッド ノードで管理されるコンピューティング ノードで、複数の Linux ディストリビューションを実行できるようになりました。A8 または A9 の VM でデプロイされており、サポートされている MPI 実装を実行している Linux コンピューティング ノードでは、RDMA ネットワークにアクセスする MPI アプリケーションを実行できます。作業を開始するには、「[チュートリアル: Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](virtual-machines-linux-classic-hpcpack-cluster.md)」を参照してください。

## ネットワーク トポロジに関する考慮事項

* Azure のサイズ A8 または A9 Linux VM の場合、Eth1 は RDMA のネットワーク トラフィック用に予約されています。Eth1 設定や、このネットワークを参照する構成ファイル内の情報を変更しないでください。Eth0 は Azure の通常のネットワーク トラフィック用に予約されています。

* Azure では、IP over Infiniband (IB) はサポートされません。RDMA over IB のみがサポートされます。


## 次のステップ

* A8、A9、A10、および A11 インスタンスの可用性と価格の詳細については、「[Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux)」を参照してください。

* ストレージの容量とディスクの詳細については、「[仮想マシンのサイズ](virtual-machines-linux-sizes.md)」を参照してください。

* Linux 上の RDMA により A8 および A9 インスタンスのデプロイと使用を開始する方法については、「[MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](virtual-machines-linux-classic-rdma-cluster.md)」を参照してください。

<!---HONumber=AcomDC_0810_2016-->
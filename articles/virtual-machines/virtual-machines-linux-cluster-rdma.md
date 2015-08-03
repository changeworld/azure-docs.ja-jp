<properties
 pageTitle="MPI アプリケーションを実行するように Linux RDMA クラスターを設定する |Microsoft Azure"
 description="RDMA を使用して MPI アプリケーションを実行する、 サイズ A8 または A9 VM の Linux クラスターを作成する方法について説明します。"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="07/17/2015"
 ms.author="danlep"/>

# MPI アプリケーションを実行するように Linux RDMA クラスターを設定する

この記事では、Azure で[サイズ A8 および A9 の仮想マシン](virtual-machines-a8-a9-a10-a11-specs.md)を使用して Linux RDMA クラスターを設定し、並列 Message Passing Interface (MPI) アプリケーションを実行する方法について説明します。サイズ A8 および A9 の Linux ベースの VM を構成して、サポートされている MPI 実装を実行すると、MPI アプリケーションは、リモート ダイレクト メモリ アクセス (RDMA) テクノロジに基づく Azure の低待機時間で高スループットのネットワークを介して効率的に通信します。

>[AZURE.NOTE]Azure Linux RDMA は、現在、SUSE Linux Enterprise Server 12 (SLES 12) で実行されている Intel MPI Library バージョン 5.0 でサポートされています。
>
> Azure は、RDMA バックエンド ネットワークに接続せずに、A8 および A9 インスタンスと同じ処理機能を持つ、A10 および A11 のコンピューティング集中型インスタンスも提供します。Azure で MPI ワークロードを実行する場合、一般に、A8 および A9 インスタンスで最良のパフォーマンスが得られます。


## Linux クラスターのデプロイ オプション

ジョブ スケジューラを使用する場合、または使用しない場合に、Linux の RDMA クラスターの作成に使用できる方法を次に示します。

* **HPC Pack** - Azure で Microsoft HPC Pack クラスターを作成して、サポートされる Linux ディストリビューションを実行するコンピューティング ノードを追加することができます (HPC Pack 2012 R2 Update 2 以降でサポート)。Linux のノードの一部を構成して、RDMA ネットワークにアクセスできます。使用を開始するには、[HPC Pack のドキュメント](http://go.microsoft.com/fwlink/?LinkId=617894)を参照してください。

* **Azure CLI スクリプト** - Mac、Linux、および Windows 用の [Azure コマンド ライン インターフェイス](../xplat-cli.md) (CLI) を使用して独自のスクリプトを作成し、仮想ネットワークやその他の必要なコンポーネントをデプロイして、Linux クラスターを作成することができます。Azure サービス管理 (asm) モード の CLI では、クラスター ノードを順番にデプロイします。そのため、多くのコンピューティング ノードをデプロイしている場合は、デプロイの完了に数分かかる場合があります。例については、この記事の残りの部分に示す手順を参照してください。

* **Azure Resource Manager テンプレート** - Azure リソース マネージャーで簡単な JSON テンプレート ファイルを作成し、arm モードの Azure CLI でコマンドを実行 (または Azure プレビュー ポータルを使用) すると、複数の A8 および A9 Linux VM をデプロイするとともに、仮想ネットワーク、静的 IP アドレス、DNS の設定、その他のリソースを定義して、RDMA の利用および MPI ワークロードの実行が可能なコンピューティング クラスターを作成することができます。[独自のテンプレートを作成](../resource-group-authoring-templates.md)するか、または [「Azure クイック スタート テンプレート」ページ](https://azure.microsoft.com/documentation/templates/)で Microsoft またはコミュニティから提供されたテンプレートを確認して、目的のソリューションをデプロイすることができます。リソース マネージャーのテンプレートは、一般に、Linux クラスターを展開するための最も信頼性の高い、最速の方法を提供します。

## Azure CLI スクリプトによる Azure サービス管理でのデプロイ

次の手順は、Azure CLI を使用して、SLES 12 VM をデプロイし、Intel MPI Library およびその他のカスタマイズをインストールして、カスタム VM イメージを作成し、A8 または A9 VM のクラスターをデプロイするスクリプトを作成する際に役立ちます。

### 前提条件

* **クライアント コンピューター** - Azure と通信するための Mac、Linux、または Windows ベースのクライアント コンピューターが必要です。

* **Azure サブスクリプション** - アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。

* **コア クォータ** - A8 または A9 VM のクラスターをデプロイするために、コア クォータを増やすことが必要な場合があります。たとえば、8 個の A9 VM をデプロイする場合は、少なくとも 128 コアが必要です。クォータを増やすには、[オンライン カスタマー サポートに申請](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) (無料) してください。

* **Azure CLI** - クライアント コンピューターに Azure CLI を[インストール](../xplat-cli-install.md)して[構成](../xplat-cli-connect.md)し、Azure サブスクリプションを使用します。


### SLES 12 VM のプロビジョニング

Azure CLI を使用して Azure にログインした後に `azure config list` コマンドを実行し、CLI が Azure サービス管理 (asm) モードであることを確認します。asm モードでない場合は、次のコマンドを実行して asm モードに設定します。

```
azure config mode asm
```

使用する権限があるすべてのサブスクリプションを一覧表示するには、次を入力します。

```
azure account list
```

現在のアクティブなサブスクリプションは、`Current` が `true` に設定されています。クラスターの作成に使用するサブスクリプションがこれではない場合、適切なサブスクリプション番号をアクティブなサブスクリプションとして設定します。

```he
azure account set <subscription-number>
```

Azure でパブリックに利用可能な SLES 12 HPC イメージを表示するには、次のようなコマンドを実行します (シェル環境で **grep** がサポートされている場合)。

```
azure vm image list | grep "suse.*hpc"
```

>[AZURE.NOTE]SLES 12 HPC イメージは、必要な Azure 用 Linux RDMA ドライバーを使用して事前構成されています。

次のようなコマンドを実行して、使用可能な SLES 12 HPC イメージを使用し、サイズ A9 VM をプロビジョニングします。

```
azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 10004 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708
```

各値の説明:

* サイズには A8 または A9 (この例では A9) を指定できます。

* 外部 SSH ポート番号 (この例では 10004) は任意の有効なポート番号です。内部 SSH ポート番号は 22 に設定されます。

* 新しいクラウド サービスは、場所で指定された Azure リージョンに作成されます。A8 および A9 インスタンスに使用できる、"米国西部" などの場所を指定します。

* イメージ名は、現在、`b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` (無料) または `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-priority-v20150708` (SUSE 優先サポート。料金がかかります) です。

### VM のカスタマイズ

VM のプロビジョニングの完了後、VM の外部 IP アドレス (または DNS 名) と構成済みポート番号を使用して VM に SSH 接続し、カスタマイズします。接続の詳細については、[「Linux を実行する仮想マシンにログオンする方法」](virtual-machines-linux-how-to-log-on.md)を参照してください。

>[AZURE.NOTE]Microsoft Azure では Linux VM にルート アクセスが提供されません。ユーザーとして接続されているときに `sudo –s` を使用して、管理アクセス権を取得できます。

**更新プログラム** - **zypper** および NFS ユーティリティを使用して、更新プログラムをインストールできます。

>[AZURE.IMPORTANT]現時点では、カーネルの更新プログラムを適用しないことをお勧めします。適用した場合、Linux RDMA ドライバーに関連する問題が発生する可能性があります。

**Intel MPI** - Intel MPI Library 5.0 ランタイムを [Intel.com のサイト](https://software.intel.com/ja-jp/intel-mpi-library/)からダウンロードしてインストールします。Intel に登録した後、確認の電子メールに含まれる、関連 Web ページへのリンクをクリックし、適切なバージョンの Intel MPI (.tgz ファイル) のダウンロード リンクをコピーします。

次のようなコマンドを実行して、Intel MPI を VM にインストールします。

```

$ wget <download link for your registration>
$ tar xvzf <tar-file>
$ cd <mpi-directory>
$ sudo ./install.sh
```

**メモリのロック** - MPI コードによって、RDMA で使用可能なメモリをロックするには、/etc/security/limits.conf ファイルで、次の設定を追加または変更する必要があります。

```
<User or group name> hard    memlock <memory required for your application in KB>
<User or group name> soft    memlock <memory required for your application in KB>
```

>[AZURE.NOTE]テスト目的で、memlock を無制限に設定することもできます。(例: `<User or group name>    hard    memlock unlimited`)。


**SSH キー** - クラスター内のすべてのコンピューティング ノード間で、この VM の作成に使用したユーザー名とパスワードの信頼関係を確立します。次のコマンドを使用して、SSH キーを作成します。

```
$ ssh-keygen
```

既定の場所に公開キーを保存します。入力したパスフレーズを忘れないでください。

```
$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

/.ssh ディレクトリにある、ssh_config ファイルを編集 (存在しない場合は作成) します。Azure で使用するプライベート ネットワークの IP アドレスの範囲を指定します。

```
host 10.32.0.*
StrictHostKeyChecking no
```

または、クラスター内の各 VM のプライベート ネットワーク IP アドレスを、次のように一覧表示できます。

```
host 10.32.0.1
 StrictHostKeyChecking no
host 10.32.0.2
 StrictHostKeyChecking no
host 10.32.0.3
 StrictHostKeyChecking no
```

>[AZURE.NOTE]特定の IP アドレスまたは範囲を指定しない場合、上記のように `StrictHostKeyChecking no` と構成すると、潜在的なセキュリティ リスクが生じる可能性があります。

**アプリケーション** - イメージをキャプチャする前に、必要なアプリケーションをこの VM にコピーしたり、その他のカスタマイズを実行したりします。

### イメージのキャプチャ

イメージをキャプチャするには、最初に、Linux VM で次のコマンドを実行します。

```
$ sudo waagent -deprovision
```

次に、クライアント コンピューターで次の Azure CLI コマンドを実行し、イメージをキャプチャします。詳細については、[「テンプレートとして使用するために Linux 仮想マシンをキャプチャする方法」](virtual-machines-linux-capture-image.md)を参照してください。

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

これらのコマンドを実行した後、使用する VM イメージがキャプチャされ、VM が削除されます。カスタム イメージをクラスターにデプロイする準備ができました。

### イメージを使用したクラスターの展開

次のスクリプトの該当部分を環境に合った適切な値に変更し、クライアント コンピューターで実行します。asm モードのデプロイ方法では VM を順番にデプロイするため、このスクリプトで示されている 8 個の A9 VM をデプロイするまでに数分かかります。

```
### Script to create a compute cluster without a scheduler in a VNet in Azure
### Create a custom private network in Azure
### Replace 10.32.0.0 with your virtual network address space
### Replace <network-name> with your network identifier
### Select a region where A8 and A9 VMs are available, such as West US
### See Azure Pricing pages for prices and availability of A8 and A9 VMs

azure network vnet create -l "West US" –e 10.32.0.0 <network-name>

### Create a cloud service. All the A8 and A9 instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
### Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

### Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

### Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

### In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i <image-name>
done

### Save this script and run it at the CLI prompt to provision your cluster
```

## Intel MPI の構成と実行

Azure Linux RDMA で MPI アプリケーションを実行するには、Intel MPI に固有な特定の環境変数を構成する必要があります。変数を構成し、アプリケーションを実行するサンプル スクリプトを示します。

```
#!/bin/bash -x
source /opt/intel/impi_latest/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

ホスト ファイルの形式は、次のとおりです。クラスター内の各ノードに対して 1 つの行を追加します。DNS 名ではなく、前に VNet から定義したプライベート IP アドレスを指定します。

```
private ip address1:16 [e.g. 10.32.0.1:16]
private ip address2:16
...
```

## Intel MPI をインストールした後に、基本的な 2 ノード クラスターを確認します。

次の Intel MPI コマンドを実行すると、Pingpong ベンチマークを使用してクラスター構成を確認することができます。

```
/opt/intel/impi_latest/bin64/mpirun -hosts <host1>, <host2> -ppn 1 -n 2 -env I_MPI_FABRICS dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0

/opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
```

2 つのノードで動作中のクラスターに、次のような出力が表示されます。

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```

## ネットワーク トポロジに関する考慮事項

* Linux VM で、Eth1 は RDMA のネットワーク トラフィック用に予約されています。Eth1 設定や、このネットワークを参照する構成ファイル内の情報を変更しないでください。

* Azure では、IP over Infiniband (IB) はサポートされません。RDMA over IB のみがサポートされます。

* Linux VM で、Eth0 は Azure の通常のネットワーク トラフィック用に予約されています。

## 次のステップ

* Linux クラスター上で、Linux MPI アプリケーションのデプロイと実行を試します。

* Intel MPI のガイダンスについては、[Intel MPI Library のドキュメント](https://software.intel.com/ja-jp/articles/intel-mpi-library-documentation/)を参照してください。

<!---HONumber=July15_HO4-->
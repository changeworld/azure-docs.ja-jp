<properties
 pageTitle="MPI アプリケーションを実行するための Linux RDMA クラスター |Microsoft Azure"
 description="RDMA を使用して MPI アプリケーションを実行する、サイズ A8 または A9 の VM の Linux クラスターを作成します"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="08/17/2016"
 ms.author="danlep"/>

# MPI アプリケーションを実行するように Linux RDMA クラスターを設定する

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Azure でサイズ A8 および A9 の仮想マシンを使用して Linux RDMA クラスターを設定し、並列 Message Passing Interface (MPI) アプリケーションを実行する方法について説明します。Azure A8 および A9 の VM でサポートされる Linux HPC ディストリビューションとサポートされる MPI 実装を実行すると、MPI アプリケーションは、リモート ダイレクト メモリ アクセス (RDMA) テクノロジに基づく低待機時間で高スループットのネットワークを介して効率的に通信します。

>[AZURE.NOTE] 現在、Azure Linux RDMA のサポートには、Azure Marketplace の SUSE Linux Enterprise Server 12 HPC イメージまたは CentOS-based 6.5 or 7.1 HPC イメージから作成された VM 上の Intel MPI Library バージョン 5 が必要です。詳細および考慮事項については、「[A8、A9、A10、A11 インスタンスについて](virtual-machines-linux-a8-a9-a10-a11-specs.md)」を参照してください。



## クラスターのデプロイ オプション

ジョブ スケジューラを使用する場合、または使用しない場合に、Linux の RDMA クラスターの作成に使用できる方法を次に示します。

* **HPC Pack** - Azure で Microsoft HPC Pack クラスターを作成して、サポートされる Linux ディストリビューションを実行するサイズ A8 または A9 コンピューティング ノードを追加して、RDMA ネットワークにアクセスします。「[Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](virtual-machines-linux-classic-hpcpack-cluster.md)」を参照してください。

* **Azure CLI スクリプト** - 後で示すように、サイズ A8 または A9 の Linux VM のクラスターのデプロイをスクリプトにするには、[Azure コマンド ライン インターフェイス](../xplat-cli-install.md) (CLI) を使用します。クラシック デプロイメント モデルのサービス管理モードの CLI では、クラスター ノードを順番に作成します。そのため、多くのコンピューティング ノードのデプロイには数分かかる場合があります。クラシック デプロイメント モデルでは、RDMA ネットワークを通して接続するには、A8 または A9 の複数の VM を同じクラウド サービスにデプロイする必要があります。

* **Azure Resource Manager テンプレート** - RDMA ネットワークに接続して MPI ワークロードを実行する A8 および A9 VM のクラスターをデプロイするには、Resource Manager デプロイメント モデルを使用します。[独自のテンプレートを作成する](../resource-group-authoring-templates.md)か、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)」で Microsoft またはコミュニティから提供されたテンプレートを確認して、目的のソリューションをデプロイすることができます。リソース マネージャー テンプレートは、Linux クラスターをデプロイするための高速で信頼性の高い方法を提供します。Resource Manager デプロイメント モデルでは、RDMA ネットワークを通して接続するには、A8 または A9 の複数の VM を同じ可用性セットにデプロイする必要があります。

## クラシック モデルでのサンプル デプロイ

次の手順では、Azure CLI を使用して、Azure Marketplace から SUSE Linux Enterprise Server (SLES) 12 HPC VM をデプロイし、Intel MPI Library をインストールして、カスタム VM イメージを作成する方法を説明します。次に、そのイメージを使用して、A8 または A9 VM のクラスターをデプロイするスクリプトを作成します。

>[AZURE.TIP]  Azure Marketplace の CentOS ベースの 6.5 または 7.1 HPC イメージに基づいて、A8 または A9 VM のクラスターをデプロイする場合と同様の手順を実行します。違いについては、手順の中で説明します。たとえば、CentOS ベースの HPC イメージには Intel MPI に含まれているため、そのイメージから作成された VM に別に Intel MPI をインストールする必要はありません。

### 前提条件

*   **クライアント コンピューター** - Azure と通信するための Mac、Linux、または Windows ベースのクライアント コンピューターが必要です。これらの手順は、Linux クライアントを使用していることを前提とします。

*   **Azure サブスクリプション** - サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を数分で作成することができます。大規模なクラスターでは、従量課金のサブスクリプションまたはその他の購入オプションを検討してください。

*   **コア クォータ** - A8 または A9 VM のクラスターをデプロイするために、コア クォータを増やすことが必要な場合があります。たとえば、8 個の A9 VM をデプロイする場合は、少なくとも 128 コアが必要です。クォータを増やすには、[オンライン カスタマー サポートに申請](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) (無料) してください。

*   **Azure CLI** - Azure CLI を[インストール](../xplat-cli-install.md)し、クライアント コンピューターから [Azure サブスクリプションに接続します](../xplat-cli-connect.md)。

*   **Intel MPI** - クラスター用の SLES 12 HPC VM イメージをカスタマイズするには、最新の Intel MPI Library 5 ランタイムを [Intel.com のサイト](https://software.intel.com/ja-JP/intel-mpi-library/)からダウンロードし、インストールする必要があります。詳細については後で説明します。インストールを準備するには、Intel に登録した後、適切なバージョンの Intel MPI の .tgz ファイルのダウンロード リンクをコピーします。この記事は、Intel MPI バージョン 5.0.3.048 に基づきます。

    >[AZURE.NOTE] Azure Marketplace の CentOS 6.5 または CentOS 7.1 HPC イメージを使用してクラスター ノードを作成すると、Intel MPI バージョン 5.1.3.181 がプレインストールされています。

### SLES 12 VM のプロビジョニング

Azure CLI で Azure にログインした後に、`azure config list` を実行して出力にサービス管理モードが表示されることを確認します。asm モードでない場合は、次のコマンドを実行して asm モードに設定します。


    azure config mode asm


使用する権限があるすべてのサブスクリプションを一覧表示するには、次を入力します。


    azure account list

現在のアクティブなサブスクリプションは、`Current` が `true` に設定されています。クラスターの作成に使用するサブスクリプションがこれではない場合、適切なサブスクリプション ID をアクティブなサブスクリプションとして設定します。

    azure account set <subscription-Id>

Azure でパブリックに利用可能な SLES 12 HPC イメージを表示するには、次のようなコマンドを実行します (シェル環境で **grep** がサポートされていると仮定)。


    azure vm image list | grep "suse.*hpc"

次のようなコマンドを実行して、SLES 12 HPC イメージを使用し、サイズ A9 VM をプロビジョニングします。

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708

各値の説明:

* サイズには A8 または A9 (この例では A9) を指定できます。

* 外部 SSH ポート番号 (この例では、SSH の規定である 22) は任意の有効なポート番号です。内部 SSH ポート番号は 22 に設定されます。

* 新しいクラウド サービスが、場所によって指定される Azure リージョンに作成されます。A8 および A9 インスタンスを使用できる場所を指定します。

* SLES 12 イメージ名は、現在、`b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` または `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-priority-v20150708` SUSE 優先サポート (追加料金がかかります) です。

    >[AZURE.NOTE]CentOS ベースの HPC イメージを使用している場合、現在のイメージ名は `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-65-HPC-20160408` と `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-71-HPC-20160408`です。

### VM のカスタマイズ

VM のプロビジョニングの完了後、VM の外部 IP アドレス (または DNS 名) と構成済みポート番号を使用して VM に SSH 接続し、カスタマイズします。接続の詳細については、「[Linux を実行する仮想マシンにログオンする方法](virtual-machines-linux-mac-create-ssh-keys.md)」を参照してください。手順の完了にルート アクセスが必要でない限り、VM で構成したユーザーとしてコマンドを実行します。

>[AZURE.IMPORTANT]Microsoft Azure では Linux VM にルート アクセスが提供されません。ユーザーとして VM に接続されているときに管理アクセス権を取得するには、`sudo` を使用してコマンドを実行します。

* **更新プログラム** - **zypper** を使用して更新プログラムをインストールします。NFS ユーティリティをインストールすることもできます。

    >[AZURE.IMPORTANT]SLES 12 HPC VM をデプロイした場合、カーネルの更新プログラムを適用しないことをお勧めします。適用した場合、Linux RDMA ドライバーに関連する問題が発生する可能性があります。
    >
    >Marketplace から CentOS ベースの HPC イメージでは、**yum** 構成ファイルでのカーネルの更新は無効にされています。Linux RDMA ドライバーは RPM パッケージとして配布されているため、カーネルが更新された場合にドライバーの更新プログラムが機能しない可能性があります。

* **Linux RDMA ドライバーの更新プログラム** - SLES 12 HPC VM をデプロイした場合、RDMA ドライバーを更新する必要があります。詳細については、「[About the A8, A9, A10, and A11 compute-intensive instances (A8、A9、A10、A11 コンピューティング集中型インスタンスの概要)](virtual-machines-linux-a8-a9-a10-a11-specs.md#rdma-driver-updates-for-sles-12)」を参照してください。

* **Intel MPI** - SLES 12 HPC VM をデプロイした場合、次のようなコマンドを実行して、Intel MPI Library 5 ランタイムを Intel.com のサイトからダウンロードしてインストールします。CentOS ベースの 6.5 または 7.1 HPC VM をデプロイした場合、この手順は不要です。

        sudo wget <download link for your registration>

        sudo tar xvzf <tar-file>

        cd <mpi-directory>

        sudo ./install.sh

    既定の設定をそのまま使用して、Intel MPI を VM にインストールします。

* **メモリのロック** - MPI コードによって、RDMA で使用可能なメモリをロックするには、/etc/security/limits.conf ファイルで、次の設定を追加または変更する必要があります(このファイルを編集するにはルート アクセスが必要です)。 CentOS ベースの 6.5 または 7.1 HPC VM をデプロイした場合、設定はこのファイルに既に追加されています。

        <User or group name> hard    memlock <memory required for your application in KB>

        <User or group name> soft    memlock <memory required for your application in KB>

    >[AZURE.NOTE]テスト目的で、memlock を無制限に設定することもできます。たとえば、「<ユーザーまたはグループ名> hard memlock unlimited」とします。

* **SLES 12 VM 用 SSH キー** - MPI ジョブの実行時に SLES 12 HPC クラスター内のコンピューティング ノード間でユーザー アカウントの信頼を確立するために、SSH キーを生成します (CentOS ベースの HPC VM をデプロイした場合、この手順は実行しないでください。この記事で後述する、イメージをキャプチャし、クラスターをデプロイした後に、クラスター ノード内にパスワードなしの SSH 信頼関係をセットアップする手順を参照してください)。

    次のコマンドを実行して、SSH キーを作成します。入力を求められたら、パスフレーズを設定せずに、単に Enter を押して既定の場所でキーを生成します。

        ssh-keygen

    公開キーを、既知の公開キー用の authorized\_keys ファイルに追加します。

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    ~/.ssh ディレクトリにある "config" ファイルを編集 (存在しない場合は作成) します。Azure で使用するプライベート ネットワークの IP アドレスの範囲を指定します (この例では 10.32.0.0/16)。

        host 10.32.0.*
        StrictHostKeyChecking no

    または、クラスター内の各 VM のプライベート ネットワーク IP アドレスを、次のように一覧表示します。

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

    >[AZURE.NOTE]特定の IP アドレスまたは範囲を指定しない場合、`StrictHostKeyChecking no` と構成すると、潜在的なセキュリティ リスクが生じる可能性があります。

* **アプリケーション** - イメージをキャプチャする前に、必要なアプリケーションをこの VM にインストールしたり、その他のカスタマイズを実行したりします。

### イメージのキャプチャ

イメージをキャプチャするには、最初に、Linux VM で次のコマンドを実行します。このコマンドにより、VM がプロビジョニング解除されますが、設定したユーザー アカウントと SSH キーは維持されます。

```
sudo waagent -deprovision
```

次に、クライアント コンピューターで次の Azure CLI コマンドを実行し、イメージをキャプチャします。詳細については、「[従来の Linux 仮想マシンをイメージとしてキャプチャする方法](virtual-machines-linux-classic-capture-image.md)」を参照してください。

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

これらのコマンドを実行した後、使用する VM イメージがキャプチャされ、VM が削除されます。カスタム イメージをクラスターにデプロイする準備ができました。

### イメージを使用したクラスターのデプロイ

次の Bash スクリプトの該当部分を環境に合った適切な値に変更し、クライアント コンピューターで実行します。クラシック デプロイメント モデルの Azure では VM を順番にデプロイするため、このスクリプトで示されている 8 個の A9 VM をデプロイするまでに数分かかります。

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Select a region where A8 and A9 VMs are available, such as West US
# See Azure Pricing pages for prices and availability of A8 and A9 VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the A8 and A9 instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## CentOS クラスター上のパスワードなしの SSH 信頼関係

CentOS ベースの HPC イメージを使用してクラスターをデプロイした場合、コンピューティング ノード間に信頼関係を構築するには、ホストベースの認証とユーザーベースの認証という 2 つの方法があります。ホストベースの認証については、この記事で扱っていませんが、一般的に、デプロイ時の拡張スクリプトで実行されます。ユーザーベースの認証は、デプロイ後に信頼関係を構築する場合に便利です。また、SSH キーを生成し、クラスター内のコンピューティング ノード間で共有する場合に必要です。この方法は、一般的にパスワードなしの SSH ログインと呼ばれ、MPI ジョブを実行するときに必要です。

コミュニティから提供されたサンプル スクリプトについては、[GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) を参照してください。CentOS ベースの HPC クラスターで簡単なユーザー認証を有効にすることができます。次の手順で、このスクリプトをダウンロードして使用します。このスクリプトを変更するか、他の方法を使用して、クラスター コンピューティング ノード間にパスワードなしの SSH 認証を確立することもできます。

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh
    
スクリプトを実行するには、サブネットの IP アドレスのプレフィックスを知っている必要があります。クラスター ノードのいずれかで次のコマンドを実行して、プレフィックスを取得します。出力は 10.1.3.5 のようになります。プレフィックスは 10.1.3 の部分です。

    ifconfig eth0 | grep -w inet | awk '{print $2}'

コンピューティング ノードの共通ユーザー名、コンピューティング ノードのそのユーザーの共通パスワード、前のコマンドから返されたサブネットのプレフィックスという 3 つのパラメーターを使用してスクリプトを実行します。


    ./user_authentication.sh <myusername> <mypassword> 10.1.3

このスクリプトでは、次の処理が実行されます。

* .ssh というホスト ノードにディレクトリを作成します。これはパスワードなしのログインの場合に必要です。
* パスワードなしのログインで、クラスター内の任意のノードからのログインを許可するように .ssh ディレクトリに構成ファイルを作成します。
* クラスター内のすべてのノードについてノード名とノードの IP アドレスを含むファイルを作成します。これらのファイルはスクリプトの実行後も残るので、後で参照できます。
* 各クラスター ノードのプライベート キーとパブリック キーのペアを作成し、authorized\_keys ファイルにエントリを作成します。

>[AZURE.WARNING]このスクリプトを実行すると、セキュリティ上のリスクが生じる可能性があります。~/.ssh のパブリック キー情報を配布しないようにしてください。


## Intel MPI の構成と実行

Azure Linux RDMA で MPI アプリケーションを実行するには、Intel MPI に固有な特定の環境変数を構成する必要があります。次に、変数を構成してアプリケーションを実行するサンプル Bash スクリプトを示します。Intel MPI のインストールに必要な場合、mpivars.sh のパスを変更します。

```
#!/bin/bash -x

# For a SLES 12 HPC cluster

source /opt/intel/impi_latest/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh


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

ホスト ファイルの形式は、次のとおりです。クラスター内の各ノードに対して 1 つの行を追加します。DNS 名ではなく、前に VNet から定義したプライベート IP アドレスを指定します。たとえば、IP アドレス 10.32.0.1 と 10.32.0.2 を持つ 2 つのホストでは、ファイルに次の内容が含まれています。

```
10.32.0.1:16
10.32.0.2:16
```

## Intel MPI をインストールした後に、基本的な 2 ノード クラスターを確認します。

この操作をまだ行っていない場合は、まず Intel MPI の環境を設定します。

```
# For a SLES 12 HPC cluster

source /opt/intel/impi_latest/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### 単純な MPI コマンドの実行

MPI が正しくインストールされており、少なくとも 2 つのコンピューティング ノード間で通信できることを示すために、いずれかのコンピューティング ノードで単純な MPI コマンドを実行します。次の **mpirun** コマンドは、2 つのノードで **hostname** コマンドを実行します。

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
出力では、`-hosts` の入力として渡したすべてのノードの名前が一覧表示されます。たとえば、2 つのノードを含む **mpirun** コマンドは、次のような出力を返します。

```
cluster11
cluster12
```

### MPI ベンチマークの実行

次の Intel MPI コマンドでは、pingpong ベンチマークを使用して、クラスターの構成および RDMA ネットワークへの接続を確認します。

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

2 つのノードで動作中のクラスターに、次のような出力が表示されます。Azure RDMA ネットワークでは、最大 512 バイトのメッセージ サイズに対して、3 マイクロ秒以下の待機時間が必要です。

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



## 次のステップ

* Linux クラスター上で、Linux MPI アプリケーションのデプロイと実行を試します。

* Intel MPI のガイダンスについては、[Intel MPI Library のドキュメント](https://software.intel.com/ja-JP/articles/intel-mpi-library-documentation/)を参照してください。

* [クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos)を実行し、CentOS ベースの HPC イメージを使用して Intel Lustre クラスターを作成してみてください。詳細については、[このブログ](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/)をご覧ください。

<!---HONumber=AcomDC_0907_2016-->
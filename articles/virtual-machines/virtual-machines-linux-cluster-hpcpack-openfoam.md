<properties
 pageTitle="Linux VM で HPC Pack を使用して OpenFOAM を実行する | Microsoft Azure"
 description="Microsoft HPC Pack クラスターを Azure にデプロイし、RDMA ネットワークに接続された複数の Linux 計算ノードで OpenFOAM ジョブを実行します。"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="11/22/2015"
 ms.author="danlep"/>

# Azure の Linux RDMA クラスター上で Microsoft HPC Pack を使用して OpenFoam を実行する

この記事では、Microsoft HPC Pack クラスターを Azure にデプロイし、Azure リモート ダイレクト メモリ アクセス (RDMA) ネットワークに接続された複数の Linux 計算ノードで Intel MPI を使用し、[OpenFoam](http://openfoam.com/) ジョブを実行する方法について説明します。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。

OpenFOAM (Open Field Operation And Manipulation) は、無償提供されているオープン ソースの計算流体力学 (CFD) ソフトウェア パッケージです。工学分野や科学分野の企業や学術機関で幅広く利用されています。メッシュ化するためのツール (特に、複雑な CAD ジオメトリ用の並列メッシュ処理機構である snappyHexMesh) や、前処理と後処理のためのツールが備わっています。ほぼすべての処理が並列に実行され、ユーザーは、コンピューターのハードウェアを最大限に活用できます。

Microsoft HPC Pack は、Microsoft Azure 仮想マシンのクラスター上で各種の大規模な HPC および並列アプリケーション (MPI アプリケーションなど) を実行する機能を備えています。Microsoft HPC Pack 2012 R2 Update 2 以降、HPC Pack では、HPC Pack クラスターにデプロイされた Linux 計算ノード VM で Linux HPC アプリケーションを実行する機能もサポートしています。HPC Pack での Linux コンピューティング ノードの使用の概要については、「[Azure の HPC Pack クラスターで Linux コンピューティング ノードを使用開始する](virtual-machines-linux-cluster-hpcpack.md)」を参照してください。

>[AZURE.NOTE]この記事は、Linux のシステム管理について、また Linux HPC クラスターでの MPI ワークロードの実行について、ある程度の知識がある読者を対象としています。

## 前提条件

*   **HPC Pack クラスターと Linux コンピューティング ノード** - Azure Marketplace にある Azure PowerShell スクリプトと HPC Pack イメージを使用して、Azure に HPC Pack クラスターと Linux コンピューティング ノードをデプロイするための前提条件と手順については、「[Azure の HPC Pack クラスターで Linux コンピューティング ノードを使用開始する](virtual-machines-linux-cluster-hpcpack.md)」を参照してください。A8 (コンピューティング集中型インスタンス) を使って Azure RDMA ネットワークにアクセスするうえでの詳しい考慮事項については、「[A8、A9、A10、A11 コンピューティング集中型インスタンスについて](virtual-machines-a8-a9-a10-a11-specs.md)」を参照してください。

    A8 サイズの Windows Server 2012 R2 ヘッド ノード 1 つと、A8 サイズの SUSE Linux Enterprise Server 12 計算ノード 2 つとから成る Azure ベースの HPC Pack クラスターをデプロイする際に、そのスクリプトで使用する XML 構成ファイルのサンプルを次に示します。該当する値は、実際のサブスクリプションとサービス名に置き換えてください。

    >[AZURE.NOTE]現在、Azure の Linux RDMA ネットワークがサポートされるのは、Azure Marketplace の RDMA 対応 SUSE Linux Enterprise Server 12 イメージから作成された VM だけです (b4590d9e3ed742e4a1d46e5424aa335e\_\_suse-sles-12-hpc-v20150708)。

    ```
    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionName>Subscription-1</SubscriptionName>
        <StorageAccount>allvhdsje</StorageAccount>
      </Subscription>
      <Location>Japan East</Location>  
      <VNet>
        <VNetName>suse12rdmavnet</VNetName>
        <SubnetName>SUSE12RDMACluster</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpclab.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>SUSE12RDMA-HN</VMName>
        <ServiceName>suse12rdma-je</ServiceName>
        <VMSize>A8</VMSize>
        <EnableRESTAPI />
        <EnableWebPortal />
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
        <ServiceName>suse12rdma-je</ServiceName>
        <VMSize>A8</VMSize>
        <NodeCount>2</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>
```

    **その他の注意事項**

    *   すべての Linux 計算ノードを 1 つのクラウド サービスにデプロイし、そのノード間で RDMA ネットワーク接続を使用する。

    *   Linux ノードのデプロイ後、特別な管理タスクを実行する目的で、SSH 接続を使用する必要がある場合、Linux VM ごとの SSH 接続の情報を Azure ポータルから探す。
        
*   **Intel MPI** - Azure の Linux 計算ノード上で OpenFOAM を実行するには、[Intel.com サイト](https://software.intel.com/ja-JP/intel-mpi-library/)から Intel MPI Library 5 ランタイムが必要となります。その後、Linux 計算ノードに Intel MPI をインストールすることになります。この準備をするために、Intel に登録した後、確認の電子メールに含まれる関連 Web ページへのリンクをクリックし、適切なバージョンの Intel MPI (.tgz ファイル) のダウンロード リンクをコピーします。この記事は、Intel MPI バージョン 5.0.3.048 に基づきます。

*   **OpenFOAM Source Pack** - [OpenFOAM Foundation のサイト](http://www.openfoam.org/download/source.php)から Linux 用の OpenFOAM Source Pack ソフトウェアをダウンロードします。この記事は、OpenFOAM-2.3.1.tgz としてダウンロードできる Source Pack Version 2.3.1 に基づいて説明しています。Linux 計算ノードに対する OpenFOAM のアンパックとコンパイルについては、この記事で後述する手順に従ってください。

*   **EnSight** (省略可) - OpenFOAM シミュレーションの結果を確認するには、HPC Pack クラスターのヘッド ノードに、可視化分析プログラムである [EnSight](https://www.ceisoftware.com/download/) の Windows 版をダウンロードしてインストールする必要があります。ライセンスとダウンロードの詳細については、EnSight のサイトを参照してください。


## コンピューティング ノードの相互の信頼関係をセットアップする

複数の Linux ノード上でクロス ノード ジョブを実行するには、すべてのノードが互いに信頼関係を持っている必要があります (**rsh** または **ssh** によって)。Microsoft HPC Pack IaaS デプロイ スクリプトを使用して HPC Pack クラスターを作成する場合は、指定した管理者アカウントに対して永続的な相互の信頼関係がスクリプトによって自動的にセットアップされます。管理者以外のユーザーをクラスター ドメインに作成した場合は、それらのユーザーにジョブを割り当てるときに、ノード間に一時的な相互の信頼関係をセットアップする必要があります。ジョブ終了後、この関係は破棄します。これをユーザーごと行うには、HPC Pack で使用するクラスターに RSA キー ペアを指定して、信頼関係を確立します。

### RSA キー ペアの生成

公開キーと秘密キーを含む RSA キー ペアは、Linux **ssh-keygen** コマンドを実行することで簡単に作成できます。

1.	Linux コンピューターにログオンします。

2.	次のコマンドを実行します。

    ```
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE]**Enter** キーを押して、コマンドが完了するまで既定の設定を使用します。ここで、パスフレーズは入力しないでください。パスワードの入力を求めるメッセージが表示されたときには、**Enter** キーを押してください。

    ![RSA キー ペアの生成][keygen]

3.	ディレクトリを ~/.ssh ディレクトリに変更します。秘密キーは id\_rsa に格納され、公開キーは id\_rsa.pub に格納されます。

    ![公開キーと秘密キー][keys]

### HPC Pack クラスターにキー ペアを追加する
1.	HPC Pack の管理者アカウント (デプロイ スクリプトを実行したときにセットアップした管理者アカウント) を使用してヘッド ノードへのリモート デスクトップ接続を行います。

2. 標準的な Windows Server 手順を使用して、クラスターの Active Directory ドメインにドメイン ユーザー アカウントを作成します。たとえば、ヘッド ノードで Active Directory ユーザーとコンピューター ツールを使用します。この記事の例では、hpclab\\hpcuser という名前のドメイン ユーザーを作成することを前提とします。

3.	C:\\cred.xml という名前のファイルを作成し、そこに RSA キーのデータをコピーします。このファイルの例については、この記事の最後にある付録を参照してください。

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

4.	コマンド プロンプトを開き、次のコマンドを入力して、hpclab\\hpcuser アカウントの資格情報データを設定します。**extendeddata** パラメーターを使用して、キー データ用に作成した C:\\cred.xml ファイルの名前を渡します。

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    このコマンドは、出力なしで正常に終了します。ジョブを実行するために必要なユーザー アカウントの資格情報を設定したら、cred.xml ファイルを安全な場所に保存するか、または削除します。

5.	Linux ノードの 1 つに対して RSA キー ペアを生成した場合は、キーの使用が終わった後に、それらを削除することを忘れないでください。既存の id\_rsa ファイルまたは id\_rsa.pub ファイルが見つかった場合、HPC Pack は相互の信頼関係をセットアップしません。

>[AZURE.IMPORTANT]共有クラスター上でクラスター管理者として Linux ジョブを実行することは、お勧めできません。管理者によって送信されたジョブが Linux ノードのルート アカウントで実行されることが、その理由です。管理者以外のユーザーによって送信されたジョブは、ジョブ ユーザーと同じ名前を持つローカルの Linux ユーザー アカウントで実行されます。HPC Pack は、ジョブに割り当てられたすべてのノード間に、この Linux ユーザー用の相互の信頼関係をセットアップします。ジョブを実行する前に Linux ノードに対して手動で Linux ユーザーをセットアップすることも、ジョブの送信時に HPC Pack がユーザーを自動的に作成するようにすることもできます。HPC Pack でユーザーを作成した場合、ジョブの完了後にユーザーは HPC Pack によって削除されます。ノード上でジョブが完了すると、セキュリティ上の脅威を軽減するためにキーは削除されます。

## Linux ノード用にファイル共有をセットアップする

今度は、ヘッド ノード上のフォルダーに標準の SMB 共有を設定し、すべての Linux ノード上に共有フォルダーをマウントすることで、それらの Linux ノードが共通のパスを使用してアプリケーション ファイルにアクセスできるようにします。必要であれば他のファイル共有方法 (Azure Files 共有、NFS 共有など) を使用することもできます。特に Azure Files 共有は多くのシナリオに対応します。「[Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](virtual-machines-linux-cluster-hpcpack.md)」に説明されているファイル共有の説明と手順を参照してください

1.	ヘッド ノードにフォルダーを作成します。読み書き権限を設定して、フォルダーを全員で共有します。たとえばヘッド ノードで、C:\\OpenFOAM を \\\SUSE12RDMA-HN\\OpenFOAM として共有します。ここで *SUSE12RDMA-HN* は、ヘッド ノードのホスト名を表します。

2.	Windows PowerShell ウィンドウを開き、次のコマンドを実行し、共有フォルダーをマウントします。

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

    clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>’`,dir_mode=0777`,file_mode=0777
    ```

最初のコマンドで「/openfoam」という名前のフォルダーが LinuxNodes グループのすべてのノードで作成されます。2 つ目のコマンドにより、dir\_mode ビットと file\_mode ビットが「777」に設定された共有フォルダー //SUSE12RDMA-HN/OpenFOAM が Linux ノードにマウントされます。コマンドの *username* と *password* は、ヘッド ノード上のユーザーの資格情報とする必要があります。

>[AZURE.NOTE]2 番目のコマンドの "`" 記号は、PowerShell のエスケープ記号です。"`," は "," (コンマ) がコマンドの一部であることを意味します。

## MPI と OpenFOAM のインストール

RDMA ネットワークで OpenFOAM を MPI ジョブとして実行するには、Intel MPI ライブラリを使って OpenFOAM をコンパイルする必要があります。

最初にいくつかの **clusrun** コマンドを実行して、すべての Linux ノードに Intel MPI ライブラリと OpenFOAM をインストールします。先ほど構成したヘッド ノードの共有場所を使用して、Linux ノード間でインストール ファイルを共有します。

>[AZURE.IMPORTANT]ここで説明したインストールとコンパイルの手順は例です。特に、必須となるコンパイラやライブラリを正しくインストールするためには、ある程度、Linux のシステム管理に関する知識が必要となります。ご使用のバージョンの Intel MPI および OpenFOAM に必要な特定の環境変数や設定を変更しなければならない場合があります。詳細については、[Intel MPI Library for Linux のインストール ガイド](http://scc.ustc.edu.cn/zlsc/tc4600/intel/impi/INSTALL.html)と [OpenFOAM Source Pack のインストール](http://www.openfoam.org/download/source.php)に関するページを参照してください。


### Intel MPI のインストール

ダウンロードした Intel MPI インストール パッケージ (この例では l\_mpi\_p\_5.0.3.048.tgz) をヘッド ノード上の C:\\OpenFoam に保存し、Linux ノードが /openfoam からこのファイルにアクセスできるようにします。そのうえで、**clusrun** を実行してすべての Linux ノードに Intel MPI ライブラリをインストールします。

1.  次のコマンドで、各ノードの /opt/intel にインストール パッケージをコピーして抽出します。

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel

    clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/

    clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
    ```

2.  Intel MPI Library をサイレント インストールするには、silent.cfg ファイルを使用します。このファイルの例については、この記事の最後にある付録を参照してください。このファイルを共有フォルダー /openfoam に格納します。silent.cfg ファイルの詳細については、[Intel MPI Library for Linux インストール ガイドのサイレント インストール](http://scc.ustc.edu.cn/zlsc/tc4600/intel/impi/INSTALL.html#silentinstall)に関するページを参照してください。

    >[AZURE.TIP]silent.cfg ファイルは必ず、Linux の改行コード (CR LF ではなく LF のみ) でテキスト ファイルとして保存してください。これにより、スクリプトは Linux ノード上で適切に動作します。

3.  サイレント モードで Intel MPI Library をインストールします。
 
    ```
    clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
    ```
    
### MPI の構成

テストを行うため、Linux ノード上の /etc/security/limits.conf に以下の行を追加してください。

```
*               hard    memlock         unlimited
*               soft    memlock         unlimited
```

C:\\OpenFoam に limits.conf というファイルを作成 (このテキスト ファイルは Linux の改行コードで保存すること) し、次のコマンドを実行して Linux ノードにコピーします。

```
clusrun /nodegroup:LinuxNodes cp /openfoam/limits.conf /etc/security
```

limits.confile を更新した後で Linux ノードを再起動します。再起動後、共有フォルダーが /openfoam としてマウントされていることを確認してください。

### OpenFOAM のコンパイルとインストール

ダウンロードした OpenFOAM Source Pack のインストール パッケージ (この例では OpenFOAM-2.3.1.tgz) をヘッド ノード上の C:\\OpenFoam に保存し、Linux ノードが /openfoam からこのファイルにアクセスできるようにします。そのうえで **clusrun** を実行し、すべての Linux ノードで OpenFOAM をコンパイルします。


1.  すべての Linux ノードに /opt/OpenFOAM フォルダーを作成し、そのフォルダーにソース パッケージをコピーして抽出します。

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM

    clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/

    clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
    ```

2.  Intel MPI Library を使って OpenFOAM をコンパイルするにはまず、Intel MPI と OpenFOAM の両方について、いくつかの環境変数を設定します。設定には settings.sh という bash スクリプトを使用します。このファイルの例については、この記事の最後にある付録を参照してください。このファイルを共有フォルダー /openfoam に格納します (Linux の改行コードで保存すること)。このファイルには、後で OpenFOAM ジョブを実行するときに使用する MPI と OpenFOAM のランタイムの設定が格納されています。

3. OpenFOAM をコンパイルするために必要な依存パッケージをインストールします。そのために、Linux のディストリビューションによっては、最初に多数のリポジトリを追加しなけれならないことがあります。この記事の最後の付録に、一連のリポジトリとパッケージを記載してあります。個々の Linux ノードに ssh で接続し、コマンドが正しく動作することを確認するようお勧めします。

4.  以下のコマンドを実行して、OpenFOAM をコンパイルします。コンパイル処理は完了までに少し時間がかかります。また、大量のログ情報が標準出力に生成されるので、**/interleaved** オプションを使用して、少しずつ出力が表示されるようにしてください。

    ```
    clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
    ```

>[AZURE.NOTE]コマンドの “`” 記号は PowerShell のエスケープ記号です。“`&” は “&” がコマンドの一部であることを意味します。

## OpenFOAM ジョブを実行するための準備

ここでは、sloshingTank3D (OpenFoam サンプルの 1 つ) という MPI ジョブを 2 つの Linux ノード上で実行するための準備を行います。この例の /opt/openfoam231 は、Linux ノード上の OpenFOAM のインストール パスです。

### ランタイム環境のセットアップ

すべての Linux ノードに MPI と OpenFOAM のランタイム環境をセットアップするには、ヘッド ノードの Windows PowerShell ウィンドウで次のコマンドを実行します。

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### サンプル データの準備

先ほど構成した (/openfoam としたマウント) ヘッド ノードの共有場所を使用して、Linux ノード間でファイルを共有します。

1.  いずれかの Linux 計算ノードに SSH で接続します。

2.  OpenFOAM ランタイム環境のセットアップが済んでいない場合は、次のコマンドで設定します。

    ```
    $ source /openfoam/settings.sh
    ```
    
3.  sloshingTank3D サンプルを共有フォルダーにコピーし、そのフォルダーに移動します。

    ```
    $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/

    $ cd /openfoam/sloshingTank3D
    ```

4.  このサンプルのパラメーターをそのまま使用すると、数十分またはそれ以上、実行に時間がかかる場合があります。実行時間を短縮するために、必要に応じて一部のパラメーターを変更してください。簡単な方法としては、system/controlDict (時間の制御や、ソリューション データの読み取り/書き込みに関連したあらゆる入力データを格納するファイル) で、時間ステップ変数 deltaT と writeInterval に変更を加えることが考えられます。たとえば deltaT の値を 0.05 から 0.5 に、writeInterval の値を 0.05 から 0.5 に変更します。

    ![Modify step variables][step_variables]

5.  system/decomposeParDict ファイル内の変数に適切な値を指定します。この例では、それぞれ 8 つのコアを持った 2 つの Linux ノードを使用しているため、numberOfSubdomains は 16 に、hierarchicalCoeffs の n は (1 1 16) に設定することになります。つまり、16 のプロセスで OpenFOAM を並列実行するという意味です。OpenFOAM を並列実行する方法の詳細については、[OpenFOAM ユーザー ガイドの第 3.4 項でアプリケーションの並列実行](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4)に関するページを参照してください。

    ![Decompose processes][decompose]

6.  sloshingTank3D ディレクトリから次のコマンドを実行して、サンプル データを準備します。

    ```
    $ . $WM_PROJECT_DIR/bin/tools/RunFunctions

    $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict

    $ runApplication blockMesh

    $ cp 0/alpha.water.org 0/alpha.water

    $ runApplication setFields  
    ```
    
7.  ヘッド ノードの C:\\OpenFoam\\sloshingTank3D にサンプル データ ファイルがコピーされていることがわかります (C:\\OpenFoam はヘッド ノード上の共有フォルダー)。

    ![Data files on the head node][data_files]

### mpirun のホスト ファイル

この手順では、**mpirun** コマンドで使用するホスト ファイル (一連の計算ノード) を作成します。

1.	いずれかの Linux ノードの /openfoam に、hostfile という名前の新しいファイルを作成します。このファイルには、すべての Linux ノードから /openfoam/hostfile でアクセスできます。

2.	実際の Linux ノードの名前をこのファイルに入力します。このファイルの例を次に示します。
    
    ```       
    SUSE12RDMA-LN1
    SUSE12RDMA-LN2
    ```
    
    >[AZURE.TIP]ヘッド ノードの C:\\OpenFoam\\hostfile にこのファイルを作成することもできます。その場合、Linux の改行コード (CR LF ではなく LF のみ) を使ったテキスト ファイルとして保存してください。これにより、スクリプトは Linux ノード上で適切に動作します。

    **Bash スクリプト ラッパー**

    Linux ノードが多数存在し、なおかつその一部でのみジョブを実行する場合、固定ホスト ファイルの使用はお勧めできません。どのノードがジョブに割り当てられるかを把握できないためです。この場合は、**mpirun** の bash スクリプト ラッパーを作成し、ホスト ファイルを自動的に作成してください。この記事の最後にある付録で bash スクリプト ラッパーのサンプル (hpcimpirun.sh) を探し、/openfoam/hpcimpirun.sh として保存してください。そのサンプル スクリプトでは、次の処理が実行されます。

    1.	RDMA ネットワークを介して MPI ジョブを実行するために、**mpirun** の環境変数に加え、いくつかのコマンド パラメーターを設定します。このケースでは、次の設定が行われます。

        *	I\_MPI\_FABRICS=shm:dapl
        *	I\_MPI\_DAPL\_PROVIDER=ofa-v2-ib0
        *	I\_MPI\_DYNAMIC\_CONNECTION=0

    2.	環境変数 $CCP\_NODES\_CORES に従ってホスト ファイルを作成します。この変数は、ジョブを起動したときに HPC ヘッド ノードによって設定されます。

        $CCP\_NODES\_CORES の形式は、次のパターンに従います。

        ```
        <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
        ```

        各値の説明:

        * `<Number of nodes>`: このジョブに割り当てるノードの数。  
        
        * `<Name of node_n_...>`: このジョブに割り当てる各ノードの名前。
        
        * `<Cores of node_n_...>`: このジョブに割り当てるノードのコア数。

        たとえば、ジョブを実行するために 2 つのノードが必要である場合、$CCP\_NODES\_CORES は次のようになります。
        
        ```
        2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
        ```
        
    3.	**mpirun** コマンドを呼び出します。コマンド ラインに 2 つのパラメーターを追加してください。

        * `--hostfile <hostfilepath>: <hostfilepath>` - スクリプトによって作成されたホスト ファイルのパス。

        * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}` - HPC Pack のヘッド ノードによって設定される環境変数。このジョブに割り当てる合計コア数を格納します。この場合、**mpirun** のプロセスの数を指定します。


## OpenFOAM ジョブの送信

いよいよ HPC クラスター マネージャーでジョブを送信します。いくつかのジョブ タスクでは、コマンド ラインにスクリプト hpcimpirun.sh を指定する必要があります。

1. クラスター ヘッド ノードに接続し、HPC クラスター マネージャーを開始します。

2. **[リソース管理]** で、Linux 計算ノードが**オンライン**状態にあることを確認します。オンライン状態にない場合は、その計算ノードを選択し、**[オンラインにする]** をクリックします。

3.  **[ジョブ管理]** で、**[新しいジョブ]** をクリックします。

4.  ジョブの名前を入力します (たとえば、_sloshingTank3D_)。

    ![Job details][job_details]

5.	**[ジョブ リソース]** で、リソースの種類として “ノード” を選択し、[最小] を 2 に設定します。この例では、それぞれ 8 つのコアを持つ 2 つの Linux ノード上でジョブが実行されます。

    ![ジョブ リソース][job_resources]

6.	4 つのタスクをジョブに追加します。それぞれのタスクには、以下のコマンド ラインと設定を使用します。

    >[AZURE.NOTE]OpenFOAM と MPI のランタイム環境は、`source /openfoam/settings.sh` を実行することによってセットアップされます。そのため、以下に示したすべてのタスクで、OpenFOAM コマンドの前にこのコマンドが呼び出されています。

    *   **タスク 1**:**decomposePar** を実行して、**interDyMFoam** を並列実行するためのデータ ファイルを生成します。
    
        *   このタスクには 1 つのノードを割り当てます。

        *   **コマンドライン** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
    
        *   **作業ディレクトリ** - /openfoam/sloshingTank3D

        ![Task 1 details][task_details1]

    *   **タスク 2**: **interDyMFoam** を並列実行してサンプルを計算します。

        *   このタスクには 2 つのノードを割り当てます。

        *   **コマンドライン** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`

        *   **作業ディレクトリ** - /openfoam/sloshingTank3D

        ![Task 2 details][task_details2]

    *   **タスク 3**: **reconstructPar** を実行して、すべての processor\_N\_ ディレクトリにある一連の time ディレクトリを結合し、単一の time ディレクトリにまとめます。

        *   このタスクには 1 つのノードを割り当てます。

        *   **コマンドライン** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`

        *   **作業ディレクトリ** - /openfoam/sloshingTank3D

        ![Task 3 details][task_details3]

    *   **タスク 4**: **foamToEnsight** を並列実行して、OpenFOAM の結果ファイルを EnSight 形式に変換し、case ディレクトリにある Ensight という名前のディレクトリにこの EnSight ファイルを格納します。

        *   このタスクには 2 つのノードを割り当てます。

        *   **コマンドライン** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`

        *   **作業ディレクトリ** - /openfoam/sloshingTank3D

        ![Task 4 details][task_details4]

6.	これらのタスクに依存関係を追加します。タスクの昇順に追加してください。

    ![Task dependencies][task_dependencies]

7.	**[送信]** をクリックして、このジョブを実行します。

    既定では、HPC Pack は、ログオンした現在のユーザー アカウントとしてジョブを送信します。**[送信]** をクリックした後、ユーザー名とパスワードの入力を求めるダイアログ ボックスが表示される場合があります。

    ![ジョブの資格情報][creds]

    条件によっては、HPC Pack は前に入力されたユーザー情報を記憶していて、このダイアログ ボックスを表示しません。このダイアログ ボックスが HPC Pack によって再び表示されるようにするには、コマンド プロンプトに次のコマンドを入力し、ジョブを送信します。

    ```
    hpccred delcreds
    ```

8.	ジョブの実行には、サンプルに対して設定したパラメーターによって数十分から数時間かかります。2 つの Linux ノードで実行中のジョブがヒートマップに表示されます。

    ![ヒート マップ][heat_map]

    それぞれのノードで 8 つのプロセスが開始されます。

    ![Linux processes][linux_processes]

9.  ジョブが終了したら、C:\\OpenFoam\\sloshingTank3D 下のフォルダーにあるジョブの結果と、C:\\OpenFoam にあるログ ファイルを探します。


## EnSight で結果を表示する

OpenFOAM ジョブの結果は、[EnSight](https://www.ceisoftware.com/) を使用して必要に応じて可視化したり分析したりすることができます。EnSight での可視化とアニメーションの詳細については、こちらの[ビデオ ガイド](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html)を参照してください。

1.  ヘッド ノードに EnSight をインストールし、起動します。

2.  C:\\OpenFoam\\sloshingTank3D\\EnSight\\sloshingTank3D.case を開きます。

    ビューアーにタンクが表示されます。

    ![Tank in EnSight][tank]

3.	**internalMesh** から **Isosurface** を作成し、**alpha\_water** 変数を選択します。

    ![Create an isosurface][isosurface]

4.	前の手順で作成した **Isosurface\_part** の色を設定します。たとえば水色に設定します。

    ![Edit isosurface color][isosurface_color]

5.  **[Parts]** パネルの **[walls]** を選択して **[walls]** から **[Iso-volume]** を作成し、ツール バーの **[Isosurfaces]** ボタンをクリックします。

6.	ダイアログ ボックスで **[Type]** に **[Isovolume]** を選び、**[Isovolume range]** の [Min] を「0.5」に設定します。**[Create with selected parts]** をクリックして Isovolume を作成します。

7.	前の手順で作成した **Iso\_volume\_part** の色を設定します。たとえば濃い水色に設定します。

8.	**walls** の色を設定します。たとえば透明白色に設定します。

9. これで **[Play]** をクリックすると、シミュレーションの結果が表示されます。

    ![Tank result][tank_result]


## 付録


### サンプル cred.xml ファイル

```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```
### サンプル silent.cfg ファイル

```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no

```

### サンプル settings.sh スクリプト

```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```

### Linux ノードにリポジトリと依存パッケージを追加するためのサンプル コマンド

```
sudo zypper ar ftp://ftp.muug.mb.ca/mirror/opensuse/factory-snapshot/repo/oss/ update1

sudo zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ update2

sudo zypper ar ftp://ftp.pbone.net/mirror/ftp.opensuse.org/factory-snapshot/repo/oss/ update3

sudo zypper ar ftp://mirror.switch.ch/pool/4/mirror/opensuse/opensuse/distribution/13.2/repo/oss/ update4

sudo zypper ar ftp://bo.mirror.garr.it/pub/1/opensuse/distribution/13.2/repo/oss/ update6

sudo zypper ar ftp://ftp.pbone.net/mirror/ftp.opensuse.org/distribution/13.2/repo/oss/ update7

sudo zypper ar ftp://ftp.icm.edu.pl/vol/rzm5/linux-opensuse/distribution/13.2/repo/oss/ update8

sudo zypper install -t pattern devel_C_C++

sudo zypper install cmake boost-devel gnuplot mpfr-devel openmpi-devel glu-devel  
```

###サンプル hpcimpirun.sh スクリプト

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
	# CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
	${MPIRUN} $*
else
	# Create the hostfile file
	NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

	# Get every node name and write into the hostfile file
	I=1
	while [ ${I} -lt ${COUNT} ]
	do
		echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
		let "I=${I}+2"
	done

	# Run the mpirun with hostfile arg
	${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

	RTNSTS=$?
	rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/keygen.png
[keys]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/keys.png
[step_variables]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/step_variables.png
[data_files]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/data_files.png
[decompose]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/decompose.png
[job_details]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/job_details.png
[job_resources]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/job_resources.png
[task_details1]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/task_details1.png
[task_details2]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/task_details2.png
[task_details3]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/task_details3.png
[task_details4]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/task_details4.png
[task_dependencies]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/task_dependencies.png
[creds]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/creds.png
[heat_map]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/heat_map.png
[tank]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/tank.png
[tank_result]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/tank_result.png
[isosurface]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/isosurface.png
[isosurface_color]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/isosurface_color.png
[linux_processes]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/linux_processes.png

<!---HONumber=AcomDC_1125_2015-->
<properties
 pageTitle="MPI アプリケーションを実行するように Windows RDMA クラスターを設定する |Microsoft Azure"
 description="A8 または A9 サイズの VM を使用する Windows HPC Pack クラスターを作成し、Azure の RDMA ネットワークを使用して MPI アプリを実行する方法について説明します。"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# HPC Pack と A8 および A9 インスタンスを使用して Windows RDMA クラスターを設定して MPI アプリケーションを実行する

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。


この記事では、Azure で [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) と [A8 および A9 サイズのコンピューティング集中型インスタンス](virtual-machines-a8-a9-a10-a11-specs.md)を使用して Windows RDMA クラスターを設定し、並列 Message Passing Interface (MPI) アプリケーションを実行する方法について説明します。A8 および A9 サイズの Windows Server ベースのインスタンスを構成して、サポートされている MPI 実装を実行すると、MPI アプリケーションは、リモート ダイレクト メモリ アクセス (RDMA) テクノロジに基づく Azure の低待機時間で高スループットのネットワークを介して効率的に通信します。

>[AZURE.NOTE]Azure Windows RDMA は、現在、Microsoft Network Direct インターフェイスを使用して A8 および A9 インスタンスとの間で通信を行う MPI アプリケーションでサポートされています。
>
> Azure は、RDMA バックエンド ネットワークに接続せずに、A8 および A9 インスタンスと同じ処理機能を持つ、A10 および A11 のコンピューティング集中型インスタンスも提供します。Azure で MPI ワークロードを実行する場合、一般に、A8 および A9 インスタンスで最良のパフォーマンスが得られます。

Linux VM 上で Azure RDMA ネットワークにアクセスする MPI ワークロードを実行する場合は、「[MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](virtual-machines-linux-cluster-rdma.md)」を参照してください。

## Windows HPC クラスターのデプロイ オプション
Microsoft HPC Pack は、Azure で Windows Server ベースの HPC クラスターを作成する場合に推奨されるツールです。A8 および A9 インスタンスと組み合わせて使用した場合、HPC Pack は、Azure の RDMA ネットワークにアクセスする Windows ベースの MPI アプリケーションを効率的に実行する方法を提供します。HPC Pack には、マイクロソフトによる Windows 用の Message Passing Interface の実装のためのランタイム環境が含まれます。

この記事では、Microsoft HPC Pack と共にクラスター化された A8 および A9 インスタンスをデプロイする 2 つのシナリオを紹介します。

* シナリオ 1. コンピューティング集中型 worker ロール インスタンスをデプロイする (PaaS)

* シナリオ 2. 計算ノードをコンピューティング集中型 VM にデプロイする (IaaS)

## 前提条件

* コンピューティング集中型インスタンスに関する**[背景情報および考慮事項](virtual-machines-a8-a9-a10-a11-specs.md)を確認**します。


* **Azure サブスクリプション** - アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。


* **コア クォータ** - A8 または A9 VM のクラスターをデプロイするために、コア クォータを増やすことが必要な場合があります。たとえば、HPC Pack と共に 8 個の A9 インスタンスをデプロイする場合は、少なくとも 128 コアが必要です。クォータを増やすには、[オンライン カスタマー サポートに申請](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) (無料) してください。

## シナリオ 1. コンピューティング集中型 worker ロール インスタンスをデプロイする (PaaS)


既存の HPC Pack クラスターから、クラウド サービスで実行される Azure worker ロール インスタンス (Azure ノード) にコンピューティング リソースを追加します (PaaS)。HPC Pack からの "Azure へのバースト" とも呼ばれるこの機能は、worker ロール インスタンスのサイズの範囲をサポートしています。コンピューティング集中型インスタンスを使用するには、Azure ノードを追加するときに A8 または A9 のサイズを指定するだけです。

既存の (通常はオンプレミスの) クラスターから A8 または A9 Azure インスタンスへのバーストを実行するための手順を次に示します。Azure VM にデプロイされた HPC Pack ヘッド ノードに worker ロール インスタンスを追加する場合も、同様の手順を実行します。

>[AZURE.NOTE]HPC Pack を使用した Azure へのバーストのチュートリアルについては、[HPC Pack を使用したハイブリッド クラスターのセットアップ](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)に関するページを参照してください。次の手順の考慮事項は、特に A8 および A9 サイズの Azure ノードに適用されます。

![Burst to Azure][burst]



4. **HPC Pack 2012 R2 ヘッド ノードをデプロイして構成する**

    最新の HPC Pack インストール パッケージを [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=47755)からダウンロードします。Azure のバースト デプロイの要件と手順については、[HPC Pack の概要ガイド](https://technet.microsoft.com/library/jj884144.aspx)と [Microsoft HPC Pack を使用した Azure へのバースト](https://technet.microsoft.com/library/gg481749.aspx)に関するページを参照してください。

5. **Azure のサブスクリプションで管理証明書を構成する**

    ヘッド ノードと Azure の間の接続をセキュリティで保護するための証明書を構成します。オプションと手順については、[HPC Pack 用に Azure の管理証明書を構成するシナリオ](http://technet.microsoft.com/library/gg481759.aspx)に関するページを参照してください。

6. **新しいクラウド サービスとストレージ アカウントを作成する**

    Azure 管理ポータルを使用して、コンピューティング集中型インスタンスが使用可能なリージョンでのデプロイのためにクラウド サービスとストレージ アカウントを作成します(クラウド サービスとストレージ アカウントは、他のデプロイに使用されている既存のアフィニティ グループに関連付けないでください)。

7. **Azure ノード テンプレートを作成する**

    HPC クラスター マネージャーのノード テンプレートの作成ウィザードを使用します。手順については、Microsoft HPC Pack を使用して Azure ノードをデプロイする手順に関するページの [Azure ノード テンプレートの作成](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ)に関するセクションを参照してください。

    初期テスト用に、手動可用性ポリシーをテンプレートに構成することをお勧めします。

8. **クラスターにノードを追加する**

    HPC クラスター マネージャーのノードの追加ウィザードを使用します。詳細については、[Azure ノードを Windows HPC クラスターに追加する手順](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add)に関するセクションを参照してください。

    ノードのサイズを指定するときに、A8 または A9 を選択します。

9. **ノードを開始 (プロビジョニング) し、オンラインにしてジョブを実行できるようにする**

    ノードを選択し、HPC クラスター マネージャーの **[開始]** 操作を使用します。プロビジョニングが完了したら、ノードを選択し、HPC クラスター マネージャーの **[オンラインにする]** 操作を使用します。ノードでジョブを実行する準備が整います。

10. **クラスターにジョブを送信する**

    HPC Pack のジョブ送信ツールを使用して、クラスター ジョブを実行します。[Microsoft HPC Pack のジョブ管理](http://technet.microsoft.com/library/jj899585.aspx)に関するページを参照してください。

11. **ノードを停止 (プロビジョニング解除) する**

    ジョブの実行が完了したら、HPC クラスター マネージャーでノードをオフラインにし、**[停止]** 操作を使用します。


### 追加の考慮事項

* **プロキシ ノード** - HPC Pack は、コンピューティング集中型インスタンスを使用する Azure デプロイへのそれぞれのバーストで、指定された Azure worker ロール インスタンスに加えて、2 つ以上の A8 サイズの追加インスタンスをプロキシ ノードとして自動的にデプロイします。詳細については、[Azure プロキシ ノードの数の設定](https://technet.microsoft.com/library/jj899633.aspx)に関するページを参照してください。プロキシ ノードは、サブスクリプションに割り当てられたコアを使用します。これにより、Azure worker ロールのインスタンスに加えて料金が発生します。

* **仮想ネットワーク** - HPC Pack では、PaaS デプロイのポイント対サイト VPN の構成はサポートされません。


## シナリオ 2. 計算ノードをコンピューティング集中型 VM にデプロイする (IaaS)

このシナリオでは、Azure 仮想ネットワークの Active Directory ドメインに参加している VM に HPC Pack ヘッド ノードとクラスター計算ノードをデプロイします。 [HPC Pack の IaaS デプロイ スクリプト](virtual-machines-hpcpack-cluster-powershell-script.md)は、このプロセスのほとんどを自動化するもので、クラスター ノードに A8 または A9 VM のサイズを指定する機能を含む、柔軟なデプロイ オプションを提供します。この自動デプロイ方法を使用する方法を次の手順に示します。また、Azure のクイック スタート テンプレートを使用して、リソース マネージャー デプロイ モデルでクラスターをデプロイすることもできます。テスト デプロイ用に、Active Directory ドメイン、ヘッド ノード VM、計算ノード VM、および Azure の HPC Pack クラスター インフラストラクチャの他の部分を手動でデプロイすることもできます。[Azure での Microsoft HPC Pack を使用した HPC クラスター オプション](virtual-machines-hpcpack-cluster-options.md)に関するページを参照してください。

![Cluster in Azure VMs][iaas]


1. **クライアント コンピューターで HPC Pack の IaaS デプロイ スクリプトを実行して、クラスター ヘッド ノードと計算ノード VM を作成する**

 HPC Pack の IaaS デプロイ スクリプト パッケージを [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=47755)からダウンロードします。

 クライアント コンピューターを準備し、スクリプトの構成ファイルを作成して、スクリプトを実行するには、[HPC Pack の IaaS デプロイ スクリプトを使用した HPC クラスターの作成](virtual-machines-hpcpack-cluster-powershell-script.md)に関するページを参照してください。A8 および A9 サイズの計算ノードをデプロイする場合は、この記事の後半にある追加の考慮事項を参照してください。

2. **計算ノードをオンラインにしてジョブを実行できるようにする**

    ノードを選択し、HPC クラスター マネージャーの **[オンラインにする]** 操作を使用します。ノードでジョブを実行する準備が整います。

3. **クラスターにジョブを送信する**

    ヘッド ノードに接続してジョブを送信するか、またはオンプレミスのコンピューターを設定してジョブを送信します。詳細については、[Azure の HPC クラスターへのジョブの送信](virtual-machines-hpcpack-cluster-submit-jobs.md)に関するページを参照してください。

4. **ノードをオフラインにし、停止 (割り当て解除) する**

    ジョブの実行が完了したら、HPC クラスター マネージャーでノードをオフラインにします。次に、Azure の管理ツールを使用して、ノードをシャットダウンします。

### クラスターのデプロイ スクリプトを実行するための追加の考慮事項
* **仮想ネットワーク** - A8 および A9 インスタンスが使用可能なリージョンで新しい仮想ネットワークを指定します。


* **Windows Server オペレーティング システム** - RDMA 接続をサポートするには、A8 または A9 サイズの計算ノード VM の Windows Server 2012 R2 または Windows Server 2012 オペレーティング システムを指定します。


* **クラウド サービス** - ヘッド ノードと、A8 および A9 計算ノードは、それぞれ異なるクラウド サービスにデプロイすることをお勧めします。


* **ヘッド ノードのサイズ** - A8 または A9 サイズの計算ノード VM を追加する場合は、ヘッド ノード用に A4 (XL) 以上のサイズを検討してください。


* **HpcVmDrivers 拡張機能** - デプロイ スクリプトは、Windows Server オペレーティング システムを持つ A8 または A9 サイズの計算ノードをデプロイするときに、Azure VM エージェントと HpcVmDrivers 拡張機能を自動的にインストールします。HpcVmDrivers は、RDMA ネットワークに接続するためのドライバーを計算ノード VM にインストールします。[Azure VM の拡張機能とその機能](virtual-machines-extensions-features.md)に関するページを参照してください。


* **クラスター ネットワークの構成** - デプロイ スクリプトは、HPC Pack クラスターをトポロジ 5 (エンタープライズ ネットワークのすべてのノード) で自動的に構成します。このトポロジは、A8 または A9 サイズの計算ノードを持つデプロイを含め、VM でのすべての HPC Pack クラスターのデプロイに必要です。クラスター ネットワークのトポロジを後で変更しないでください。

## A8 および A9 インスタンス上で MPI アプリケーションを実行する

### 例: HPC Pack クラスターで mpipingpong を実行する

コンピューティング集中型インスタンスの HPC Pack デプロイを確認するには、クラスターで HPC Pack の **mpipingpong** コマンドを実行します。**mpipingpong** により、ペアになっているノード間でデータのパケットが繰り返し送信され、待機時間とスループットの測定値、RDMA が有効なアプリケーション ネットワークの統計情報が計算されます。この例は、クラスターの **mpiexec** コマンドを使用して MPI ジョブ (この場合は **mpipingpong**) を実行するための一般的なパターンを示しています。

この例では、"Azure へのバースト" 構成 (この記事の[シナリオ 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-(PaaS))) で Azure ノードを追加しているものと想定しています。HPC Pack を Azure VM のクラスターにデプロイする場合は、コマンド構文を修正して、別のノード グループを指定し、ネットワーク トラフィックを RDMA ネットワークに転送するように追加の環境変数を設定する必要があります。


クラスターで mpipingpong を実行するには:


1. ヘッド ノードまたは適切に構成されたクライアント コンピューターで、コマンド ウィンドウを開きます。

2. 4 ノードの Azure バースト デプロイ内のノード ペア間の待機時間を推定するために、次のコマンドを入力して、パケット サイズが小さく繰り返し数が多い設定で mpipingpong を実行するジョブを送信します。

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```

    このコマンドを実行すると、送信されたジョブの ID が返されます。

    Azure VM にデプロイされた HPC Pack クラスターをデプロイした場合は、1 つのクラウド サービスにデプロイされた計算ノード VM を含むノード グループを指定し、**mpiexec** コマンドを次のように変更します。

  ```
  job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI\_DISABLE\_SOCK 1 -env MSMPI\_PRECONNECT all -env MPICH\_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
  ```

3. ジョブが完了したら、出力 (この場合は、ジョブの 1 番目のタスクの出力) を表示するために次のように入力します。

    ```
    task view <JobID>.1
    ```

    ここで、&lt;*JobID*&gt; には、送信されたジョブの ID を指定します。

    出力には、次のように待機時間の結果が含まれます。

    ![Ping pong latency][pingpong1]

4. Azure バースト ノードのペア間のスループットを推定するために、次のコマンドを入力して、パケット サイズが大きく繰り返し数が少ない設定で **mpipingpong** を実行するジョブを送信します。

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```

    このコマンドを実行すると、送信されたジョブの ID が返されます。

    Azure VM にデプロイされた HPC Pack クラスターで、手順 2. に示したようにコマンドを変更します。

5. ジョブが完了したら、出力 (この場合は、ジョブの 1 番目のタスクの出力) を表示するために次のように入力します。task view &lt;JobID&gt;.1

  出力には、次のようにスループットの結果が含まれます。

  ![Ping pong throughput][pingpong2]


### MPI アプリケーションに関する考慮事項


Azure インスタンスで MPI アプリケーションを実行する場合の考慮事項を次に示します。いくつかの項目は、Azure ノード ("Azure へのバースト" 構成で追加された worker ロールのインスタンス) のデプロイにのみ適用されます。

* クラウド サービスの worker ロール インスタンスは、(たとえば、システムのメンテナンスやインスタンスの障害が原因で) Azure によって通知なく定期的に再プロビジョニングされます。インスタンスが MPI ジョブの実行中に再プロビジョニングされた場合、インスタンスのすべてのデータが失われ、インスタンスが最初にデプロイされた状態に戻り、MPI ジョブが失敗する原因となります。1 つの MPI ジョブに使用するノードの数が多いほど、およびジョブの実行に時間がかかるほど、ジョブの実行中にいずれかのインスタンスが再プロビジョニングされる可能性が高くなります。デプロイの 1 つのノードをファイル サーバーとして指定する場合もこの点に注意する必要があります。


* Azure で MPI ジョブを実行するために A8 および A9 インスタンスを使用する必要はありません。HPC Pack でサポートされている任意のインスタンス サイズを使用することができます。ただし、待機時間やノードを接続するネットワークの帯域幅に依存する比較的大規模な MPI ジョブの実行には A8 および A9 インスタンスをお勧めします。A8 と A9 以外のインスタンスを使用して待機時間と帯域幅に依存する MPI ジョブを実行する場合は、サイズが小さなジョブを実行して、1 つのタスクが少数のノードで実行されるようにすることをお勧めします。

* Azure インスタンスにデプロイされたアプリケーションには、アプリケーションに関連付けられているライセンス条件が適用されます。クラウドで実行するためのライセンスまたはその他の制限事項については、商用アプリケーションのベンダーに確認してください。すべてのベンダーが従量課金制ライセンスを提供しているとは限りません。


* オンプレミスのノード、共有、ライセンス サーバーにアクセスするには、Azure インスタンスでさらに設定が必要になります。たとえば、オンプレミスのライセンス サーバーにアクセスできるように Azure ノードを有効にする場合、サイト間 Azure 仮想ネットワークを構成できます。


* Azure インスタンスで MPI アプリケーションを実行するには、**hpcfwutil** コマンドを実行して、各 MPI アプリケーションをインスタンスの Windows ファイアウォールに登録します。これにより、ファイアウォールによって動的に割り当てられたポートで MPI 通信が実行されます。

    >[AZURE.NOTE]Azure デプロイへのバースト用には、ファイアウォールの例外コマンドを、クラスターに追加されたすべての新しい Azure ノードで自動的に実行されるように構成することもできます。**hpcfwutil** コマンドを実行してアプリケーションが動作することを確認した後、Azure ノードのスタートアップ スクリプトに次のコマンドを追加します。詳細については、[Azure ノードのスタートアップ スクリプトの使用](https://technet.microsoft.com/library/jj899632(v=ws.10).aspx)に関するページを参照してください。



* HPC Pack では、CCP\_MPI\_NETMASK クラスター環境変数を使用して、MPI 通信で許容されるアドレスの範囲を指定します。HPC Pack 2012 R2 以降では、CCP\_MPI\_NETMASK クラスター環境変数は、(オンプレミスまたは Azure VM の) ドメインに参加しているクラスター計算ノード間の MPI 通信にのみ影響を与えます。この変数は、Azure へのバースト構成で追加されたノードで無視されます。


* MPI ジョブは、異なるクラウド サービス (たとえば、異なるノード テンプレートを使用した Azure へのバースト デプロイ、または複数のクラウド サービスにデプロイされた Azure VM 計算ノード) にデプロイされた Azure インスタンス間で実行できません。異なるノード テンプレートを使用して開始された複数の Azure ノード デプロイの場合は、MPI ジョブを 1 つの Azure ノードのセットで実行する必要があります。


* Azure ノードをクラスターに追加してオンラインにすると、HPC ジョブ スケジューラ サービスは、すぐにノードでジョブを開始しようとします。ワークロードの一部のみを Azure で実行できる場合は、ジョブ テンプレートを更新または作成して、Azure で実行できるジョブの種類を定義してください。たとえば、ジョブ テンプレートを使用して送信されたジョブが Azure ノードでのみ実行されるようにするには、ジョブ テンプレートにノード グループ プロパティを追加し、必須の値として AzureNodes を選択します。Azure ノードに対応するカスタム グループを作成する場合、Add-HpcGroup HPC PowerShell コマンドレットを使用できます。


## 次のステップ

* Azure RDMA ネットワークにアクセスする Linux MPI ワークロードを実行する場合は、「[MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](virtual-machines-linux-cluster-rdma.md)」を参照してください。

<!--Image references-->
[burst]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/burst.png
[iaas]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/iaas.png
[pingpong1]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/pingpong1.png
[pingpong2]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/pingpong2.png

<!---HONumber=Oct15_HO3-->
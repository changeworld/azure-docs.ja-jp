<properties
 pageTitle="A8、A9、A10、A11 インスタンスについて | Microsoft Azure"
 description="仮想マシンとクラウド サービスに Azure A8、A9、A10、A11 という多くのコンピューティング処理を要するサイズを使用する場合の背景情報と考慮事項について説明します。"
 services="virtual-machines, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="09/29/2015"
 ms.author="danlep"/>

# A8、A9、A10、A11 コンピューティング集中型インスタンスについて

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]この記事は、リソース マネージャー デプロイ モデルまたはクラシック デプロイ モデルを使用してリソースを作成する場合に適用されます。

この記事では、*コンピューティング集中型*インスタンスとも呼ばれる Azure A8、A9、A10、A11 インスタンスの背景情報および使用上の注意事項について説明します。これらのインスタンスの主な機能:

* **高性能のハードウェア** – これらのインスタンスを実行する Azure データ センター ハードウェアは、ハイ パフォーマンス コンピューティング (HPC) クラスター アプリケーション、モデリング、シミュレーションなど、コンピューティング集中型およびネットワーク集中型アプリケーション用に設計および最適化されています。

* **MPI アプリケーションの RDMA ネットワーク接続** – A8 および A9 インスタンスは必要なネットワーク ドライバーを使用して構成した場合、リモート ダイレクト メモリ アクセス (RDMA) テクノロジに基づく Azure の低待機時間で高スループットのネットワークを介して他の A8 および A9 インスタンスと通信できます。この機能により、サポートされている Linux または Windows Message Passing Interface (MPI) の実装を使用するアプリケーションのパフォーマンスを向上させることができます。

* **Linux および Windows HPC クラスターのサポート** – Azure でクラスター管理およびジョブ スケジューリング ソフトウェアを A8、A9、A10、および A11 インスタンスにデプロイして、スタンドアロン HPC クラスターを作成するか、オンプレミスのクラスターに容量を追加します。他の Azure VM のサイズと同様に、A8、A9、A10、および A11 インスタンスは、Azure VM (IaaS) での標準またはカスタムの Windows Server および Linux オペレーティング システム イメージまたは Azure リソース マネージャー テンプレート、またはクラウド サービス (Windows Server 専用 PaaS) での Azure ゲスト OS リリースをサポートします。

>[AZURE.NOTE]A10 および A11 インスタンスのパフォーマンス最適化と仕様は、A8 および A9 インスタンスと同じです。ただし、Azure での RDMA ネットワークへのアクセスは含まれません。A10 と A11 のインスタンスは、ノード間で一定した待ち時間の少ない通信を必要としない HPC アプリケーション (パラメトリックまたは驚異的並列アプリケーションとも呼ばれる) 向けに設計されています。MPI アプリケーション以外のワークロードの実行の場合、A8 および A9 インスタンスは RDMA ネットワークにアクセスせず、A10 および A11 インスタンスと機能的に同等です。


## 仕様

### CPU とメモリ

次の表に示すように、Azure の A8、A9、A10、A11 コンピューティング集中型インスタンスは、高速のマルチコア CPU と大容量のメモリを備えています。

サイズ | CPU | メモリ
------------- | ----------- | ----------------
A8 および A10 | Intel Xeon E5-2670<br/>8 コア @ 2.6 GHz | DDR3-1600 MHz<br/>56 GB
A9 および A11 | Intel Xeon E5-2670<br/>16 コア @ 2.6 GHz | DDR3-1600 MHz<br/>112 GB


>[AZURE.NOTE]サポートされる命令セット拡張機能など、プロセッサに関する詳細については、Intel.com の Web サイトを参照してください。VM ストレージの容量とディスクの詳細については、「[仮想マシンのサイズ](virtual-machines-size-specs.md)」を参照してください。

### ネットワーク アダプター

A8 および A9 インスタンスは 2 個のネットワーク アダプターを備え、次の 2 つのバックエンド Azure ネットワークに接続します。


ネットワーク | 説明
-------- | -----------
10 Gbps イーサネット | Azure サービス (Azure Storage や Azure Virtual Network など) およびインターネットに接続します。
32 Gbps バックエンド、RDMA 対応 | 1 つのクラウド サービスまたは可用性セット内のインスタンス間での低待機時間、高スループットのアプリケーション通信を可能にします。MPI トラフィック専用に予約されます。


>[AZURE.IMPORTANT]Linux を実行する A8 および A9 VM では、現在、RDMA ネットワークへのアクセスは、SUSE Linux Enterprise Server 12 (SLES 12) で Azure Linux RDMA および Intel MPI Library 5 を使用するアプリケーションを通じてのみ有効です。Windows Server を実行するA8 および A9 インスタンスでは、RDMA ネットワークへのアクセスは現在、Microsoft Network Direct インターフェイスを使用するアプリケーションを通じてのみ実行できます。その他の要件については、この記事の「[RDMA ネットワークへのアクセス](#access-the-rdma-network)」を参照してください。

A10 および A11 インスタンスは、Azure サービスおよびインターネットに接続する 10 Gbps イーサネット ネットワーク アダプターを 1 個備えています。

## Azure サブスクリプションに関する考慮事項

* **Azure アカウント** – 少数のコンピューティング集中型インスタンスより多数のインスタンスをデプロイする場合は、従量課金のサブスクリプションまたは他の購入オプションを検討してください。MSDN サブスクリプションを使用することもできます。「[MSDN サブスクライバー向けの Azure の特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)」を参照してください。[Azure 無料試用版](http://azure.microsoft.com/pricing/free-trial/)を使用している場合は、使用できる Azure コンピューティング コアの数に制限があります。

* **コア クォータ** – Azure サブスクリプションのコア クォータを、既定値の 20 コアから増やすことが必要な場合があります。8 コアまたは 16 コア インスタンスの多くのシナリオでは不十分です。初期テストの場合、100 コアへのクォータ増量を要求することを検討してください。そのためには、「[Azure の制限と増設について](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)」に従って無償サポート チケットを開きます。

    >[AZURE.NOTE]Azure のクォータは容量保証ではなくクレジット制限です。使用したコアに対してのみ課金されます。

* **アフィニティ グループ** – 現在、アフィニティ グループは、ほとんどの新しいデプロイに推奨されていません。ただし、A8 ～ A11 以外のサイズのインスタンスを含むアフィニティ グループを使用している場合、A8 ～ A11 インスタンスに対しては使用できなくなります (その逆も同様)。

* **仮想ネットワーク** – コンピューティング集中型インスタンスを使用するために、Azure Virtual Network は必要ありません。ただし、多くの IaaS シナリオの場合、またはアプリケーション ライセンス サーバーなどのオンプレミス リソースへのアクセスが必要なサイト間接続の場合は、少なくともクラウド ベースの Azure Virtual Network が必要になる可能性があります。インスタンスをデプロイする前に、新しい (リージョン) 仮想ネットワークを作成する必要があります。アフィニティ グループ内の仮想ネットワークに A8、A9、A10、または A11 VM を追加することはできません。詳細については、「[仮想ネットワーク (VNet) の作成方法](../virtual-network/virtual-networks-create-vnet.md)」および「[サイト間 VPN 接続を使用したVirtual Network の構成](../vpn-gateway/vpn-gateway-site-to-site-create.md)」を参照してください。

* **クラウド サービスまたは可用性セット** – RDMA ネットワーク経由で接続するには、A8 および A9 インスタンスを同じクラウド サービス (Azure サービス管理で Linux ベースの VM または Windows ベースの VM を使用する IaaS シナリオ、または Windows Server を使用する PaaS シナリオ用) または同じ可用性セット (Azure リソース マネージャーでの Linux ベースの VM または Windows ベースの VM 用).にデプロイする必要があります。

## HPC Pack を使用する場合の考慮事項

### HPC Pack および Linux を使用する場合の考慮事項

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) は、Windows 向け Microsoft の無料の HPC クラスターおよびジョブ管理ソリューションです。HPC Pack 2012 R2 Update 2 以降、HPC Pack ではいくつかの Linux ディストリビューションを Azure VM にデプロイされ、Windows Server ヘッド ノードで管理されるコンピューティング ノードで実行できるようになりました。HPC Pack の最新版では、Azure で RDMA ネットワークにアクセスする MPI アプリケーションを実行できる Linux ベースのクラスターをデプロイできます。詳細については、「[チュートリアル: Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](virtual-machines-linux-cluster-hpcpack.md)」を参照してください。

### HPC Pack および Windows を使用する場合の考慮事項

HPC Pack は、Windows Server で A8、A9、A10、および A11 インスタンスを使用するためには必要ありませんが、Azure で Windows HPC Server ベースのクラスターを作成する場合は推奨されるツールです。A8 および A9 インスタンスの場合、HPC Pack は、Azure の RDMA ネットワークにアクセスする Windows ベースの MPI アプリケーションを実行する最も効率的な方法です。HPC Pack には、マイクロソフトによる Windows 用の Message Passing Interface の実装のためのランタイム環境が含まれます。

Windows Server で HPC Pack を使って、多くのコンピューティング処理を要するインスタンスを使用する方法およびチェックリストについては、「[Set up a Windows RDMA cluster with HPC Pack to run MPI applications (HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する)](virtual-machines-windows-hpcpack-cluster-rdma.md)」を参照してください。

## RDMA ネットワークへのアクセス

### Linux A8 および A9 VM からのアクセス

単一のクラウド サービスまたは可用性セット内では、Linux RDMA ドライバーを使用してインスタンス間の通信を行う MPI アプリケーションが実行されていれば、A8 および A9 インスタンスは Azure の RDMA ネットワークにアクセスできます。現時点では、[Intel MPI Library 5](https://software.intel.com/ja-JP/intel-mpi-library/) では Azure Linux RDMA のみがサポートされています。

>[AZURE.NOTE]現在、Azure Linux RDMA ドライバーは、ドライバー拡張機能を介したインストールでは使用できません。これを使用する唯一の方法は、Azure Marketplace の RDMA 対応 SLES 12 イメージを使用することです。

以下の表に、Linux MPI アプリケーションがコンピューティング ノード (IaaS) のクラスターの RDMA ネットワークにアクセスするための前提条件を記載します。デプロイメントのオプションと構成手順については、「[MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](virtual-machines-linux-cluster-rdma.md)」を参照してください。

前提条件 | 仮想マシン (IaaS)
------------ | -------------
オペレーティング システム | Azure Marketplace から入手した SLES 12 HPC イメージ
MPI | Intel MPI Library 5

### Windows A8 および A9 インスタンスからのアクセス

単一のクラウド サービスまたは可用性セット内では、Microsoft Network Direct インターフェイスを使用してインスタンス間の通信を行う MPI アプリケーションが実行されていれば、A8 および A9 インスタンスは Azure の RDMA ネットワークにアクセスできます。A10 および A11 インスタンスには、RDMA ネットワークへのアクセスは含まれません。

A8 または A9 インスタンスの仮想マシン (IaaS) およびクラウド サービス (PaaS) デプロイメント内の RDMA ネットワークにアクセスするための MPI アプリケーションの要件を、次の表に示します。標準的なデプロイのシナリオについては、「[Set up a Windows RDMA cluster with HPC Pack to run MPI applications (HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する)](virtual-machines-windows-hpcpack-cluster-rdma.md)」を参照してください。


前提条件 | 仮想マシン (IaaS) | Cloud Services (PaaS)
---------- | ------------ | -------------
オペレーティング システム | Windows Server 2012 R2 または Windows Server 2012 | Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 ゲスト OS ファミリ
MPI | MS-MPI 2012 R2 以降 (スタンドアロンまたは HPC Pack 2012 R2 以降によってインストール済み)<br/><br/>Intel MPI Library 5 | MS-MPI 2012 R2 以降 (HPC Pack 2012 R2 以降によってインストール済み)<br/><br/>Intel MPI Library 5


>[AZURE.NOTE]IaaS シナリオの場合、HpcVmDrivers 拡張機能を VM に追加して RDMA 接続に必要な Windows ネットワーク デバイス ドライバーをインストールする必要があります。デプロイメントの方法によって、サイズ A8 または A9 の VM への HpcVmDrivers 拡張機能の追加は、自動的に行われることも、手動で行う必要があることもあります。拡張機能の追加については、「[仮想マシン拡張機能の管理](virtual-machines-extensions-install.md)」を参照してください。


## その他の注意事項

* TA8 ～ A11 の VM サイズは、標準の価格レベルでのみ提供されます。

* 既存の Azure VM を A8、A9、A10、または A11 のサイズに変更することはできません。

* 現在、A8、A9、A10、および A11 インスタンスを、既存のアフィニティ グループの一部であるクラウド サービスを使用してデプロイすることはできません。同様に、A8、A9、A10、および A11 インスタンスを含むクラウド サービスのアフィニティ グループを、他のインスタンス サイズのデプロイに使用することはできません。これらのデプロイを試みると、「`Azure deployment failure (Compute.OverconstrainedAllocationRequest): The VM size (or combination of VM sizes) required by this deployment cannot be provisioned due to deployment request constraints.`」のようなエラー メッセージが表示されます。

* Azure の RDMA ネットワークでは、アドレス空間 172.16.0.0/12 は予約済みです。Azure Virtual Network にデプロイ済みの A8 および A9 インスタンスで MPI アプリケーションを実行する場合、仮想ネットワークのアドレス空間が RDMA ネットワークと重複しないようにしてください。

## 次のステップ

* A8、A9、A10、および A11 インスタンスの可用性と料金の詳細については、「[Virtual Machines の料金](http://azure.microsoft.com/pricing/details/virtual-machines/)」および「[Cloud Services の料金](http://azure.microsoft.com/pricing/details/cloud-services/)」を参照してください。
* A8 および A9 インスタンスを使用して Linux ベースのクラスターをデプロイし、Azure RDMA ネットワークにアクセスするように構成する方法については、「[MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](virtual-machines-linux-cluster-rdma.md)」を参照してください。
* Windows 上の HPC Pack により A8 および A9 インスタンスのデプロイと使用を開始する方法については、「[Set up a Windows RDMA cluster with HPC Pack to run MPI applications (HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する)](virtual-machines-windows-hpcpack-cluster-rdma.md)」を参照してください。

<!---HONumber=Oct15_HO1-->
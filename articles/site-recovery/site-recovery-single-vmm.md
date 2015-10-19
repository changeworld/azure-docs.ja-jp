
<properties
	pageTitle="単一の VMM サーバーを使用した保護の設定"
	description="Azure Site Recovery は、オンプレミスの VMM クラウドに配置された仮想マシンのセカンダリ VMM クラウドへのレプリケーション、フェールオーバー、および復旧を調整します。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="10/07/2015"
	ms.author="raynew"/>

#  単一の VMM サーバーを使用した保護の設定

## 概要

Azure Site Recovery は、さまざまなデプロイ シナリオでの仮想マシンのレプリケーション、フェールオーバー、復旧を調整してビジネス継続性と障害復旧 (BCDR) 戦略に貢献します。デプロイ シナリオのすべての一覧については、「[Azure Site Recovery の概要](site-recovery-overview.md)」を参照してください。

インフラストラクチャに単一の VMM サーバーしかない場合は、Site Recovery をデプロイして Azure に VMM クラウドの仮想マシンをレプリケートするか、単一の VMM サーバー上のクラウド間でレプリケートすることができます。このデプロイではフェールオーバーと復旧はシームレスでないため、2 つの VMM サーバー (各サイトに 1 つずつ) をデプロイできない場合にのみ、この操作を行うことをお勧めします。復旧には、(Hyper-V Manager コンソールで Hyper-V レプリカを使用して) Azure Site Recovery コンソール以外から VMM サーバーを手動でフェールオーバーする必要があります。

いくつかの方法で、単一の VMM サーバーを使用してレプリケーションを設定できます。

### スタンドアロン デプロイ

プライマリ サイトに仮想マシンとしてスタンドアロンの VMM サーバーをデプロイし、Site Recovery と Hyper-V レプリカを使用して、セカンダリ サイトにこの仮想マシンをレプリケートします。ダウンタイムを削減するために、VMM 仮想マシンに SQL Server をインストールできます。VMM でリモート SQL Server が使用されている場合は、まずリモート SQL Server を復旧してから VMM サーバーを復旧する必要があります。

![スタンドアロンの仮想 VMM サーバー](./media/site-recovery-single-vmm/SingleVMMStandalone.png)

### クラスター デプロイ

VMM を高可用性にするために、Windows フェールオーバー クラスター内の仮想マシンとしてデプロイすることができます。このことは、クリティカルなワークロードが VMM によって管理されている場合に役立ちます。VMM が、ワークロードの可用性を確保し、VMM を実行しているホストのハードウェア フェールオーバーから保護するためです。Site Recovery と共に単一の VMM サーバーをデプロイするには、VMM 仮想マシンを地理的に別のサイトをまたいだストレッチ クラスター上にデプロイする必要があります。VMM によって使用される SQL Server データベースは、セカンダリ サイト上にレプリカを配置して、SQL Server AlwaysOn 可用性グループで保護する必要があります。障害が発生すると、VMM サーバーと対応する SQL Server データベースがフェールオーバーされ、セカンダリ サイトからアクセスすることができます。

![クラスター化された仮想 VMM サーバー](./media/site-recovery-single-vmm/SingleVMMCluster.png)


## 開始する前に

- この手順では、1 つのスタンドアロン VMM サーバーと共に Site Recovery をデプロイする方法について説明します。
- デプロイを開始する前に、[前提条件](site-recovery-vmm-to-vmm.md/#before-you-start)を満たしていることを確認します。
- 単一の VMM サーバーには少なくとも 2 つのクラウドを構成する必要があります。1 つのクラウドは保護されたクラウドとして機能し、もう一方は保護を実行します。
- 保護するクラウドには、以下が含まれている必要があります。
	- 1 つ以上の VMM ホスト グループ
	- 各ホスト グループに 1 つ以上の Hyper-V ホスト サーバー
	- 各ホスト サーバー上に配置された 1 つ以上の Hyper-V 仮想マシン

このシナリオの設定時に問題が発生した場合は、[Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)に質問を投稿してください。



## 単一のサーバー デプロイの構成

1. VMM がデプロイされていない場合は、SQL Server データベースがインストールされている仮想マシンで VMM を設定します。「[system requirements (システム要件)](https://technet.microsoft.com/library/dn771747.aspx)」を参照してください。 
2. VMM サーバー上で少なくとも 2 つのクラウドを設定します。以下の詳細情報を参照してください。

	- [What’s New in Private Cloud with System Center 2012 R2 VMM (System Center 2012 R2 VMM のプライベート クラウドの新機能)](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=) および [VMM 2012 and the clouds (VMM 2012 とクラウド)](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html) 
	- [VMM クラウド ファブリックの構成](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
	- [VMM でのプライベート クラウドの作成](https://technet.microsoft.com/library/jj860425.aspx)および[Walkthrough: Creating private clouds with System Center 2012 SP1 VMM (チュートリアル: System Center 2012 SP1 VMM を使用したプライベート クラウドの作成)](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)
3. 保護する仮想マシンが配置されているソース Hyper-V ホスト サーバーを、保護するクラウド (ソース クラウド) に追加します。保護を提供する VMM サーバー上のクラウドに、ターゲットの Hyper-V ホスト サーバーを追加します。
4. Azure Site Recovery コンテナーを[作成](site-recovery-vmm-to-vmm.md/#step-1-create-a-site-recovery-vault)し、コンテナー登録キーを生成します。
4. Azure Site Recovery Provider を VMM サーバーに[インストール](site-recovery-vmm-to-vmm.md/#step-3-install-the-azure-site-recovery-provider)し、サーバーをコンテナーに登録します。 
5. Site Recovery ポータルにクラウドが表示されることを確認し、[クラウドの保護設定を構成す](site-recovery-vmm-to-vmm.md/#step-4-configure-cloud-protection-settings)します。
	- **[ソースの場所]** と **[ターゲットの場所]** で、単一の VMM サーバーの名前を指定します。
	- **[レプリケーション方法]** で、初期レプリケーションに対して **[ネットワーク経由]** を選択します。これは、クラウドが同じサーバー上にあるためです。

6. 必要に応じて、[ネットワーク マッピングを構成](site-recovery-vmm-to-vmm.md/#step-5-configure-network-mapping)します。

	- **[ソース]** と **[ターゲット]** で、単一の VMM サーバーの名前を指定します。
	- **[ソース上のネットワーク]**で、保護対象のクラウドに対して構成されている VM ネットワークを選択します。
	- **[ターゲット上のネットワーク]**で、保護に使用するクラウドに対して構成されている VM ネットワークを選択します。
	- 同じ VMM サーバー上の 2 つの仮想マシン (VM) ネットワーク間に、ネットワーク マッピングを構成することができます。2 つの異なるサイトに同じ VMM ネットワークが存在する場合は、同じネットワーク間でマップできます。
7. 保護する VMM クラウドの仮想マシンに対して[保護を有効](site-recovery-vmm-to-vmm.md/#step-7-enable-virtual-machine-protection)にします。 
7. Hyper-V Manager コンソールで、Hyper-V レプリカを使用した VMM 仮想マシンのレプリケーションを設定します。VMM 仮想マシンは、どの VMM クラウドにも追加しないでください。


## フェールオーバーと復旧

### 復旧計画の作成

復旧計画は、一緒にフェールオーバーおよび復旧する必要がある仮想マシンをグループ化します。

1. 復旧計画を作成する場合、**[ソース]** と **[ターゲット]** で単一の VMM サーバーの名前を指定します。**[仮想マシンの選択]** に、プライマリ クラウドに関連付けられている仮想マシンが表示されます。
2. 次に、[復旧計画を作成し、カスタマイズ](https://msdn.microsoft.com/library/azure/dn337331.aspx)します。


### 復旧

障害が発生した場合は、次の手順を使用してワークロードを復旧できます。

1. レプリカの VMM 仮想マシンを Hyper-V Manager コンソールから復旧サイトへ手動でフェールオーバーします。
2. VMM 仮想マシンを復旧した後、ポータルから Hyper-V Recovery Manager コンソールにログインし、プライマリ サイトから復旧サイトへの仮想マシンの計画外のフェールオーバーを実行できます。
3.  計画外のフェールオーバーが完了したら、ユーザーは、プライマリ サイトのすべてのリソースにアクセスできます。


 

<!---HONumber=Oct15_HO2-->
<properties
	pageTitle="Site Recovery のネットワーク マッピング"
	description="Azure Site Recovery は、オンプレミスに配置されている仮想マシンと物理サーバーの Azure またはセカンダリ オンプレミス サイトへのレプリケーション、フェールオーバー、および復旧を調整します。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="05/08/2015"
	ms.author="raynew"/>


# Site Recovery のネットワーク マッピング


Azure Site Recovery は、仮想マシンと物理サーバーのレプリケーション、フェールオーバー、復旧を調整してビジネス継続性と障害復旧 (BCDR) 戦略に貢献します。可能なデプロイ シナリオについては、「[Site Recovery Overview (Site Recovery の概要)](site-recovery-overview.md)」 を参照してください。


## この記事の内容

ネットワーク マッピングは VMM および Site Recovery をデプロイする場合の重要な要素です。ネットワーク マッピングにより、ターゲット Hyper-V ホスト サーバー上で、レプリケートされた仮想マシンの配置は最適化され、レプリケートされた仮想マシンは、フェールオーバー後に適切なネットワークに確実に接続されます。この記事では、ネットワーク マッピングについて説明し、ネットワーク マッピングのしくみの理解に役立つ例をいくつかを示します。


質問がある場合は、[Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

## 概要

ネットワーク マッピングを設定する方法は、Site Recovery デプロイ シナリオに依存します。



- **オンプレミス間の VMM サーバー**—ネットワーク マッピングにより、ソース VMM サーバー上の VM ネットワークとターゲット VMM サーバー上の VM ネットワーク間をマップして、次のことを行います。

	- **フェールオーバー後に仮想マシンを接続**—フェールオーバー後に仮想マシンを適切なネットワークに接続します。レプリカ仮想マシンは、ソース ネットワークにマップされているターゲット ネットワークに接続されます。
	- **ホスト サーバー上にレプリカ仮想マシンを配置**—Hyper-V ホスト サーバーにレプリカ仮想マシンを最適な方法で配置します。レプリカ仮想マシンは、マップされた VM ネットワークにアクセスできるホストに配置されます。
	- **ネットワーク マッピングを構成しない**—ネットワーク マッピングを構成しない場合は、フェールオーバー後に、レプリケートされた仮想マシンは VM ネットワークに接続されません。

- **オンプレミスの VMM サーバーから Azure**—ネットワーク マッピングにより、ソース VMM サーバー上の VM ネットワークとターゲット Azure ネットワーク間をマップして、次のことを行います。
	- **フェールオーバー後に仮想マシンを接続**—同じネットワーク上でフェールオーバーするすべてのマシンは、どの復旧計画に含まれていても、相互に接続できます。
	- **ネットワーク ゲートウェイ**—ターゲットの Azure ネットワークでネットワーク ゲートウェイをセットアップすると、仮想マシンは、他のオンプレミスの仮想マシンに接続できます。
	- **ネットワーク マッピングを構成しない**—ネットワーク マッピングを構成しない場合は、Azure へのフェールオーバー後、同じ復旧計画でフェールオーバーする仮想マシンのみが相互に接続できます。

## VM ネットワーク

VMM 論理ネットワークは、物理ネットワーク インフラストラクチャの抽象的なビューを提供します。VM ネットワークは、仮想マシンが論理ネットワークに接続できるように、ネットワーク インターフェイスを提供します。論理ネットワークには、少なくとも 1 つの VM ネットワークが必要です。保護するためにクラウドに仮想マシンを配置する場合、その仮想マシンは、クラウドに関連付けられている論理ネットワークにリンクする VM ネットワークに接続されている必要があります。以下の詳細情報を参照してください。

- [Logical Networks (Part 1) (論理ネットワーク (第 1 部))](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx)
- [Virtual Networking in VMM 2012 SP1 (VMM 2012 SP1 の仮想ネットワーク)](http://blogs.technet.com/b/scvmm/archive/2013/01/08/virtual-networking-in-vmm-2012-sp1.aspx)

## 例

ネットワーク マッピングは、2 つの VMM サーバー上の VM ネットワーク間、または 2 つのサイトが同じサーバーによって管理されている場合は、単一の VMM サーバー上の VM ネットワーク間に構成できます。マッピングを正しく構成し、レプリケーションを有効にすると、1 次拠点にある仮想マシンはネットワークに接続され、ターゲットの拠点にあるそのレプリカは、マップされたネットワークに接続されます。

VMM でネットワークが正しくセットアップされている場合、ネットワーク マッピング中に、ターゲット VM ネットワークを選択すると、ソース VM ネットワークを使用する VMM ソース クラウドが表示されます。また、保護に使用されるターゲット クラウド上の使用可能なターゲット VM ネットワークも表示されます。

このメカニズムを説明するために、次に例を示します。ニューヨークとシカゴという 2 つの拠点がある組織を例にします。

**場所** | **VMM サーバー** | **VM ネットワーク** | **マップ先**
---|---|---|---
ニューヨーク | VMM-NewYork| VMNetwork1-NewYork | VMNetwork1-Chicago にマップされています
 | | VMNetwork2-NewYork | マッピングなし
シカゴ | VMM-Chicago| VMNetwork1-Chicago | VMNetwork1-NewYork にマップされています
 | | VMNetwork1-Chicago | マッピングなし

この例では、次の点に注意してください。

- VMNetwork1-NewYork に接続されている仮想マシンに対してレプリカ仮想マシンが作成されると、そのレプリカ仮想マシンは VMNetwork1-Chicago に接続されます。
- VMNetwork2-NewYork または VMNetwork2-Chicago 対してレプリカ仮想マシンが作成されると、そのレプリカ仮想マシンはどのネットワークにも接続されません。

ここでは、サンプルの組織での VMM クラウドのセットアップ方法、およびクラウドに関連付けられた論理ネットワークを示します。

### クラウドの保護設定

**保護されたクラウド** | **保護するクラウド** | **論理ネットワーク (ニューヨーク)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>該当なし</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>該当なし</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### 論理設定と VM ネットワークの設定

**場所** | **論理ネットワーク** | **関連付けられた VM ネットワーク**
---|---|---
ニューヨーク | LogicalNetwork1-NewYork | VMNetwork1-NewYork
シカゴ | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2-Chicago | VMNetwork2-Chicago

### ターゲット ネットワーク

これらの設定に基づき、次の表は、ターゲット VM ネットワークを選択する場合に使用可能な選択肢を示します。

**選択肢** | **保護されたクラウド** | **保護するクラウド** | **使用可能なターゲット ネットワーク**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | 使用可能
 | GoldCloud1 | GoldCloud2 | 使用可能
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | 使用できません。
 | GoldCloud1 | GoldCloud2 | 使用可能



## 複数のサブネット

ターゲット ネットワークに複数のサブネットがあり、そのサブネットのいずれかが、ソースの仮想マシンが配置されているサブネットと同じ名前である場合、フェールオーバー後、レプリカの仮想マシンはそのターゲット サブネットに接続されます。ターゲットのサブネットで名前が一致するものがなければ、仮想マシンはネットワークの最初のサブネットに接続されます。


### フェールバック

フェールバック (レプリケーションの反転) の場合の動作を確認するために、次の設定で VMNetwork1-NewYork が VMNetwork1-Chicago にマップされていると仮定します。


**仮想マシン** | **接続先の VM ネットワーク**
---|---
VM1 | VMNetwork1-NewYork
VM2 (VM1 のレプリカ) | VMNetwork1-Chicago

これらの設定で、いくつかの可能なシナリオでの動作を確認します。

**シナリオ** | **結果**
---|---
フェールオーバー後に VM-2 のネットワークのプロパティは変更なし | VM-1 は、ソース ネットワークに接続されたままです。
VM-2 のネットワークのプロパティがフェールオーバー後に変更され、VM-2 が切断される | VM-1 が切断されます。
VM-2 のネットワークのプロパティがフェールオーバー後に変更され、VM-2 が VMNetwork2-Chicago に接続される | VMNetwork2-Chicago がマップされていない場合、VM-1 は切断されます。
VMNetwork1-Chicago のネットワークのマッピングが変更される | VM-1 は VMNetwork1-Chicago にマップされたネットワークに接続されます。


## 次のステップ

ネットワーク マッピングに関する理解を深めたので、[ベスト プラクティス](site-recovery-best-practices.md)を読み、デプロイの準備をします。
 

<!---HONumber=July15_HO2-->
<properties
	pageTitle="オンプレミス データセンター間で Hyper-V 仮想マシンをレプリケーションするために、Azure Site Recovery の記憶域をマップする | Microsoft Azure"
	description="Azure Site Recovery を使用して 2 つのオンプレミス データセンター間で Hyper-V 仮想マシンをレプリケーションするために、記憶域マッピングを準備します。"
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
	ms.workload="storage-backup-recovery"
	ms.date="07/06/2016"
	ms.author="raynew"/>


# Azure Site Recovery を使用して 2 つのオンプレミス データセンター間で Hyper-V 仮想マシンをレプリケーションするために、記憶域マッピングを準備する


Azure Site Recovery は、仮想マシンと物理サーバーのレプリケーション、フェールオーバー、復旧を調整してビジネス継続性と障害復旧 (BCDR) 戦略に貢献します。この記事では記憶域マッピングについて説明します。これを利用すれば、Site Recovery を使って 2 つのオンプレミスの VMM データセンター間で Hyper-V 仮想マシンをレプリケートするときに、記憶域を最適に使用できます。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。

## 概要

記憶域マッピングが有効なのは、次のように、Hyper-V レプリカまたは SAN レプリケーションを使用して、VMM クラウドに配置されている Hyper-V 仮想マシンをプライマリ データセンターからセカンダリ データセンターにレプリケートする場合のみです。


- **Hyper-V レプリカを使用したオンプレミス間のレプリケーション** - 次の目的で、ソースとターゲットの VMM サーバーで記憶域分類をマップすることで、記憶域マッピングを設定します。

	- **レプリカ仮想マシンのターゲット記憶域の特定** - 仮想マシンは、選択した記憶域ターゲット (SMA 共有またはクラスター共有ボリューム (CSV)) にレプリケートされます。
	- **レプリカ仮想マシンの配置** - 記憶域マッピングを使用して、Hyper-V ホスト サーバーにレプリカ仮想マシンを最適に配置します。レプリカ仮想マシンは、マップされた記憶域分類にアクセスできるホストに配置されます。
	- **記憶域マッピングなし** - 記憶域マッピングを構成しない場合、仮想マシンは、レプリカ仮想マシンに関連付けられている Hyper-V ホスト サーバーで指定された既定の記憶域の場所にレプリケートされます。

- **SAN を使用したオンプレミス間のレプリケーション** - ソースとターゲットの VMM サーバーで記憶域配列プールをマッピングすることで、記憶域マッピングを設定します。
	- **プールの指定** - プライマリ プールからレプリケーション データを受け取るセカンダリ記憶域プールを指定します。
	- **ターゲット記憶域プールの特定** - ソース レプリケーション グループ内の LUN が、マップされたターゲット記憶域プールにレプリケートされるようにします。

## Hyper-V レプリケーション用に記憶域分類を設定する

Site Recovery で Hyper-V レプリカを使ってレプリケートする場合は、ソースとターゲットの VMM サーバーか、1 つの VMM サーバーで (2 つのサイトが同じ VMM サーバーによって管理されている場合)、記憶域分類どうしをマップします。以下の点に注意してください。

- マッピングが適切に構成され、レプリケーションが有効になっていると、プライマリの場所の仮想マシンの仮想ハード ディスクが、マップされたターゲットの場所の記憶域にレプリケートされます。
- 記憶域分類は、ソースとターゲットのクラウドに配置されたホスト グループで使用できる必要があります。
- 分類のストレージの種類は同じでなくてもかまいません。たとえば、SMB 共有が含まれているソース分類を、CSV が含まれているターゲット分類にマップすることができます。
- 詳細については、「[How to create storage classifications in VMM (VMM で記憶域分類を作成する方法)](https://technet.microsoft.com/library/gg610685.aspx)」をご覧ください。

## 例

VMM で分類が適切に構成されている場合、記憶域マッピング中にソースとターゲットの VMM サーバーを選択すると、ソース分類とターゲット分類が表示されます。ニューヨークとシカゴの 2 つの拠点がある組織での記憶域のファイル共有と分類の例を以下に示します。

**場所** | **VMM サーバー** | **ファイル共有 (ソース)** | **分類 (ソース)** | **マップ先** | **ファイル共有 (ターゲット)**
---|---|--- |---|---|---
ニューヨーク | VMM\_Source| SourceShare1 | GOLD | GOLD\_TARGET | TargetShare1
 | | SourceShare2 | SILVER | SILVER\_TARGET | TargetShare2
 | | SourceShare3 | BRONZE | BRONZE\_TARGET | TargetShare3
シカゴ | VMM\_Target | | GOLD\_TARGET | マッピングなし |
| | | SILVER\_TARGET | マッピングなし |
 | | | BRONZE\_TARGET | マッピングなし

Site Recovery ポータルの **[リソース]** ページの **[サーバー記憶域]** タブでこれらを構成しました。

![ストレージ マッピングの構成](./media/site-recovery-storage-mapping/storage-mapping1.png)

この例では、次の点に注意してください。
- GOLD ストレージ (SourceShare1) 上のいずれかの仮想マシンのレプリカ仮想マシンが作成されると、それが GOLD\_TARGET ストレージ (TargetShare1) にレプリケートされます。
- 同様に、SILVER ストレージ (SourceShare2) 上のいずれかの仮想マシンのレプリカ仮想マシンが作成されると、それが SILVER\_TARGET (TargetShare2) ストレージにレプリケートされます。

実際のファイル共有と、VMM でそれらに割り当てられた分類は次のようになります。

![VMM での記憶域分類](./media/site-recovery-storage-mapping/storage-mapping2.png)

## 複数の記憶域の場所

ターゲット分類が複数の SMB 共有または CSV に割り当てられている場合、仮想マシンを保護するときに、最適な記憶域の場所が自動的に選択されます。指定された分類で適切なターゲット記憶域が使用できない場合、Hyper-V ホストで指定された既定の記憶域の場所を使用して、レプリカ仮想ハード ディスクが配置されます。

この例の記憶域分類とクラスター共有ボリュームの設定を次の表に示します。

**場所** | **分類** | **関連付けられた記憶域**
---|---|---
ニューヨーク | GOLD | <p>C:\\ClusterStorage\\SourceVolume1</p><p>\\FileServer\\SourceShare1</p>
 | SILVER | <p>C:\\ClusterStorage\\SourceVolume2</p><p>\\FileServer\\SourceShare2</p>
シカゴ | GOLD\_TARGET | <p>C:\\ClusterStorage\\TargetVolume1</p><p>\\FileServer\\TargetShare1</p>
 | SILVER\_TARGET| <p>C:\\ClusterStorage\\TargetVolume2</p><p>\\FileServer\\TargetShare2</p>

この例の環境で仮想マシン (VM1 ～ VM5) の保護を有効にしたときの動作を次の表にまとめます。

**仮想マシン** | **ソース記憶域** | **ソース分類** | **マップされたターゲット記憶域**
---|---|---|---
VM1 | C:\\ClusterStorage\\SourceVolume1 | GOLD | <p>C:\\ClusterStorage\\SourceVolume1</p><p>\\\FileServer\\SourceShare1</p><p>Both GOLD\_TARGET</p>
VM2 | \\FileServer\\SourceShare1 | GOLD | <p>C:\\ClusterStorage\\SourceVolume1</p><p>\\FileServer\\SourceShare1</p> <p>Both GOLD\_TARGET</p>
VM3 | C:\\ClusterStorage\\SourceVolume2 | SILVER | <p>C:\\ClusterStorage\\SourceVolume2</p><p>\\FileServer\\SourceShare2</p>
VM4 | \\FileServer\\SourceShare2 | SILVER |<p>C:\\ClusterStorage\\SourceVolume2</p><p>\\FileServer\\SourceShare2</p><p>Both SILVER\_TARGET</p>
VM5 | C:\\ClusterStorage\\SourceVolume3 | 該当なし | マッピングを構成していないため、Hyper-V ホストの既定の記憶域の場所が使用されます。

## 次のステップ

記憶域マッピングに関する理解が深まったところで、[Azure Site Recovery をデプロイする準備](site-recovery-best-practices.md)を行いましょう。

<!---HONumber=AcomDC_0706_2016-->
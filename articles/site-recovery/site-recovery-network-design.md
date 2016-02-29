<properties
	pageTitle="VMM と Site Recovery のデプロイメントに関する考慮事項 | Microsoft Azure"
	description="この記事では、VMM を Azure Site Recovery と統合する場合の実用的な設計の考慮事項について説明します。"
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
	ms.date="02/15/2016"
	ms.author="raynew"/>

#  VMM と Site Recovery のデプロイメントに関する考慮事項

Azure Site Recovery サービスは、仮想マシンと物理サーバーのレプリケーション、フェールオーバー、復旧を調整してビジネス継続性と障害復旧 (BCDR) 戦略に貢献します。コンピューターを Azure に、またはオンプレミスのセカンダリ データ センターにレプリケートできます。簡単な概要については、「[Azure Site Recovery とは](site-recovery-overview.md)」を参照してください。


## 概要

この記事では、BCDR ソリューションと、System Center VMM や Azure Site Recovery などのインフラストラクチャを設計し、実装するときに役立つ情報について説明します。

BCDR 戦略の目的は、ビジネス アプリケーションを稼働状態に維持すること、およびワークロードやサービスで障害が発生した場合に組織が通常の操作をすばやく再開できるように復元することにあります。障害復旧戦略を策定することは容易ではありません。不測のイベントを予測するのは本質的に難しく、広範囲に及ぶ障害に対処する適切な保護を実装するには高いコストがかかるからです。Site Recovery を使用すれば、保護と、プライマリ データ センターからセカンダリ データ センター (または Azure) へのフェールオーバーとを容易に実装することができます。そのためには、最初にプライマリ データをコピー (レプリケート) し、その後、レプリカを定期的に更新するという方法をとります。

BCDR を計画する上で重要な作業として、目標復旧時間 (RTO) と目標復旧時点 (RPO) を定義する必要があります。そうすることで、データの損失を最小限に抑え (低 RPO)、可能な限り迅速に (低 RTO で) 組織のデータをオンラインに戻すことができます。組織でのネットワーク設計は、RTO および RPO の目標実現の妨げになる可能性があります。堅牢な設計計画は、この障害を回避するのに効果があります。


Site Recovery をデプロイして VMM クラウド内の Hyper-V VM をレプリケートする場合、Site Recovery レプリケーションとフェールオーバー戦略に VMM を統合する方法を考慮する必要があります。

## スタンドアロン VMM サーバーの統合

このトポロジでは、プライマリ サイトの仮想マシンに VMM サーバーをデプロイし、Site Recovery と Hyper-V レプリカを使用して、セカンダリ サイトにこの仮想マシンをレプリケートします。VMM サーバーと、そのサポート SQL Server を同じ仮想マシンにインストールすれば、1 つの VM をインスタンス化するだけで済むので、ダウンタイムを抑えることができます。VMM サービスがリモート SQL Server を使用している場合、まず SQL Server インスタンスを復旧してから VMM サーバーを復旧する必要があります。

Hyper-V レプリカを使用して VM に VMM を 1 つデプロイするには

1. SQL Server がインストールされた VM で VMM を設定します。
2. 管理対象のホストを、この VMM サーバー上のクラウドに追加します。
3. Azure ポータルにログインし、クラウドを保護されるように構成します。
4. VMM サーバーによって保護される必要があるすべての VM についてレプリケーションを有効にします。
5. HYPER-V Manager コンソールに移動し、HYPER-V レプリカを選択し、VMM VM でレプリケーションを有効にします。
6. ASR サービスによって保護されているクラウドに VMM VM が決して追加されないようにします。これにより、Hyper-V レプリケーション設定は ASR で上書きされません。

災害が発生した場合は、次の手順を使用してワークロードを復旧できます。

1. HYPER-V マネージャーを使用して、レプリカ VMM VM を復旧サイトにフェールオーバーします。
2. VMM VM が回復すると、ユーザーはセカンダリ サイトから HYPER-V Recovery Manager にログインできます。
3. 計画外のフェールオーバーが完了すると、ユーザーはプライマリ サイトにあるすべてのリソースにアクセスできます。
4. ワークロードをフェールオーバーするには、VMM VM を手動でセカンダリ サイトにフェールオーバーする必要があります。


### VMM クラスターの統合


[クラスターに VMM をデプロイする](https://technet.microsoft.com/library/gg610675.aspx)と、可用性を向上させ、ハードウェア フェールオーバーを防止することができます。Site Recovery と一緒に VMM クラスターをデプロイする場合は、以下の点に注意してください。

- VMM サーバーは、地理的に離れたサイトにまたがる拡張クラスターにデプロイする必要があります。
- VMM によって使用される SQL Server データベースは、セカンダリ サイト上にレプリカを配置して、SQL Server AlwaysOn 可用性グループで保護する必要があります。
- 災害が発生した場合は、VMM サーバーとそれに対応する SQL Server が自動的に復旧サイトにフェールオーバーされます。Site Recovery を使用してワークロードをフェールオーバーすることができます。

	![拡張された VMM クラスター](./media/site-recovery-network-design/network-design1.png)


## 次のステップ

[学習内容](site-recovery-network-mapping.md): Site Recovery がソースおよびターゲット ネットワークをマップする方法。

<!---HONumber=AcomDC_0218_2016-->
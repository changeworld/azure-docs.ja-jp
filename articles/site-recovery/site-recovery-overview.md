<properties
	pageTitle="Site Recovery とは" 
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
	ms.date="11/29/2015" 
	ms.author="raynew"/>

#  Site Recovery とは

Site Recovery は、オンプレミスのセカンダリ データセンターまたは Azure へのオンプレミス サーバーと仮想マシンのレプリケーションを調整することにより、ビジネス継続性と障害復旧 (BCDR) の戦略を支援する Azure サービスです。Site Recovery でレプリケーションが処理されるため、クリックするだけでフェールオーバーと復旧を開始することができます。[FAQ](site-recovery-faq.md) のよく寄せられる質問の一覧に目を通してください。


## Site Recovery を使用する理由 
- **BCDR のストーリーがシンプルになる**: Site Recovery を使用すると、オンプレミスのワークロードとアプリケーションのレプリケーション、フェールオーバー、復旧が扱いやすくなります。
- **柔軟なレプリケーション**: オンプレミスのサーバー、Hyper-V 仮想マシン、VMware 仮想マシンをレプリケートすることができます。Site Recovery は、最初のレプリケーションに VHD 全体ではなくデータのブロックのみをレプリケートするスマート レプリケーションを使用します。進行中のレプリケーションでは、差分の変更のみがレプリケートされます。Site Recovery は、オフライン データ転送をサポートし、WAN オプティマイザーと連携します。 
- **セカンダリ データセンターが必要なくなる**: Site Recovery を使用すると、データセンター間のレプリケーションを自動化できますが、レプリケート先を Azure にすることで、オンサイトのセカンダリ データセンターを廃止することもできます。レプリケートされたデータは、元の復元性を十分に備えた状態で Azure ストレージに格納されます。


## デプロイ シナリオ

次の表は、Site Recovery でサポートされているレプリケーションのシナリオをまとめたものです。

**REPLICATE** | **レプリケート元** | **レプリケート先** | **記事**
---|---|---|---
VMware 仮想マシン | オンプレミスの VMware サーバー | Azure Storage | [デプロイする](site-recovery-vmware-to-azure.md)
Windows または Linux の物理サーバー | オンプレミスの物理サーバー | Azure Storage | [デプロイする](site-recovery-vmware-to-azure.md)
Hyper-V 仮想マシン | VMM クラウド内のオンプレミスの Hyper-V ホスト サーバー | Azure Storage | [デプロイする](site-recovery-vmm-to-azure.md)
Hyper-V 仮想マシン | オンプレミスの Hyper-V サイト (1 つ以上の Hyper-V ホスト サーバー) | Azure Storage | [デプロイする](site-recovery-hyper-v-site-to-azure.md)
オンプレミスの Hyper-V 仮想マシン| VMM クラウド内のオンプレミスの Hyper-V ホスト サーバー | セカンダリ データセンターの VMM クラウド内のオンプレミス Hyper-V ホスト サーバー | [デプロイする](site-recovery-vmm-to-vmm)
Hyper-V 仮想マシン | VMM クラウド内のオンプレミス Hyper-V ホスト サーバーと SAN ストレージ| セカンダリ データセンターの VMM クラウド内のオンプレミス Hyper-V ホスト サーバーと SAN ストレージ | [デプロイする](site-recovery-vmm-san.md)
VMware 仮想マシン | オンプレミスの VMware サーバー | VMware が実行されているセカンダリ データセンター | [デプロイする](https://microsoft.sharepoint.com/sites/academy/media/AEVD-3-85237) 
Windows または Linux の物理サーバー | オンプレミスの物理サーバー | セカンダリ データセンター | [デプロイする](https://microsoft.sharepoint.com/sites/academy/media/AEVD-3-85237) 

これらを次の図にまとめて示します。

![オンプレミス間](./media/site-recovery-overview/asr-overview-graphic.png)

## 保護できるワークロードとは

Site Recovery は、アプライアンス対応のビジネス継続性に貢献します。Site Recovery を使用すると、Windows とサード パーティ アプリの障害復旧を調整できます。アプリケーションに対応したこの保護機能には、次の特徴があります。


- RPO を使用した近同期レプリケーションにかかる時間は Hyper-V や VMware の連続レプリケーションではわずか 30 秒間であるため、重要度の高いアプリケーションのニーズを満たします。
- アプリケーションとの整合性が取られたスナップショットであるため、単一層または N 層のアプリケーションに適しています。
- SQL Server AlwaysOn と統合できるほか、その他のアプリケーション レベルのレプリケーション テクノロジ (Active Directory のレプリケーション、Exchange DAG、Oracle Data Guard など) と組み合わせて使用できます。
- 柔軟な復旧計画を策定し、1 回のクリックでアプリケーション スタック全体を復旧できます。この計画には、外部スクリプトまたは手動のアクションを含めることもできます。 
- Site Recovery と Azure での高度なネットワーク管理により、IP アドレスの予約、ロード バランサーの構成、低 RTO ネットワーク切り替え用の Azure Traffic Manager の統合など、アプリのケットワーク要件が簡略化されます。
- 豊富な自動化ライブラリでは、運用環境ですぐに使えるアプリケーション固有のスクリプトを提供しています。これらのスクリプトは、ダウンロードして Site Recovery と統合できます。  


詳細については、[Site Recovery で保護できるワークロード](site-recovery-workload.md)に関するページを参照してください。


## 次のステップ

この概要の確認が終わったら、Site Recovery のアーキテクチャの[詳細](site-recovery-components.md)を確認してください。
 

<!---HONumber=AcomDC_1203_2015-->
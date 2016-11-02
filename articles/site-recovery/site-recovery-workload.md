<properties
	pageTitle="Azure Site Recovery で保護できるワークロード" 
	description="Azure Site Recovery は、オンプレミスの仮想マシンと物理サーバーの Azure またはセカンダリ オンプレミス サイトへのレプリケーション、フェールオーバー、および復旧を調整することで、ワークロードやアプリケーションを保護します。" 
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
	ms.date="07/06/2016" 
	ms.author="raynew"/>

# Azure Site Recovery で保護できるワークロード


この記事では、Azure Site Recovery を使用し保護できるワークロードとアプリケーションについて説明します。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。

## 概要

組織には、予定されたダウンタイムや予定外のダウンタイムの際にアプリ、ワークロード、およびデータを利用可能な状態に維持し、できるだけ早く通常の動作状態に復旧させる方法を決定するビジネス継続性および障害復旧 (BCDR) の戦略が必要です。

Site Recovery は、クラウド (Azure) またはセカンダリ サイトへのオンプレミスの物理サーバーおよび仮想マシンのレプリケーションを調整することで、アプリケーション対応の回復ソリューションをデプロイできるようにして、BCDR 戦略を支援する Azure サービスです。アプリが Windows ベースと Linux ベースのどちらであっても、また、物理サーバー、VMware VM、Hyper-V VM のいずれで実行されていても、Site Recovery を使用して、レプリケートの調整、障害復旧テスト、フェールオーバーとフェールバックを実行できます。


Site Recovery は、SharePoint、Exchange、Dynamics、SQL Server、Active Directory などの Microsoft アプリケーションと統合されています。また、Microsoft は、Oracle、SAP、IBM、Red Hat などの主要ベンダーと密接に連携して、Azure などの Microsoft プラットフォームで各ベンダーのアプリケーションやサービスが正常に動作するように取り組んでいます。ソリューションはアプリケーションごとにカスタマイズすることができます。

## アプリケーションの保護に Site Recovery を使用する理由

Site Recovery は、アプリケーション レベルの保護と復旧に次のように役立ちます。

- RPO を使用した近同期レプリケーションにかかる時間はわずか 30 秒間であるため、重要度の高いビジネス アプリケーションのニーズを満たします。
- アプリケーションとの整合性が取られたスナップショットであるため、単一層または複数層のアプリケーションに適しています。
- SQL Server AlwaysOn と統合できるほか、その他のアプリケーション レベルのレプリケーション テクノロジ (AD のレプリケーション、SQL AlwaysOn、Exchange データベース可用性グループ (DAG)、Oracle Data Guard など) と組み合わせて使用できます。
- 柔軟な復旧計画を策定し、1 回のクリックでアプリケーション スタック全体を復旧できます。この計画に、外部スクリプトや手動のアクションを含めることもできます。
- Site Recovery と Azure の高度なネットワーク管理により、IP アドレスの予約、負荷分散の構成、低 RTO ネットワーク切り替え用の Azure Traffic Manager の統合など、アプリのケットワーク要件が簡略化されます。
-  豊富な自動化ライブラリに、運用環境ですぐに使えるアプリケーション固有のスクリプトが用意されています。これらのスクリプトをダウンロードして、復旧計画に統合することができます。



##ワークロードの概要

Site Recovery は、サポート対象の仮想マシンで実行されているすべてのアプリをレプリケートできます。また、Microsoft では、製品チームと連携して、アプリに特化したテストを追加で実施しました。

**ワークロード** | **Hyper-V VM をセカンダリ サイトにレプリケート** | **Hyper-V VM を Azure にレプリケート** | **VMware VM をセカンダリ サイトにレプリケート** | **VMware VM を Azure にレプリケート**
---|---|---|---|---
Active Directory、DNS | Y | Y | Y | Y 
Web アプリケーション (IIS、SQL) | Y | Y | Y | Y
SCOM | Y | Y | Y | Y
SharePoint | Y | Y | Y | Y
SAP<br/><br/>SAP サイトを Azure にレプリケート (非クラスターの場合) | Y (Microsoft によってテスト済み) | Y (Microsoft によってテスト済み) | Y (Microsoft によってテスト済み) | Y (Microsoft によってテスト済み)
Exchange (DAG 以外) | Y | 近日対応予定 | Y | Y
リモート デスクトップ/VDI | Y | Y | Y | 該当なし 
Linux (オペレーティング システムおよびアプリケーション) | Y (Microsoft によってテスト済み) | Y (Microsoft によってテスト済み) | Y (Microsoft によってテスト済み) | Y (Microsoft によってテスト済み) 
Dynamics AX | Y | Y | Y | Y
Dynamics CRM | Y | 近日対応予定 | Y | 近日対応予定
Oracle | Y (Microsoft によってテスト済み) | Y (Microsoft によってテスト済み) | Y (Microsoft によってテスト済み) | Y (Microsoft によってテスト済み)
Windows ファイル サーバー | Y | Y | Y | Y


## Active Directory と DNS のレプリケート

Active Directory と DNS インフラストラクチャは多くのエンタープライズ アプリにとって不可欠な要素です。障害復旧の際は、これらのインフラストラクチャ コンポーネントを保護して復旧した後に、ワークロードとアプリケーションを復旧することが必要になります。

Site Recovery を使用すると、Active Directory と DNS に合わせて完全な自動障害復旧計画を作成できます。たとえば、プライマリ サイトからセカンダリ サイトに SharePoint や SAP をフェールオーバーする場合は、まず Active Directory をフェールオーバーする復旧計画を策定してから、Active Directory に依存する他のアプリをフェールオーバーするためのアプリごとの復旧計画を追加で策定します。

Active Directory と DNS の保護の詳細については、[こちら](site-recovery-active-directory.md)を参照してください。

## SQL Server の保護

SQL Server は、オンプレミスのデータセンターの多くのビジネス アプリにデータ サービス基盤を提供します。Site Recovery を SQL Server HA/DR テクノロジと共に使用して、SQL Server を使用する多層エンタープライズ アプリを保護できます。Site Recovery で提供されるものは次のとおりです。

- SQL Server に対応したシンプルでコスト効率の良い障害復旧ソリューション。SQL Server のスタンドアロン サーバーとクラスターの複数のバージョンとエンドポイントを Azure またはセカンダリ サイトにレプリケートします。
- SQL AlwaysOn 可用性グループとの統合。これにより、Azure Site Recovery の復旧計画を使用してフェールオーバーとフェールバックを管理できます。
- SQL Server データベースなど、アプリケーションのすべての層に対応するエンド ツー エンドの復旧計画。
- Site Recovery による、ピーク時の負荷に対応した SQL Server のスケーリング。このために、Azure でより大きな IaaS 仮想マシン サイズに負荷を "バースト" します。
- SQL Server 障害復旧の容易なテスト。テスト フェールオーバーを実行して、運用環境に影響を与えることなく、データの分析とコンプライアンス チェックを実施できます。

SQL Server の保護の詳細については、[こちら](site-recovery-sql.md)を参照してください。

##SharePoint の保護

Azure Site Recovery は、SharePoint デプロイメントの保護に次のように役立ちます。

- 障害復旧用のスタンバイ ファームを用意する必要がなくなり、関連するインフラストラクチャ コストも削減されます。Site Recovery を使用して、ファーム全体 (Web 層、アプリケーション層、データベース層) を Azure またはセカンダリ サイトにレプリケートできます。
- アプリケーションのデプロイと管理が簡易になります。プライマリ サイトにデプロイされた更新プログラムは自動的にレプリケートされるため、ファームをセカンダリ サイトにフェールオーバーして復旧した後で利用できます。また、スタンバイ ファームを最新の状態に保つための複雑な管理が不要になり、関連するコストも削減されます。
- テストやデバッグ用に必要に応じて運用環境に似たレプリカ環境のコピーを作成することで、SharePoint アプリケーションの開発とテストが簡単になります。
- Site Recovery を使用して SharePoint のデプロイメントを Azure に移行することで、クラウドへの移行が簡単になります。

SharePoint の保護の詳細については、[こちら](https://gallery.technet.microsoft.com/SharePoint-DR-Solution-f6b4aeae)を参照してください。


## Dynamics AX の保護

Azure Site Recovery は、次のような点で Dynamics AX ERP ソリューションの保護に役立ちます。

- Azure またはセカンダリ サイトへの Dynamics AX 環境全体 (Web 層、AOS 層、データベース層、SharePoint) のレプリケーションを調整します。
- クラウド (Azure) への Dynamics AX デプロイメントの移行が簡略化されます。
- テストやデバッグ用に必要に応じて運用環境に似たコピーを作成することで、Dynamics AX アプリケーションの開発とテストが簡単になります。

Dynamic AX の保護の詳細については、[こちら](https://gallery.technet.microsoft.com/Dynamics-AX-DR-Solution-b2a76281)を参照してください。

## RDS の保護 

リモート デスクトップ サービス (RDS) を使用すると、ユーザーが仮想デスクトップ インフラストラクチャ (VDI)、セッションベースのデスクトップ、アプリケーションを使用して、どこにいても作業できるようになります。Azure Site Recovery を使用すると、次のことができます。

- 管理対象か管理対象外かを問わず、プールされた仮想デスクトップをセカンダリ サイトにレプリケートし、リモート アプリケーションとセッションをセカンダリ サイトまたは Azure にレプリケートできます。
- レプリケートできるシナリオは次のとおりです。

**RDS** | **Hyper-V VM をセカンダリ サイトにレプリケート** | **Hyper-V VM を Azure にレプリケート** | **VMware VM をセカンダリ サイトにレプリケート** | **VMware VM を Azure にレプリケート** | **物理サーバーをセカンダリ サイトにレプリケート** | **物理サーバーを Azure にレプリケート**
---|---|---|---|---|---|---
**プールされた仮想デスクトップ (管理対象外)** | あり | なし | あり | なし | あり | いいえ
**プールされた仮想デスクトップ (管理対象、UPD なし)** | あり | なし | あり | なし | あり | いいえ
**リモート アプリケーションとデスクトップ セッション (UPD なし)** | あり | はい | はい | はい | はい | あり


RDS の保護の詳細については、[こちら](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb)を参照してください。


## Exchange の保護

Site Recovery は、Exchange の保護に次のように役立ちます。

- 単一サーバーやクラスター化されていないサーバーなど、小規模な Exchange デプロイメントの場合、Site Recovery では Azure またはセカンダリ サイトへのレプリケートとフェールオーバーを実行できます。
- 大規模なデプロイメントでは、Exchange DAG と統合します。
- Exchange DAG は、企業における Exchange の障害復旧に推奨されるソリューションです。Site Recovery の復旧計画に DAG を含めることで、サイト全体の DAG のフェールオーバーを調整できます。


Exchange の保護の詳細については、[こちら](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6)を参照してください。

## SAP の保護

Site Recovery を使用して、SAP のデプロイメントを次のように保護できます。

- 複数のデプロイメント層を Azure またはセカンダリ サイトにレプリケートして、SAP デプロイメント全体を保護できます。
- Site Recovery を使用して SAP デプロイを Azure に移行することで、クラウド移行を簡略化します。
- アプリケーションのテストとデバッグ用に必要に応じて運用環境に似たコピーを作成できるため、SAP の開発とテストが簡単になります。

SAP の保護の詳細については、[こちら](http://aka.ms/asr-sap)を参照してください。

## 次のステップ

Site Recovery のデプロイを[準備](site-recovery-best-practices.md)します。

<!---HONumber=AcomDC_0706_2016-->
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
	ms.date="12/01/2015" 
	ms.author="raynew"/>

# Azure Site Recovery で保護できるワークロード

Azure Site Recovery を使用すると、組織のビジネス継続性と障害復旧 (BCDR) 戦略に貢献する、アプリケーション対応の可用性ソリューションをデプロイできます。Windows Server または Linux ベースのアプリ、Microsoft のエンタープライズ アプリケーション、他のベンダー製のサービスのいずれであっても、Azure Site Recovery を使用して、障害復旧、オンデマンドの開発環境とテスト環境、クラウド移行を実現できます。

Site Recovery は、SharePoint、Exchange、Dynamics、SQL Server、Active Directory などの Microsoft アプリケーションと統合されています。また、Microsoft は Oracle、SAP、IBM、Red Hat などの主要ベンダーと密接に連携して、Azure や Hyper-V などの Microsoft プラットフォームで各ベンダーのアプリケーションやサービスが正常に動作するように取り組んでいます。障害復旧ソリューションは、特定のアプリケーションごとにカスタマイズできます。


##主な特徴
アプリケーション レベルの保護および復旧の戦略に役立つ Azure Site Recovery の特徴は次のとおりです。

- RPO を使用した近同期レプリケーションにかかる時間はわずか 30 秒間であるため、重要度の高いアプリケーションのニーズを満たします。
- アプリケーションとの整合性が取られたスナップショットであるため、単一層または N 層のアプリケーションに適しています。
- SQL Server AlwaysOn と統合できるほか、その他のアプリケーション レベルのレプリケーション テクノロジ (AD のレプリケーション、SQL AlwaysOn、Exchange データベース可用性グループ (DAG)、Oracle Data Guard など) と組み合わせて使用できます。
- 柔軟な復旧計画を策定し、1 回のクリックでアプリケーション スタック全体を復旧できます。この計画には、外部スクリプトまたは手動のアクションを含めることもできます。 
- Site Recovery と Azure での高度なネットワーク管理により、IP アドレスの予約、ロード バランサーの構成、低 RTO ネットワーク切り替え用の Azure Traffic Manager の統合など、アプリのケットワーク要件が簡略化されます。
-  豊富な自動化ライブラリでは、運用環境ですぐに使えるアプリケーション固有のスクリプトを提供しています。これらのスクリプトは、ダウンロードして Site Recovery と統合できます。



##ワークロードの概要

Site Recovery レプリケーションのテクノロジは、仮想マシンで実行されているすべてのアプリケーションと互換性があります。さらに、各アプリケーションのサポートを強化するために、アプリケーション製品チームと協力して、追加のテストを実施しました。

**ワークロード** | <p>**Hyper-V VM のレプリケート**</p> <p>**(セカンダリ サイトへ)**</p> | <p>**Hyper-V VM のレプリケート**</p> <p>**(Azure へ)**</p> | <p>**VMware VM のレプリケート**</p> <p>**(セカンダリ サイトへ)**</p> | <p>**VMware VM のレプリケート**</p><p>**(Azure へ)****</p> ---|---|---|---|--- Active Directory、DNS | Y | Y | Y | Y Web アプリ (IIS、SQL) | Y | Y | Y | Y SCOM | Y | Y | Y | Y Sharepoint | Y | Y | Y | Y <p>SAP</p><p>クラスター以外の Azure に SAP サイトをレプリケート</p> | Y (Microsoft によってテスト済み) | Y (Microsoft によってテスト済み) | Y (Microsoft によってテスト済み) | Y (Microsoft によってテスト済み) Exchange (非 DAG) | Y | 近日対応予定 | Y | Y リモート デスクトップ/VDI | Y | Y | Y | N/A <p>Linux</p> <p>(オペレーティング システムとアプリ)</p> | Y (Microsoft によってテスト済み) | Y (Microsoft によってテスト済み) | Y (Microsoft によってテスト済み) | Y (Microsoft によってテスト済み) Dynamics AX | Y | Y | Y | Y Dynamics CRM | Y | 近日対応予定 | Y | 近日対応予定 Oracle | Y (Microsoft によってテスト済み) | Y (Microsoft によってテスト済み) | Y (Microsoft によってテスト済み) | Y (Microsoft によってテスト済み) Windows File Server | Y | Y | Y | Y

##Active Directory と DNS の保護

SharePoint、Dynamics AX、SAP など、すべてのエンタープライズ アプリケーションは、Active Directory と DNS のインフラストラクチャに依存しています。BCDR ソリューションの一部として、これらのインフラストラクチャ コンポーネントを保護および復旧した後に、ワークロードとアプリケーションを復旧することが必要になります。

Site Recovery を使用すると、Active Directory と DNS に合わせて完全な自動障害復旧計画を作成できます。たとえば、プライマリ サイトで複数のアプリケーション (SharePoint や SAP など) に Active Directory を使用していて、サイト全体をフェールオーバーする場合は、まず復旧計画を使用して Active Directory をフェールオーバーしてから、アプリケーション固有の復旧計画を使用して、Active Directory に依存するアプリケーションをフェールオーバーします。

[詳細情報](http://aka.ms/asr-ad)

##SQL Server の保護

SQL Server は、オンプレミスのデータセンターの多くのビジネス アプリケーションにデータ サービスの基盤を提供します。Site Recovery と SQL Server の HA/DR テクノロジは補完的で、多層エンタープライズ アプリケーションにエンド ツー エンド保護を提供するためにまとめて使用できます。SQL Server 環境では、Azure Site Recovery による次の利点があります。

- Azure またはセカンダリ サイトでのスタンドアロンまたはクラスター化された SQL サーバーの簡単な保護とレプリケーション。多くの SQL Server のバージョンやエディションに適切なシンプルでコスト効率の良い障害復旧ソリューションが提供されます。
- SQL AlwaysOn 可用性グループとの統合。これにより、Azure Site Recovery の復旧計画を使用してフェールオーバー プロセスとフェールバック プロセスを管理できます。
- SQL Server データベースなど、アプリケーション スタック全体に対応するエンド ツー エンドの復旧計画。
- Azure Site Recovery による、ピーク時の負荷に対応した SQL Server のスケールアップ。このためには、Azure でより多くの IaaS 仮想マシン サイズに負荷を "バースト" します。
- Azure Site Recovery を使用した Azure またはセカンダリ サイトでの SQL Server のテスト。テスト フェールオーバーは、運用環境に影響を与えることなく、分析データ コンプライアンス チェックのために使用できます。

[詳細情報](http://aka.ms/asr-sql)

##SharePoint の保護

Azure Site Recovery を使用すると、SharePoint のデプロイを保護することができます。Site Recovery を使用した場合、次のことを実現できます。

- 障害復旧用のスタンバイ ファームを用意する必要がなくなり、関連するインフラストラクチャ コストもなくなります。Site Recovery を使用すると、ファーム全体 (Web 層、アプリケーション層、データベース層) を Azure またはセカンダリ サイトで簡単に保護できます。
- アプリケーションのデプロイと管理が簡易になります。プライマリ サイトにデプロイされた更新は自動的にレプリケートされるため、ファームがセカンダリ サイトで復旧されると使用できるようになります。その結果、スタンバイ ファームを最新の状態に保つための複雑な管理が不要になり、コストが削減されます。
- テストやデバッグ用に必要に応じて、運用環境に似たレプリカ環境のコピーを作成することで、SharePoint アプリケーションの開発とテストが簡単になります。
- Site Recovery を使用して SharePoint のデプロイを Azure に移行することで、クラウドへの移行が簡単になります。

[詳細情報](http://aka.ms/asr-sharepoint)


## Dynamics AX の保護

Azure Site Recovery は、Dynamics AX ERP ソリューションを保護する際に役立ちます。

- Site Recovery を使用すると、Dynamics AX 環境全体 (Web 層、AOS 層、データベース層、SharePoint) を Azure またはセカンダリ サイトで保護できるようになります。
- Site Recovery を使用して Dynamics AX のデプロイを Azure に移行することで、クラウドへの移行が簡単になります。
- テストやデバッグ用に必要に応じて運用環境に似たコピーを作成することで、Dynamics AX アプリケーションの開発とテストが簡単になります。

[詳細については、こちらを参照してください。](http://aka.ms/asr-dynamics)

## RDS の保護 
リモート デスクトップ サービスでは、仮想デスクトップ インフラストラクチャ (VDI)、セッションベースのデスクトップ、アプリケーションを利用できるので、ユーザーはどこでも作業できるようになります。Site Recovery を使用すると、管理対象または管理対象外のプールされた仮想デスクトップをセカンダリ サイトで保護し、リモート アプリケーションとセッションをセカンダリ サイトまたは Azure で保護することができます。

[詳細情報](http://aka.ms/asr-rds)


## Exchange の保護

Microsoft Exchange には、高可用性と障害復旧のサポートが組み込まれています。Exchange DAG と Azure Site Recovery は連携させることができます。

- Exchange DAG は、企業における Exchange の障害復旧に推奨されるソリューションです。Site Recovery の復旧計画に DAG を含めることで、サイト全体の DAG のフェールオーバーを調整できます。
- 単一サーバーやクラスター化されていないサーバーなど、小規模なデプロイの場合、Site Recovery では Azure またはセカンダリ サイトでの保護とフェールオーバーを実行できます。

[詳細情報](http://aka.ms/asr-exchange)

## SAP の保護

SAP デプロイを保護するには、Site Recovery を使用します。

- さまざまな層がある SAP デプロイ全体を Azure またはセカンダリ サイトで保護できます。
- Site Recovery を使用して SAP デプロイを Azure に移行することで、クラウド移行を簡略化します。
- アプリケーションのテストとデバッグ用に必要に応じて運用環境に似たコピーを作成できるので、SAP の開発とテストが簡単になります。

[詳細情報](http://aka.ms/asr-sap)

<!---HONumber=AcomDC_1203_2015-->
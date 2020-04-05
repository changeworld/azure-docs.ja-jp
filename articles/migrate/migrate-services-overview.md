---
title: Azure Migrate について
description: Azure Migrate サービスについて説明します。
ms.topic: overview
ms.date: 03/22/2020
ms.custom: mvc
ms.openlocfilehash: a9723f15d496393d27bdd227ec1121a7878b37e0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "80127738"
---
# <a name="about-azure-migrate"></a>Azure Migrate について

この記事では、Azure Migrate サービスの概要を簡単に説明します。

Azure Migrate では、オンプレミスのサーバー、インフラストラクチャ、アプリケーション、データの評価および Azure への移行を行うための一元的なハブが提供されています。 Azure Migrate には次の機能が用意されています。

- **統合された移行プラットフォーム**:Azure への移行の過程を開始、実行、追跡するための単一のポータル。
- **ツールの範囲**:評価と移行のためのさまざまなツール。 ツールには、Azure Migrate:Server Assessment と Azure Migrate:Server Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。 Azure Migrate は、他の Azure サービスや、他のツールおよび独立系ソフトウェア ベンダー (ISV) オファリングと統合されています。
- **評価と移行**: Azure Migrate ハブでは、以下の評価と移行を行うことができます。
    - **サーバー**:オンプレミスのサーバーを評価し、Azure VM に移行します。
    - **データベース**: オンプレミスのデータベースを評価し、Azure SQL DB または Azure SQL Managed Instance に移行します。
    - **Web アプリケーション**: Azure App Service Assistant を使用して、オンプレミスの Web アプリケーションを評価し、Azure App Service に移行します。
    - **仮想デスクトップ**: オンプレミスの仮想デスクトップ インフラストラクチャ (VDI) を評価し、Azure の Windows Virtual Desktop に移行します。
    - **Data**:Azure Data Box 製品を使用して、大量のデータを迅速かつコスト効果よく Azure に移行します。 


## <a name="integrated-tools"></a>統合されているツール

Azure Migrate Hub には次のツールが含まれています。

**ツール** | **評価および移行** | **詳細**
--- | --- | ---
**Azure Migrate:Server Assessment** | サーバーを評価します。 | Azure への移行の準備として、オンプレミスの VMware VM、Hyper-V VM、および物理サーバーを検出して評価します。
**Azure Migrate:Server Migration** | サーバーを移行します。 | VMware VM、Hyper-V VM、物理サーバー、その他の仮想マシンとパブリック クラウド VM を Azure に移行します。 
**Database Migration Assistant (DMA)** | Azure SQL DB、Azure SQL Managed Instance、または SQL Server が実行される Azure VM への移行に関して、オンプレミスの SQL Server データベースを評価します。 | DMA は、移行を妨げる可能性のある問題を特定するのに役立ちます。 サポートされていない機能や、移行後に役に立つ新機能が明らかになり、データベース移行の適切なパスを特定するのに役立ちます。 [詳細については、こちらを参照してください](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)。
**Database Migration Service (DMS)** | SQL、Azure SQL DB、および Azure SQL Managed Instance を実行している Azure VM にオンプレミスのデータベースを移行します。 | DMS の[詳細を参照](https://docs.microsoft.com/azure/dms/dms-overview)してください。
**Movere** | サーバーを評価します。 | Movere の[詳細を参照](#movere)してください。
**Web App Migration Assistant** | オンプレミスの Web アプリを評価し、Azure に移行します。 |  Azure App Service に移行するためにオンプレミスの Web サイトを評価するには、Azure App Service Migration Assistant を使用します。<br/><br/> .NET と PHP の Web アプリを Azure に移行するには、Assistant を使用します。 Azure App Service Migration Assistant について、[詳しくはこちら](https://appmigration.microsoft.com/)をご覧ください。
**Azure Data Box** | オフライン データの移行。 | Azure Data Box 製品を使用して、大量のデータを Azure にオフラインで移動します。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/databox/)。

## <a name="isv-integration"></a>ISV 統合

Azure Migrate は、多数の ISV オファリングと統合されています。 

**ISV**    | **機能**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | サーバーの移行
[Cloudamize](https://www.cloudamize.com/platform) | サーバーの評価
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | サーバーの評価と移行
[Device 42](https://docs.device42.com/) | サーバーの評価
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | VDI の評価
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | サーバーの移行
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | サーバーの評価
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | サーバーとデータベースの評価


## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate Server Assessment ツール

Azure Migrate:Server Assessment ツールを使うと、オンプレミスの VMware VM、Hyper-V VM、物理サーバーを検出し、Azure への移行について評価することができます。 ツールには次のような機能があります。

- **Azure の準備状況:** オンプレミスのコンピューターで Azure に移行する準備ができているかどうかを評価します。
- **Azure のサイズ変更:** 移行後の Azure VM のサイズを見積もります。
- **Azure コストの見積もり:** Azure でオンプレミス サーバーを実行する場合の推定コスト。
- **依存関係の分析:** Server Assessment と[依存関係の分析](concepts-dependency-visualization.md)を使用すると、サーバー間の依存関係を効率的に特定し、相互に依存するサーバーを Azure に移動するための戦略を最適化できます。


Server Assessment では、オンプレミスに展開されている軽量の [Azure Migrate アプライアンス](migrate-appliance.md)が使用されます。

- アプライアンスは、VM または物理サーバーで実行されます。 ダウンロードしたテンプレートを使用して、簡単にインストールできます。
- アプライアンスはオンプレミスのマシンを検出し、マシンのメタデータとパフォーマンス データを Azure Migrate に継続的に送信します。
- アプライアンスによる検出はエージェントレスです。 検出されたマシンには何もインストールされません。
- アプライアンスの検出後、検出されたマシンをグループ化し、グループの評価を実行します。

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate Server Migration ツール

Azure Migrate:Server Migration ツールにより、オンプレミスの VMware VM、Hyper-V VM、物理サーバー、その他の仮想マシン、パブリック クラウド VM を Azure に移行できます。 マシンは、評価後でも、評価なしでも移行することができます。 

VMware VM のエージェントレス移行および Hyper-V VM の移行の場合、Server Migration では、オンプレミスに展開されている Azure Migrate アプライアンスが使用されます。 また、アプライアンスは、サーバーの評価を設定する場合にも使用されます。これについては、前のセクションで説明されています。


## <a name="selecting-assessmentmigration-tools"></a>評価および移行ツールの選択

Azure Migrate Hub で、評価または移行に使用するツールを選択し、それを Azure Migrate プロジェクトに追加します。 ISV ツールまたは Movere を追加する場合は、次のようにします。

- 使い始めるには、ツールの指示に従って、ライセンスを取得するか、無料試用版にサインアップします。 ツールのライセンスは、ISV またはツールによって決定されます。 
- 各ツールには、Azure Migrate に接続するためのオプションがあります。 ツールの指示に従って接続します。
- すべてのツールにわたって、移行の過程を Azure Migrate プロジェクト内から一元的に追跡します。


## <a name="movere"></a>Movere

Movere は、1 日以内に IT 環境全体を正確に表現することによってビジネス インテリジェンスを向上させる SaaS プラットフォームです。 組織の成長、変化、およびデジタル最適化に対応して、このソリューションは、プラットフォーム、アプリケーション、または地理的な場所に関係なく、企業が環境の把握と制御について自信が持てるようにします。 Movere は、Microsoft によって[買収](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/)され、スタンドアロンのオファーとしては販売されなくなりました。  Movere は、Microsoft Solution Assessment および Cloud Economics Programs を通じて入手できます。 Movere の[詳細を参照](https://www.movere.io)してください。 

また、組み込みの移行サービスである Azure Migrate も参照することをお勧めします。 Azure Migrate は、クラウドへの移行を簡略化する中央ハブを提供します。 このハブは、物理サーバー、仮想サーバー、データベース、アプリケーションなど、さまざまなワークロードに対する包括的なサポートを備えています。 エンド ツー エンドの可視性により、検出、評価、および移行全体の進行状況を簡単に追跡できます。 また、Azure Migrate には Azure とパートナー ISV の両方のツールが組み込まれており、仮想サーバーおよび物理サーバーの検出、パフォーマンスベースの適切なサイズ設定、コスト計画、インポートベースの評価、エージェントレスなアプリケーション依存関係分析など、広範な機能があります。 使用の開始にあたって専門家の支援が必要な場合は、Microsoft の熟練した [Azure Expert マネージド サービス プロバイダー](https://azure.microsoft.com/partners)が工程全体にわたってお手伝いします。 [Azure Migrate Web サイト](https://azure.microsoft.com/services/azure-migrate/)を確認してください。 
 

## <a name="azure-migrate-versions"></a>Azure Migrate のバージョン

Azure Migrate サービスのバージョンには、次の 2 つがあります。

- **現在のバージョン**: Azure Migrate プロジェクトの作成、オンプレミス マシンの検出、評価と移行の調整を行う場合はこちらのバージョンを使用します。 このバージョンの新機能については[こちらを参照してください](whats-new.md)。
- **以前のバージョン**: 旧バージョンの Azure Migrate (オンプレミスの VMware VM の評価のみサポート) を使用していた場合、今後は最新バージョンを使用してください。 前のバージョンを使用して Azure Migrate プロジェクトを作成することはできなくなりました。新しい検出を実行しないことをお勧めします。 既存のプロジェクトにアクセスするには、Azure portal で、 **[Azure Migrate]** を検索して選択します。 **Azure Migrate** ダッシュボードに、通知と、過去の Azure Migrate プロジェクトにアクセスするためのリンクが表示されます。



## <a name="next-steps"></a>次のステップ

- チュートリアルを実行し、[VMware VM](tutorial-prepare-vmware.md)、[Hyper-V VM](tutorial-prepare-hyper-v.md)、または[物理サーバー](tutorial-prepare-physical.md)を評価します。
- Azure Migrate について[よく寄せられる質問を確認](resources-faq.md)します。

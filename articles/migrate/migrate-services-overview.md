---
title: Azure Migrate について
description: Azure Migrate サービスについて説明します。
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: fd8806a02e48481042eb756a0a077d801583cd2e
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870774"
---
# <a name="about-azure-migrate"></a>Azure Migrate について

この記事では、Azure Migrate サービスの概要を簡単に説明します。

Azure Migrate では、オンプレミスのサーバー、インフラストラクチャ、アプリケーション、データを評価し、Azure への移行を行うための一元化されたハブが提供されます。 以下が提供されます。

- **統合された移行プラットフォーム**:Azure への移行を開始、実行、追跡するための単一のポータル。
- **ツールの範囲**:評価と移行のためのさまざまなツール。 Azure Migrate ツールには、Azure Migrate: Discovery and Assessment と Azure Migrate: Server Migration が含まれます。 また、Azure Migrate は、他の Azure サービスやツール、さらに独立系ソフトウェア ベンダー (ISV) オファリングと統合されています。
- **評価と移行**: Azure Migrate ハブでは、以下の評価と移行を行うことができます。
    - **Windows、Linux、SQL Server**: SQL Server インスタンスを含むオンプレミスのサーバーを評価し、それらを Azure 仮想マシンまたは Azure VMware Solution (AVS) (プレビュー) に移行します。
    - **データベース**: オンプレミスのデータベースを評価し、Azure SQL Database または SQL Managed Instance に移行します。
    - **Web アプリケーション**: Azure App Service Migration Assistant を使用して、オンプレミスの Web アプリケーションを評価し、Azure App Service に移行します。
    - **仮想デスクトップ**: オンプレミスの仮想デスクトップ インフラストラクチャ (VDI) を評価し、Azure の Windows Virtual Desktop に移行します。
    - **Data**:Azure Data Box 製品を使用して、大量のデータを迅速かつコスト効果よく Azure に移行します。

## <a name="integrated-tools"></a>統合されているツール

Azure Migrate ハブには、次のツールが含まれています。

**ツール** | **評価と移行** | **詳細**
--- | --- | ---
**Azure Migrate: Discovery and Assessment** | SQL を含むサーバーの検出と評価 | Azure への移行の準備として、オンプレミスの VMware VM、Hyper-V VM、および物理サーバーを検出して評価します。
**Azure Migrate: Server Migration** | サーバーの移行 | VMware VM、Hyper-V VM、物理サーバー、その他の仮想サーバー、パブリック クラウド VM を Azure に移行します。
**Data Migration Assistant** | Azure SQL Database、Azure SQL Managed Instance、または SQL Server を実行する Azure VM に移行する SQL Server データベースを評価します。 | Data Migration Assistant は、SQL Server を評価するためのスタンドアロン ツールです。移行を妨げる可能性のある問題の特定に役立ちます。 サポートされていない機能、移行後に役に立つ新機能、データベースの移行の適切なパスが明らかになります。 [詳細については、こちらを参照してください](/sql/dma/dma-overview)。
**Azure Database Migration Service** | オンプレミスのデータベースを、SQL Server を実行している Azure VM、Azure SQL Database、または SQL Managed Instance に移行します。 | Database Migration Service の[詳細を参照](../dms/dms-overview.md)してください。
**Movere** | サーバーの評価 | Movere の[詳細を参照](#movere)してください。
**Web App Migration Assistant** | オンプレミスの Web アプリを評価し、Azure に移行します。 |  Azure App Service に移行するためにオンプレミスの Web サイトを評価するには、Azure App Service Migration Assistant を使用します。<br/><br/> .NET と PHP の Web アプリを Azure に移行するには、Migration Assistant を使用します。 Azure App Service Migration Assistant の[詳細を参照](https://appmigration.microsoft.com/)してください。
**Azure Data Box** | オフライン データを移行します | Azure Data Box 製品を使用して、大量のオフライン データを Azure に移動します。 [詳細については、こちらを参照してください](../databox/index.yml)。

> [!NOTE]
> Azure Government をご利用の場合、外部の統合ツールや ISV オファリングから Azure Migrate にデータを送信することができません。 別途ツールをご利用ください。

## <a name="isv-integration"></a>ISV 統合

Azure Migrate は、いくつかの ISV オファリングと統合されています。 

**ISV**    | **機能**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | サーバーを移行します。
[Cloudamize](https://www.cloudamize.com/platform) | サーバーを評価します。
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | サーバーを評価して移行します。
[Device42](https://docs.device42.com/) | サーバーを評価します。
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | VDI を評価します。
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | サーバーを移行します。
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | サーバーを評価します。
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | サーバーとデータベースを評価します。
[Zerto](https://go.microsoft.com/fwlink/?linkid=2152102) | サーバーを移行します。

## <a name="azure-migrate-discovery-and-assessment-tool"></a>Azure Migrate: Discovery and Assessment ツール

Azure Migrate: Discovery and Assessment ツールを使うと、オンプレミスの VMware VM、Hyper-V VM、物理サーバーを検出し、Azure への移行について評価することができます。 

ツールには次のような機能があります。

- **Azure 対応性**: オンプレミスのサーバーで Azure に移行する準備ができているかどうかを評価します。
- **Azure のサイジング**: 移行後の Azure VM/Azure SQL 構成のサイズまたは Azure VMware Solution ノードの数を推定します。
- **Azure コストの見積もり**: Azure でオンプレミス サーバーを実行する場合のコストを見積もります。
- **依存関係の分析**: 相互に依存するサーバーを Azure に移動できるように、サーバー間の依存関係と最適化戦略を特定します。 [依存関係の分析](concepts-dependency-visualization.md)による検出と評価の詳細を参照してください。

Discovery and Assessment では、オンプレミスに展開されている軽量の [Azure Migrate アプライアンス](migrate-appliance.md)が使用されます。

- アプライアンスは、VM または物理サーバーで実行されます。 ダウンロードしたテンプレートを使用して、簡単にインストールできます。
- アプライアンスにより、オンプレミスのサーバーが検出されます。 また、サーバーのメタデータとパフォーマンス データが Azure Migrate に継続的に送信されます。
- アプライアンスによる検出はエージェントレスです。 検出されたサーバーには何もインストールされません。
- アプライアンスによる検出後、検出されたサーバーをグループ化し、各グループの評価を実行できます。


## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: Server Migration ツール

Azure Migrate: Server Migration ツールは、サーバーを Azure に移行する際に役立ちます。

**移行** | **詳細**
--- | ---
オンプレミスの VMware VM | エージェントレスまたはエージェントベースの移行を使用して、VM を Azure に移行します。<br/><br/> エージェントレス移行の場合、Discovery and Assessment が VMware VM の検出と評価に使用できる Azure Migrate アプライアンスが、Server Migration でも使用されます。<br/><br/> エージェントベース移行の場合は、レプリケーション アプライアンスが Server Migration で使用されます。
オンプレミスの Hyper-V VM | VM を Azure に移行します。<br/><br/> Server Migration では、Hyper-V ホストにインストールされているプロバイダー エージェントが移行に使用されます。
オンプレミスの物理サーバーまたは他のクラウドでホストされているサーバー | 物理サーバーを Azure に移行することができます。 また、他の仮想化されたサーバーや他のパブリック クラウドの VM を、移行の目的で物理サーバーとして扱うことで移行することができます。 Server Migration では、レプリケーション アプライアンスが移行に使用されます。


## <a name="selecting-assessment-and-migration-tools"></a>評価および移行ツールの選択

Azure Migrate ハブで、評価または移行に使用するツールを選択し、それをプロジェクトに追加します。 ISV ツールまたは Movere を追加する場合は、次のようにします。

- 使い始めるには、ツールの指示に従って、ライセンスを取得するか、無料試用版にサインアップします。 ISV またはツールごとに、ツールのライセンスが指定されています。
- 各ツールには、Azure Migrate に接続するためのオプションがあります。 ツールの指示に従って接続します。
- プロジェクト内から、すべてのツールにわたって移行を追跡します。

## <a name="movere"></a>Movere

Movere は、SaaS (サービスとしてのソフトウェア) プラットフォームです。 Movere では、1 日以内に IT 環境全体を正確に表現することによってビジネス インテリジェンスを向上させることができます。 組織や企業は、成長したり、変化したり、デジタル最適化を行ったりします。 そのような際に Movere を使用すると、プラットフォーム、アプリケーション、地理的な場所に関係なく、自信を持って環境の確認や制御を行うことができます。

Movere は、Microsoft によって[買収](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/)されたため、スタンドアロンのオファリングとしては販売されなくなりました。 Movere は、Microsoft Solution Assessment および Microsoft Cloud Economics Program を通じて入手できます。 Movere の[詳細を参照](https://www.movere.io)してください。

また、組み込みの移行サービスである Azure Migrate も参照することをお勧めします。 Azure Migrate では、クラウド移行を簡略化する中央ハブが提供されます。 このハブでは、物理サーバー、仮想サーバー、データベース、アプリケーションなどのワークロードが包括的にサポートされます。 エンド ツー エンドの可視性により、検出、評価、および移行全体の進行状況を簡単に追跡できます。

Azure Migrate には Azure のツールとパートナー ISV のツールの両方が組み込まれているため、次のような幅広い機能を利用できます。

- 仮想サーバーと物理サーバーの検出。
- パフォーマンスベースの適切なサイジング。
- コストの計画。
- インポートベースの評価。
- エージェントレス アプリケーションの依存関係分析。

使用の開始にあたって専門家の支援が必要な場合は、Microsoft の熟練した [Azure Expert マネージド サービス プロバイダー](https://azure.microsoft.com/partners)がお手伝いします。 [Azure Migrate Web サイト](https://azure.microsoft.com/services/azure-migrate/)を確認してください。 

## <a name="azure-migrate-versions"></a>Azure Migrate のバージョン

Azure Migrate サービスには、次の 2 つのバージョンがあります。

- **現在のバージョン**: プロジェクトの作成、オンプレミス サーバーの検出、評価と移行の調整を行う場合はこちらのバージョンを使用します。 このバージョンの新機能については[こちらを参照してください](whats-new.md)。
- **以前のバージョン**: 以前のバージョンの Azure Migrate (クラシック Azure Migrate) では、オンプレミスの VMware VM の評価のみがサポートされています。 従来の Azure Migrate は 2024 年 2 月に廃止されます。 2024 年 2 月以降、クラシック バージョンの Azure Migrate はサポートされなくなり、クラシック プロジェクトのインベントリ メタデータは削除されます。 旧バージョンのプロジェクトやコンポーネントを新バージョンにアップグレードすることはできません。 [新しいプロジェクトを作成](create-manage-projects.md)して、それに[評価と移行ツールを追加する](./create-manage-projects.md)必要があります。 チュートリアルを利用して、使用可能な評価と移行ツールの使用方法を理解してください。 Log Analytics ワークスペースがクラシック プロジェクトにアタッチされている場合は、クラシック プロジェクトを削除した後で、そのワークスペースを現在のバージョンのプロジェクトにアタッチできます。

    Azure portal で既存のプロジェクトにアクセスするには、「**Azure Migrate**」を検索して選択します。 **Azure Migrate** ダッシュボードに、通知と、過去のプロジェクトにアクセスするためのリンクが表示されます。

## <a name="next-steps"></a>次のステップ

- チュートリアルを実行し、[VMware VM](./tutorial-discover-vmware.md)、[Hyper-V VM](./tutorial-discover-hyper-v.md)、または[物理サーバー](./tutorial-discover-physical.md)を評価します。
- Azure Migrate について[よく寄せられる質問を確認](resources-faq.md)します。
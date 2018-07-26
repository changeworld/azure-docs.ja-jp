---
title: Azure コンテナーと Azure SQL Database で Contoso アプリを再構築する | Microsoft Docs
description: Contoso が Azure Windows コンテナーと Azure SQL Database でアプリを再構築する方法について説明します。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: raynew
ms.openlocfilehash: 7146865270accb73981b09be6409180c4ef1440f
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003199"
---
# <a name="contoso-migration-rearchitect-an-on-premises-app-to-an-azure-container-and-azure-sql-database"></a>Contoso の移行: Azure コンテナーと Azure SQL Database でオンプレミス アプリを再構築する

この記事では、Contoso が SmartHotel アプリを Azure に移行し、再構築する方法について説明します。 アプリ フロントエンド VM は Azure Windows コンテナーに、アプリ データベースは Azure SQL Database に移行します。

このドキュメントは、架空の会社 Contoso がオンプレミス リソースを Microsoft Azure クラウドに移行する方法を説明するシリーズ記事の 1 つです。 このシリーズには背景情報やシナリオが含まれ、移行インフラストラクチャのセットアップ、移行のためのオンプレミス リソースへのアクセス、およびさまざまな種類の移行の実行が説明されています。 シナリオが複雑になってきているため、さらに記事が追加される予定です。

**記事** | **詳細** | **状態**
--- | --- | ---
[記事 1: 概要](contoso-migration-overview.md) | Contoso の移行戦略、記事シリーズ、および使用するサンプル アプリの概要を示します。 | 使用可能
[記事 2: Azure インフラストラクチャのデプロイ](contoso-migration-infrastructure.md) | Contoso が移行に備えてオンプレミスおよび Azure インフラストラクチャをどのように準備するかを説明します。 移行に関するすべてのアーティクルでは同じインフラストラクチャが使用されます。 | 使用可能
[記事 3: オンプレミス リソースの評価](contoso-migration-assessment.md)  | VMware で実行されているオンプレミスの 2 階層 SmartHotel アプリの評価を Contoso が実行する方法を説明します。 Contoso は、アプリの VM は [Azure Migrate](migrate-overview.md) サービスを使用して、アプリの SQL Server データベースは [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) を使用して評価します。 | 使用可能
[記事 4: Azure VM および SQL Managed Instance へのアプリのリホスト](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso が、SmartHotel アプリの Azure へのリフトアンドシフト移行を実行する方法を説明します。 Contoso は、[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) を使用してアプリのフロントエンド VM を移行し、[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) を使用してアプリのデータベースを SQL Managed Instance に移行します。 | 使用可能
[記事 5: アプリの Azure VM へのリホスト](contoso-migration-rehost-vm.md) | Contoso が Site Recovery のみを使用して SmartHotel アプリ VM を移行する方法を説明します。 | 使用可能
[記事 6: Azure VM と SQL Server Always On 可用性グループへのアプリのリホスト](contoso-migration-rehost-vm-sql-ag.md) | Contoso が SmartHotel アプリを移行する方法を示します。 Contoso は、Site Recovery を使用してアプリの VM を移行し、Database Migration Service を使用してアプリのデータベースを AlwaysOn 可用性グループで保護されている SQL Server クラスターに移行します。 | 使用可能
[記事 7: Linux アプリの Azure VM へのリホスト](contoso-migration-rehost-linux-vm.md) | Contoso が Site Recovery を使用して Azure VM への Linux osTicket アプリのリフトアンドシフト移行を実行する方法を説明します。 | 使用可能
[記事 8: Linux アプリの Azure VM および Azure MySQL Server へのリホスト](contoso-migration-rehost-linux-vm-mysql.md) | Contoso が Site Recovery を使用して Linux osTicket アプリを Azure VM に移行する方法、および MySQL Workbench を使用してアプリのデータベースを Azure MySQL Server インスタンスに移行する方法を説明します。 | 使用可能
[記事 9: Azure Web App と Azure SQL Database でのアプリのリファクター](contoso-migration-refactor-web-app-sql.md) | Contoso が SmartHotel アプリを Azure Web App に移行して、アプリ データベースを Azure SQL Server インスタンスに移行する方法を示します。 | 使用可能
[記事 10: Azure Web Apps と Azure MySQL への Linux アプリのリファクター](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso が Linux osTicket アプリを複数のサイトの (継続的デリバリーのために GitHub と統合された) Azure Web Apps に移行する方法を示します。 Contoso は、アプリ データベースを Azure MySQL インスタンスに移行します。 | 使用可能
記事 11: Azure コンテナーと Azure SQL Database でのアプリの再構築 | Contoso が SmartHotel アプリを Azure に移行して再構築する方法を示します。 Contoso は、アプリの Web 階層を Windows コンテナーとして再構築し、Azure SQL Database 内のアプリ データベースを再構築します。 | この記事の内容は次のとおりです。
[記事 12: Azure コンテナーと Azure SQL Database でのアプリの再構築](contoso-migration-rearchitect-container-sql.md) | Contoso が SmartHotel アプリを Azure に移行して再構築する方法を示します。 Contoso は、アプリの Web 階層を Windows コンテナーとして再構築し、Azure SQL Database 内のアプリ データベースを再構築します。 | 使用可能
[記事 13: Azure でのアプリのリビルド](contoso-migration-rebuild.md) | Contoso が Azure のさまざまな機能とサービス (App Services、Azure Kubernetes、Azure Functions、Cognitive サービス、Cosmos DB など) を使用して SmartHotel アプリをリビルドする方法を示します。 | 使用可能

この記事では、Contoso は 2 階層の Windows を移行します。 NET SmartHotel アプリを Azure に移行します。 このアプリを使用したい場合は、オープン ソースとして提供されていますので、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードしてください。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応**: Contoso は成長を続けています。そのため、オンプレミス システムおよびインフラストラクチャに対する負荷が高まっています。
- **効率化**: Contoso は不要な手順を取り除き、開発者とユーザーのプロセスを効率化する必要があります。  ビジネス部門は IT に対して、時間やコストを無駄にせず、迅速に作業を行ってもらう必要があります。たとえば、顧客の要求に対して、素早く対応する必要があります。
- **敏捷性の強化**: Contoso IT は、ビジネス部門の要求に対して、対応力を向上させる必要があります。 また、グローバル経済で成功を収めるために、市場の変化に対して、より迅速な対応ができる必要があります。  ビジネスの妨げになったり、ビジネスの機会を壊すようなことがあってはなりません。
- **拡張性**: ビジネスが順調に成長していく中で、Contoso IT は、同じペースで拡張可能なシステムを提供する必要があります。
- **コスト**: Contoso はライセンス コストを最小限に抑えたいと考えています。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を設定しました。 これらの目標を使用して、最良の移行方法を決定しました。

**目標** | **詳細**
--- | --- 
**アプリの要件** | Azure のアプリの重要度は、現在と同様に今後も変わりません。<br/><br/> 現在の VMWare と同じくらいのパフォーマンス機能が必要です。<br/><br/> アプリが現在実行されている Windows Server 2008 R2 のサポートを終了し、アプリに投資したいと考えています。<br/><br/> SQL Server 2008 R2 から最新の PaaS Database プラットフォームに移行したいと考えています。これにより、管理の必要性を最小限に抑えられます。<br/><br/> Contoso は、可能であれば、SQL Server のライセンスとソフトウェア アシュアランスへのこれまでの投資を利用したいと考えています。<br/><br/> アプリの Web 階層をスケールアップできるようにしたいと考えています。
**制限事項** | このアプリは、同じ VM 上で実行されている ASP.NET アプリと WCF サービスで構成されています。 Azure App Service を使用して 2 つの Web アプリに分割したいと考えています。 
**Azure の要件** | アプリを Azure に移行し、そのアプリをコンテナーで実行してアプリの寿命を延ばしたいと考えています。 Azure でのアプリの実装を完全に最初から始めたくはありません。 

## <a name="solution-design"></a>ソリューション設計

Contoso は目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。

### <a name="current-app"></a>現在のアプリ

- SmartHotel オンプレミス アプリは 2 つの VM (WEBVM と SQLVM) に階層化されています。
- VM は、VMware ESXi ホスト **contosohost1.contoso.com** (バージョン 6.5) 上にあります。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (**vcenter.contoso.com**) によって管理されています。
- Contoso にはオンプレミスのデータセンター (contoso-datacenter) があり、そこにオンプレミスのドメイン コントローラー (**contosodc1**) が含まれています。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。


### <a name="proposed-architecture"></a>提案されたアーキテクチャ

- このアプリのデータベース層について、Contoso は[こちらの記事](https://docs.microsoft.com/azure/sql-database/sql-database-features)を使用して Azure SQL Database と SQL Server を比較しました。 そして、いくつかの理由で Azure SQL Database を使用することにしました。
    - Azure SQL Database は、リレーショナル データベース マネージド サービスです。 複数のサービス レベルで予測可能なパフォーマンスを実現します。管理作業はほぼゼロです。 利点としては、ダウンタイムのない動的スケーラビリティ、組み込みのインテリジェントな最適化、グローバルなスケーラビリティと可用性などがあります。
    - 軽量な Data Migration Assistant (DMA) を利用して、オンプレミス データベースを評価し、Azure SQL に移行することができます。
    - ソフトウェア アシュアランスに基づき、SQL Database では SQL Server 用の Azure ハイブリッド特典を利用して、既存のライセンスを割引料金のライセンスに交換することができます。 この方法なら最大 30% の節約が可能です。
    - SQL Database には、常に暗号化された動的データ マスキング、行レベルのセキュリティ/脅威検出など、さまざまなセキュリティ機能があります。
- アプリの Web 層については、Visual Studio を使用して Windows コンテナーに変換することを決定しました。
    - Azure Service Fabric を使用してアプリをデプロイし、Azure Container Registry (ACR) から Windows コンテナー イメージをプルします。
    - センチメント分析を含むようにアプリを拡張するプロトタイプは、Cosmos DB に接続された Service Fabric の別のサービスとして実装されます。  ツイートから情報が読み取られ、アプリに表示されます。

    ![シナリオのアーキテクチャ](./media/contoso-migration-rearchitect-container-sql/architecture.png) 

  
### <a name="solution-review"></a>ソリューションのレビュー
Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

**考慮事項** | **詳細**
--- | ---
**長所** | SmartHotel アプリ コードは、Azure Service Fabric に移行するために変更する必要があります。 ただし、変更には Service Fabric SDK ツールを使用して、作業を最小限に抑えられます。<br/><br/> Service Fabric に移行することで、マイクロサービスを開発し、元のコード ベースに悪影響を及ぼすことなく、徐々に簡単にマイクロサービスをアプリケーションに追加することができます。<br/><br/> Windows コンテナーには、一般的にコンテナーと同じ利点があります。 機敏性、移植性、制御性が向上します。<br/><br/> SQL Server と Windows Server の両方に Azure ハイブリッド特典を使用して、ソフトウェア アシュアランスへの投資を利用できます。<br/><br/> 移行後は、Windows Server 2008 R2 をサポートする必要がなくなります。 [詳細情報](https://support.microsoft.com/lifecycle)。<br/><br/> 複数のインスタンスがあるアプリの Web 層を構成することができたので、単一障害点ではなくなりました。<br/><br/> 古い SQL Server 2008 R2 に依存しなくなります。<br/><br/> SQL Database は、Contoso の技術面の要件をサポートしています。 Database Migration Assistant を使用してオンプレミス データベースを評価し、互換性があることがわかりました。<br/><br/> SQL Database には、Contoso が設定する必要がないフォールト トレランス機能が組み込まれています。 そのため、データ層がフェールオーバーの単一ポイントではなくなります。
**短所** | コンテナーは他の移行オプションより複雑です。 Contoso にとって、コンテナーの学習曲線が問題になる可能性があります。  この曲線にもかかわらず、Contoso は多くの価値を提供する新しいレベルの複雑さを導入します。<br/><br/> Azure、コンテナー、アプリのマイクロサービスを理解し、サポートできるように、Contoso の運用チームを立ち上げる必要があります。<br/><br/> Database Migration Service ではなく Data Migration Assistant を使用してデータベースを移行する場合、Contoso は大規模なデータベースを移行できるインフラストラクチャを持つ予定がありません。



### <a name="migration-process"></a>移行プロセス

1. Contoso は Windows 用 Azure Service Fabric クラスターをプロビジョニングしています。
2. Azure SQL インスタンスをプロビジョニングし、SmartHotel データベースをそこに移行します。
3. Service Fabric SDK ツールを使用して、Web 層の VM を Docker コンテナーに変換します。
4. Service Fabric クラスターと ACR を接続し、Azure Service Fabric を使用してアプリをデプロイします。

    ![移行プロセス](./media/contoso-migration-rearchitect-container-sql/migration-process.png) 

### <a name="azure-services"></a>Azure サービス

**サービス** | **説明** | **コスト**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Contoso では DMA を使用して、Azure でのデータベースの機能に影響を与える可能性のある互換性の問題を評価し、検出します。 DMA は SQL のソースとターゲット間で機能パリティを評価し、パフォーマンスと信頼性の向上箇所を推奨します。 | これは無料でダウンロードできるツールです。
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | インテリジェントなフル マネージド リレーショナル クラウド データベース サービス。 | 機能、スループット、サイズに基づくコスト。 [詳細情報](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | あらゆる種類のコンテナー デプロイのイメージを保存します。 | 機能、ストレージ、使用期間に基づくコスト。 [詳細情報](https://azure.microsoft.com/pricing/details/container-registry/)。
[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) | 常時接続可能でスケーラブルな分散アプリを構築し、運用します | コンピューティング ノードのサイズ、場所、期間に基づくコスト。 [詳細情報](https://azure.microsoft.com/pricing/details/service-fabric/)。

## <a name="prerequisites"></a>前提条件

このシナリオを実行するために作業担当者 (と Contoso) が必要とするものを以下に示します。

**要件** | **詳細**
--- | ---
**Azure サブスクリプション** | このシリーズの最初の記事で評価を行ったときに、サブスクリプションは既に作成しているはずです。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成してください。<br/><br/> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。<br/><br/> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。
**Azure インフラストラクチャ** | [Contoso で Azure インフラストラクチャを設定する方法](contoso-migration-infrastructure.md)を確認してください。
**開発者の前提条件** | Contoso は、開発者用ワークステーションに次のツールをインストールする必要があります。<br/><br/> - [Visual Studio 2017 Community エディション: バージョン 15.5](https://www.visualstudio.com/)<br/><br/> - .NET ワークロードが有効。<br/><br/> - [Git](https://git-scm.com/)<br/><br/> - [Service Fabric SDK v 3.0 以降](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)<br/><br/> - [Windows コンテナーを使用するように設定された Docker CE (Windows 10) または Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/)。



## <a name="scenario-steps"></a>シナリオのステップ

Contoso が移行を実行する方法を次に示します。

> [!div class="checklist"]
> * **手順 1: Azure に SQL Database インスタンスをプロビジョニングする**: Contoso は Azure に SQL インスタンスをプロビジョニングします。 フロントエンド Web VM が Azure コンテナーに移行された後、アプリ Web フロントエンドを含むコンテナー インスタンスは、このデータベースを示すようになります。
> * **手順 2: Azure Service Fabric をプロビジョニングする**: Service Fabric クラスターをプロビジョニングします。
> * **手順 4: DMA を使用してデータベースを移行する**: Data Migration Assistant を使用してアプリ データベースを移行します。
> * **手順 5: アプリをコンテナーに変換する**: Visual Studio と SDK Tools を使用してアプリをコンテナーに変換します。
> * **手順 6: アプリを発行する**: アプリを ACR と Service Fabric クラスターに発行します。
> * **手順 7: アプリを拡張する**: アプリが発行された後、Azure の機能を利用するように拡張し、それを Azure に再発行します。



## <a name="step-1-provision-an-azure-sql-database"></a>手順 1: Azure SQL Database をプロビジョニングする

1. Azure で SQL Database を作成することを選択します。 

    ![SQL をプロビジョニングする](./media/contoso-migration-rearchitect-container-sql/provision-sql1.png)

2. オンプレミス VM 上で実行されているデータベースと一致するようにデータベース名を指定します (**SmartHotel.Registration**)。 ContosoRG リソース グループにデータベースを配置します。 これは、Azure の運用リソースに使用するリソース グループです。

    ![SQL をプロビジョニングする](./media/contoso-migration-rearchitect-container-sql/provision-sql2.png)

3. プライマリ リージョンに新しい SQL Server インスタンス (**sql-smarthotel-eus2**) を設定します。

    ![SQL をプロビジョニングする](./media/contoso-migration-rearchitect-container-sql/provision-sql3.png)

4. サーバーとデータベースのニーズに合わせて価格レベルを設定します。 既に SQL Server のライセンスを持っているため、Azure ハイブリッド特典で費用を節約することにします。
5. サイズ設定については、仮想コアベースの購入を使用し、予想される要件の制限を設定します。

    ![SQL をプロビジョニングする](./media/contoso-migration-rearchitect-container-sql/provision-sql4.png)

6. 次に、データベース インスタンスを作成します。

    ![SQL をプロビジョニングする](./media/contoso-migration-rearchitect-container-sql/provision-sql5.png)

7. インスタンスの作成後、データベースを開き、移行に Data Migration Assistant を使用するときに必要な詳細情報を書き留めます。

    ![SQL をプロビジョニングする](./media/contoso-migration-rearchitect-container-sql/provision-sql6.png)


**さらにサポートが必要な場合**

- SQL Database のプロビジョニングの[手順を参照](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)します。
- 仮想コア リソースの制限の詳細については[こちら](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)を参照してください。



## <a name="step-2-create-an-acr-and-provision-an-azure-container"></a>手順 2: ACR を作成し、Azure コンテナーをプロビジョニングする

Azure コンテナーは、Web VM からエクスポートされたファイルを使用して作成されます。 コンテナーは Azure Container Registry (ACR) に配置されています。


1. Contoso は Azure portal で Container Registry を作成します。

     ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry1.png)

2. レジストリの名前 (**contosoacreus2**) を指定し、プライマリ リージョンのインフラストラクチャ リソースに使用するリソース グループ内に配置します。 管理者ユーザーにアクセスを許可し、Premium SKU として設定して、geo レプリケーションを利用できるようにします。

    ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry2.png)  


## <a name="step-3-provision-azure-service-fabric"></a>手順 3: Azure Service Fabric をプロビジョニングする

SmartHotel コンテナーは、Azure Service Fabric クラスターで実行されます。 Contoso は、次のように Service Fabric クラスターを作成します。

1. Azure Marketplace から Service Fabric リソースを作成する

     ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric1.png)

2. **Basic** では、クラスターの一意の DS 名と、オンプレミス VM にアクセスするための資格情報を指定します。 プライマリ リージョンの米国東部 2 の運用リソース グループ (**ContosoRG**) にリソースを配置します。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric2.png) 

3. **[ノード タイプの構成]** に、ノード タイプ名、持続性の設定、VM サイズ、およびアプリケーションのエンドポイントを入力します。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric3.png) 


4. **[キー コンテナーの作成]** で、証明書を格納する新しいキー コンテナーをインフラストラクチャ リソース グループに作成します。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric4.png) 


5. **[アクセス ポリシー]** で、仮想マシンにアクセスしてキー コンテナーを展開できるようにします。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric5.png) 

6. 証明書の名前を指定します。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric6.png) 

7. 概要ページで、証明書のダウンロードに使用されるリンクをコピーします。 Service Fabric クラスターに接続するには、このリンクが必要です。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric7.png) 

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric8.png) 

8. 検証に合格したら、クラスターをプロビジョニングします。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric9.png) 

9. [証明書のインポート] ウィザードで、ダウンロードした証明書を開発用マシンにインポートします。 証明書は、クラスターに対する認証に使用されます。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric10.png) 

10. クラスターがプロビジョニングされたら、Service Fabric Cluster Explorer に接続します。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric11.png) 

11. 正しい証明書を選択する必要があります。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric12.png) 

12. Service Fabric Explorer が読み込まれ、Contoso 管理者がクラスターを管理できるようになります。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric13.png) 


## <a name="step-3-migrate-the-database-with-dma"></a>手順 3: DMA を使用してデータベースを移行する

Contoso は DMA を使用して SmartHotel データベースを移行します。

### <a name="install-dma"></a>DMA をインストールする

1. [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=53595) からオンプレミスの SQL Server VM (**SQLVM**) にツールをダウンロードします。
2. VM 上でセットアップ (DownloadMigrationAssistant.msi) を実行します。
3. **[完了]** ページで、ウィザードを終了する前に **[Microsoft Data Migration Assistant の起動]** を選択します。

### <a name="configure-the-firewall"></a>ファイアウォールの構成

Azure SQL Database に接続するには、ファイアウォール ルールが必要です。

1. データベースの **[ファイアウォールと仮想ネットワーク]** プロパティで、Azure サービスへのアクセスを許可し、オンプレミス SQL Server VM のクライアント IP アドレスのルールを追加します。
2. サーバーレベルのファイアウォール ルールが作成されます。

    ![ファイアウォール](./media/contoso-migration-rearchitect-container-sql/sql-firewall.png)

さらにサポートが必要な場合

Azure SQL Database のファイアウォール ルールの作成と管理の詳細については、[こちら](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#creating-and-managing-firewall-rules)を参照してください。

### <a name="migrate"></a>移行

1. DMA で新しいプロジェクトを作成し (**SmartHotelDB**)、**[移行]** を選択します 
2. ソース サーバーの種類として **SQL Server**、ターゲットとして **Azure SQL Database** を選択します。 

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-1.png)

3. 移行の詳細では、ソース サーバーとして **SQLVM** を追加し、**SmartHotel.Registration** データベースを追加します。 

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-2.png)

4. 認証に関連しているようなエラーが表示されます。 ただし、調査の結果、問題はデータベース名のピリオド (.) であることがわかりました。 この問題を解決するため、回避策として、**SmartHotel-Registration** という名前を使用して新しい SQL Database をプロビジョニングすることに決めました。 再度 DMA を実行すると、**SmartHotel-Registration** を選択してウィザードを続行できます。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-3.png)

5. **[オブジェクトの選択]** で、データベース テーブルを選択し、SQL スクリプトを生成します。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-4.png)

6. DMS でスクリプトが作成されたら、**[スキーマの配置]** をクリックします。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-5.png)

7. DMA で配置が成功したことを確認します。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-6.png)

8. 次は移行の開始です。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-7.png)

9. 移行が完了すると、Contoso はデータベースが Azure SQL インスタンス上で実行されていることを確認できます。

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-8.png)

10. Azure portal で余計な SQL データベース **SmartHotel.Registration** を削除します。

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-9.png)



## <a name="step-4-convert-the-app-to-a-container"></a>手順 4: アプリをコンテナーに変換する

オンプレミス アプリは従来の 3 階層のアプリです。

- これには WebForms と、SQL Server に接続する WCF サービスが含まれています。
- Entity Framework を使用して SQL Database のデータと統合し、WCF サービスを介して公開します。
- WebForms アプリケーションは、WCF サービスと対話します。

Contoso は、以下のように Visual Studio と SDK Tools を使用してアプリをコンテナーに変換します。

1. ローカルでリポジトリを開発者用マシンに複製します。

    **Git クローン https://github.com/Microsoft/SmartHotel360-internal-booking-apps.git**

    ![コンテナー](./media/contoso-migration-rearchitect-container-sql/container1.png)

2. Visual Studio を使用して、ローカル リポジトリの **SmartHotel360-internal-booking-apps\src\Registration** ディレクトリにあるソリューション ファイル (SmartHotel.Registration.sln) を開きます。  2 つのアプリが表示されます。 Web フロントエンドの SmartHotel.Registration.Web と WCF サービス アプリの SmartHotel.Registration.WCF です。

    ![コンテナー](./media/contoso-migration-rearchitect-container-sql/container2.png)


3. Web アプリを右クリックし、**[追加]** > **[Container Orchestrator Support]\(コンテナー オーケストレーター サポート\)** の順に選択します。
4. **[Add Container Orchestra Support]\(コンテナー オーケストレーター サポートの追加\)** で **[Service Fabric]** を選択します。

    ![コンテナー](./media/contoso-migration-rearchitect-container-sql/container3.png)

5. Contoso は SmartHotel.Registration.WCF アプリについて同じプロセスを繰り返します。
6. 次に、Contoso はソリューションがどのように変わったかを確認します。

    - 新しいアプリは **SmartHotel.RegistrationApplication/** です
    - このアプリには、**SmartHotel.Registration.WCF** と **SmartHotel.Registration.Web** という 2 つのサービスが含まれています。

    ![コンテナー](./media/contoso-migration-rearchitect-container-sql/container4.png)

7. Visual Studio によって Docker ファイルが作成され、必要なイメージが開発者用マシンのローカルにプルダウンされました。

    ![コンテナー](./media/contoso-migration-rearchitect-container-sql/container5.png)

8. Visual Studio によってマニフェスト ファイル (**ServiceManifest.xml**) が作成され、開かれます。 このファイルは、コンテナーが Azure にデプロイされるときにコンテナーを構成する方法を Service Fabric に指示します。

    ![コンテナー](./media/contoso-migration-rearchitect-container-sql/container6.png)

9. もう 1 つのマニフェスト ファイル (**ApplicationManifest.xml) には、コンテナーの構成アプリケーションが含まれています。

    ![コンテナー](./media/contoso-migration-rearchitect-container-sql/container7.png)

## <a name="step-5-publish-the-app"></a>手順 5: アプリを発行する


最後に、Contoso はアプリを ACR と Service Fabric クラスターに発行できます。

> [!NOTE]
> Service Fabric クラスターに関連するいくつかの変更が SmartHotel アプリで行われました。 [GitHub](https://github.com/Microsoft/SmartHotel360-internal-booking-apps) から元のアプリ コードと最新化されたアプリ コードの両方をダウンロードできます。 変更されたファイルは **AppliationModern/ApplicationParameters/Cloud.xml** です。


1. Visual Studio で、アプリを Azure SQL Database と接続するように接続文字列を更新します。 接続文字列は Azure portal のデータベースで確認できます。

    ![[発行]](./media/contoso-migration-rearchitect-container-sql/publish1.png)

2. Contoso は、Visual Studio を使用して Service Fabric にアプリを発行します。 Service Fabric アプリケーションを右クリックし、**[発行]** をクリックします。

    ![[発行]](./media/contoso-migration-rearchitect-container-sql/publish2.png)

3. サブスクリプション、接続エンドポイント、および ACR を選択します。 **[発行]** をクリックします。

    ![[発行]](./media/contoso-migration-rearchitect-container-sql/publish3.png)

4. デプロイが完了すると、SmartHotel で Service Fabric が実行されます。

    ![[発行]](./media/contoso-migration-rearchitect-container-sql/publish4.png)

5. アプリに接続するために、Contoso は、Service Fabric ノードの前面にある Azure Load Balancer のパブリック IP アドレスにトラフィックを誘導します。

    ![[発行]](./media/contoso-migration-rearchitect-container-sql/publish5.png)

## <a name="step-6-extend-the-app-and-republish"></a>手順 6: アプリを拡張して再発行する

SmartHotel アプリとデータベースが Azure で実行された後、Contoso はアプリを拡張したいと考えています。

- Contoso の開発者は、Service Fabric クラスター上で動作する新しい .NET Core アプリケーションのプロトタイプを作成しています。
- アプリは、CosmosDB からセンチメント データをプルするために使用されます。
- このデータは、Serverless Azure Function と Cognitive Services Text Analysis API を使用して処理されるツイートの形式になります。

### <a name="provision-azure-cosmos-db"></a>Azure Cosmos DB をプロビジョニングする

最初の手順として、Contoso は Azure Cosmos データベースをプロビジョニングします。

1. Azure Marketplace から Azure Cosmos DB リソースを作成します。

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend1.png)

2. データベース名 (**contososmarthotel**) を指定し、SQL API を選択し、プライマリ リージョンの米国東部 2 の運用リソース グループに配置します。

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend2.png)

3. **[作業の開始]** で **[データ エクスプローラー]** を選択し、新しいコレクションを追加します。
4. **[コレクションの追加]** で ID を指定し、ストレージの容量とスループットを設定します。

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend3.png)

5. ポータルで新しいデータベースを開き、**[コレクション]** > **[ドキュメント]** の順に選択し、**[新しいドキュメント]** をクリックします。
6. 次の JSON コードをドキュメント ウィンドウに貼り付けます。 これは単一のツイート形式のサンプル データです。

    ```
    {
            "id": "2ed5e734-8034-bf3a-ac85-705b7713d911",
            "tweetId": 927750234331580911,
            "tweetUrl": "https://twitter.com/status/927750237331580911",
            "userName": "CoreySandersWA",
            "userAlias": "@CoreySandersWA",
            "userPictureUrl": "",
            "text": "This is a tweet about #SmartHotel",
            "language": "en",
            "sentiment": 0.5,
            "retweet_count": 1,
            "followers": 500, 
            "hashtags": [
                ""
            ]
    }
    ```

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend4.png)

7. Cosmos DB エンドポイントと認証キーを確認します。 これらはアプリでコレクションに接続するために使用されます。 データベースで、**[キー]** をクリックし、URI と主キーをメモ帳にコピーします。

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend5.png)

### <a name="update-the-sentiment-app"></a>センチメント アプリを更新する

Cosmos DB がプロビジョニングされたら、Contoso はそれに接続するようにアプリを構成できます。

1. Visual Studio のソリューション エクスプローラーで ApplicationModern\ApplicationParameters\cloud.xml ファイルを開きます。

    ![センチメント アプリ](./media/contoso-migration-rearchitect-container-sql/sentiment1.png)

2. 次の 2 つのパラメーターを入力します。

   ```
   <Parameter Name="SentimentIntegration.CosmosDBEndpoint" Value="[URI]" />
   ```
   
   ```
   <Parameter Name="SentimentIntegration.CosmosDBAuthKey" Value="[Key]" />
   ```

    ![センチメント アプリ](./media/contoso-migration-rearchitect-container-sql/sentiment2.png)

### <a name="republish-the-app"></a>アプリを再発行する

アプリを拡張した後、Contoso はそれを Azure に再発行します。

1. ポータルで Service Fabric アプリを右クリックし、**[発行]** をクリックします。

    ![再発行](./media/contoso-migration-rearchitect-container-sql/republish1.png)

2. サブスクリプション、接続エンドポイント、および ACR を選択します。 **[発行]** をクリックします。

    ![再発行](./media/contoso-migration-rearchitect-container-sql/republish2.png)

4. デプロイが完了すると、SmartHotel で Service Fabric が実行されます。 Servie Fabric 管理コンソールに 3 つのサービスが表示されるようになりました。

    ![再発行](./media/contoso-migration-rearchitect-container-sql/republish3.png)

5. Contoso はサービスをクリックして、SentimentIntegration アプリが起動していることを確認できます

    ![再発行](./media/contoso-migration-rearchitect-container-sql/republish4.png)

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行後、Contoso は、以下のクリーンアップ手順を完了する必要があります。  

- vCenter のインベントリからオンプレミスの VM を削除します。
- ローカルのバックアップ ジョブからから VM を削除します。
- SmartHotel アプリの新しい場所を示すように社内ドキュメントを更新します。 Azure SQL Database でデータベースは実行中と表示され、Service Fabric でフロント エンドは実行中と表示されます。
- 使用停止されている VM と対話しているリソースがないか確認します。また、関連する設定やドキュメントがあれば、更新して新しい構成を反映します。


## <a name="review-the-deployment"></a>デプロイ環境を検討する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティ保護する必要があります。

### <a name="security"></a>セキュリティ

- Contoso は新しい **SmartHotel-Registration** データベースがセキュリティで保護されていることを確認する必要があります。 [詳細情報](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)。
- 特に、証明書で SSL を使用するようにコンテナーを更新する必要があります。
- KeyVault を使用して、Service Fabric アプリのシークレットを保護することを検討する必要があります。 [詳細情報](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management)。

### <a name="backups"></a>バックアップ

- Contoso は、Azure SQL Database のバックアップ要件を確認する必要があります。 [詳細情報](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。
- データベースのリージョン内フェールオーバーを提供するようにフェールオーバー グループを実装することを検討する必要があります。 [詳細情報](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)。
- ACR Premium SKU の geo レプリケーションを利用できます。 [詳細情報](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)。
- Contoso は、Web App for Containers がリリースされた時点で、メインの米国東部 2 リージョンと米国中部リージョンに Web アプリをデプロイすることを検討する必要があります。 リージョンの障害が発生した場合に確実にフェールオーバーするように Traffic Manager を構成できます。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- すべてのリソースをデプロイした後、Contoso は[インフラストラクチャ計画](contoso-migration-infrastructure.md#set-up-tagging)に基づいて Azure タグを割り当てる必要があります。
- すべてのライセンスは、Contoso が使用している PaaS サービスのコストに組み込まれています。 これは EA から差し引かれます。
1. Contoso は、Microsoft の子会社である Cloudyn からライセンスが供与される Azure Cost Management を有効にします。 これは、Azure やその他のクラウド リソースの利用や管理に役立つ、マルチクラウド対応のコスト管理ソリューションです。  Azure Cost Management の詳細については、[こちら](https://docs.microsoft.com/azure/cost-management/overview)を参照してください。 

## <a name="conclusion"></a>まとめ

この記事で、Contoso は、アプリのフロントエンド VM を Service Fabric に移行することで、Azure の SmartHotel アプリをリファクターしました。 アプリ データベースを Azure SQL Database に移行しました。






---
title: Contoso アプリを Azure VM および SQL Server AlwaysOn 可用性グループに移行してリファクターする | Microsoft Docs
description: Contoso がオンプレミス アプリを Azure Container Web アプリと Azure SQL Server データベースに移行して、オンプレミス アプリをリホストする方法について説明します。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: f4a348815ef058cb795ed12e8f118b494650a555
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005192"
---
# <a name="contoso-migration-refactor-an-on-premises-app-to-an-azure-web-app-and-azure-sql-database"></a>Contoso の移行: Azure Web App と Azure SQL Database にオンプレミス アプリをリファクターする

この記事では、Contoso が SmartHotel アプリを Azure でリファクターする方法について説明します。 アプリ フロントエンド VM は Azure Web App に、アプリ データベースは Azure SQL Database に移行します。

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
記事 9: Azure Web App と Azure SQL Database でのアプリのリファクター | Contoso が SmartHotel アプリを Azure Web App に移行して、アプリ データベースを Azure SQL Server インスタンスに移行する方法を示します。 | この記事の内容は次のとおりです。
[記事 10: Azure Web Apps と Azure MySQL への Linux アプリのリファクター](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso が Linux osTicket アプリを複数のサイトの (継続的デリバリーのために GitHub と統合された) Azure Web Apps に移行する方法を示します。 Contoso は、アプリ データベースを Azure MySQL インスタンスに移行します。 | 使用可能
[記事 11: VSTS での TFS のリファクター](contoso-migration-tfs-vsts.md) | Contoso がオンプレミスの Team Foundation Server (TFS) の展開を Azure の Visual Studio Team Services (VSTS) に移行する方法を示します。 | 使用可能
[記事 12: Azure コンテナーと Azure SQL Database でのアプリの再構築](contoso-migration-rearchitect-container-sql.md) | Contoso が SmartHotel アプリを Azure に移行して再構築する方法を示します。 Contoso は、アプリの Web 階層を Windows コンテナーとして再構築し、Azure SQL Database にアプリ データベースを再構築します。 | 使用可能
[記事 13: Azure でのアプリのリビルド](contoso-migration-rebuild.md) | Contoso が Azure のさまざまな機能とサービス (App Services、Azure Kubernetes、Azure Functions、Cognitive サービス、Cosmos DB など) を使用して SmartHotel アプリをリビルドする方法を示します。 | 使用可能


この記事では、Contoso は 2 階層の Windows を移行します。 NET SmartHotel アプリを Azure に移行します。 このアプリを使用したい場合は、オープン ソースとして提供されていますので、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードしてください。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応**: Contoso は成長を続けており、オンプレミス システムとインフラストラクチャに対する負荷が高まっています。
- **効率化**: Contoso は不要な手順を取り除き、開発者とユーザーのプロセスを効率化する必要があります。  ビジネス部門は IT に対して、時間やコストを無駄にせず、迅速に作業を行ってもらう必要があります。たとえば、顧客の要求に対して、素早く対応する必要があります。
- **敏捷性の強化**: Contoso IT は、ビジネス部門の要求に対して、対応力を向上させる必要があります。 また、グローバル経済で成功を収めるために、市場の変化に対して、より迅速な対応ができる必要があります。  ビジネスの妨げになったり、ビジネスの機会を壊すようなことがあってはなりません。
- **拡張性**: ビジネスが順調に成長していく中で、Contoso IT は、同じペースで拡張可能なシステムを提供する必要があります。
- **コスト**: Contoso はライセンス コストを最小限に抑えたいと考えています。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を設定しました。 これらの目標を使用して、最良の移行方法を決定しました。

**要件** | **詳細**
--- | --- 
**アプリ** | Azure のアプリの重要度は、現在と同様に今後も変わりません。<br/><br/> 現在の VMWare と同じくらいのパフォーマンス機能が必要です。<br/><br/> アプリには投資したくありません。 現在のところは、クラウドに問題なく移行したいだけです。<br/><br/> アプリが現在実行されている Windows Server 2008 R2 のサポートを終了したいと考えています。<br/><br/> SQL Server 2008 R2 から最新の PaaS Database プラットフォームに移行したいと考えています。これにより、管理の必要性を最小限に抑えられます。<br/><br/> Contoso は、可能であれば、SQL Server のライセンスとソフトウェア アシュアランスへのこれまでの投資を利用したいと考えています。<br/><br/> さらに、Contoso は Web 層の単一障害点を軽減したいと考えています。
**制限事項** | このアプリは、同じ VM 上で実行されている ASP.NET アプリと WCF サービスで構成されています。 Azure App Service を使用して 2 つの Web アプリに分割したいと考えています。 
**Azure** | Azure にアプリを移行したいと考えていますが、VM 上では実行したくありません。 Azure PaaS サービスを Web 層とデータ層の両方に利用したいと考えています。 

## <a name="solution-design"></a>ソリューション設計

Contoso は目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。

### <a name="current-app"></a>現在のアプリ

- SmartHotel オンプレミス アプリは 2 つの VM (WEBVM と SQLVM) に階層化されています。
- VM は、VMware ESXi ホスト **contosohost1.contoso.com** (バージョン 6.5) 上にあります。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (**vcenter.contoso.com**) によって管理されています。
- Contoso にはオンプレミスのデータセンター (contoso-datacenter) があり、そこにオンプレミスのドメイン コントローラー (**contosodc1**) が含まれています。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。


### <a name="proposed-solution"></a>提案されるソリューション

- このアプリのデータベース層について、Contoso は[こちらの記事](https://docs.microsoft.com/azure/sql-database/sql-database-features)を使用して Azure SQL Database と SQL Server を比較しました。 そして、いくつかの理由で Azure SQL Database を使用することにしました。
    - Azure SQL Database は、リレーショナル データベース マネージド サービスです。 複数のサービス レベルで予測可能なパフォーマンスを実現します。管理作業はほぼゼロです。 利点としては、ダウンタイムのない動的スケーラビリティ、組み込みのインテリジェントな最適化、グローバルなスケーラビリティと可用性などがあります。
    - 軽量な Data Migration Assistant (DMA) を利用して、オンプレミス データベースを評価し、Azure SQL に移行することができます。
    - ソフトウェア アシュアランスに基づき、SQL Database では SQL Server 用の Azure ハイブリッド特典を利用して、既存のライセンスを割引料金のライセンスに交換することができます。 この方法なら最大 30% の節約が可能です。
    - SQL Database には、常に暗号化された動的データ マスキング、行レベルのセキュリティ/脅威検出など、さまざまなセキュリティ機能があります。
- アプリの Web 層については、Azure App Service を使用することに決めました。 この PaaS サービスにより、わずかな構成変更だけでアプリをデプロイできます。 Visual Studio を使用して変更を加え、2 つの Web アプリをデプロイします。 1 つは Web サイト用、もう 1 つは WCF サービス用です。
  
### <a name="solution-review"></a>ソリューションのレビュー
Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

**考慮事項** | **詳細**
--- | ---
**長所** | SmartHotel アプリ コードは、Azure に移行するために変更する必要があります。<br/><br/> SQL Server と Windows Server の両方に Azure ハイブリッド特典を使用して、ソフトウェア アシュアランスへの投資を利用できます。<br/><br/> 移行後は、Windows Server 2008 R2 をサポートする必要がなくなります。 [詳細情報](https://support.microsoft.com/lifecycle)。<br/><br/> 複数のインスタンスがあるアプリの Web 層を構成することができたので、単一障害点ではなくなりました。<br/><br/> 古い SQL Server 2008 R2 に依存しなくなります。<br/><br/> SQL Database は、Contoso の技術面の要件をサポートしています。 Database Migration Assistant を使用してオンプレミス データベースを評価し、互換性があることがわかりました。<br/><br/> SQL Database には、Contoso が設定する必要がないフォールト トレランス機能が組み込まれています。 そのため、データ層がフェールオーバーの単一ポイントではなくなります。
**短所** | Azure App Services は、各 Web アプリに対して 1 つのアプリのデプロイのみをサポートしています。 つまり、2 つの Web アプリをプロビジョニングする必要があります (Web サイト用に 1 つと WCF サービス用に 1 つ)。<br/><br/> Database Migration Service ではなく Data Migration Assistant を使用してデータベースを移行する場合、Contoso は大規模なデータベースを移行できるインフラストラクチャを持つ予定がありません。 プライマリ リージョンが利用不可の場合に、確実にフェールオーバーできるように別のリージョンを構築する必要があります。

## <a name="proposed-architecture"></a>提案されたアーキテクチャ

![シナリオのアーキテクチャ](media/contoso-migration-refactor-web-app-sql/architecture.png) 


### <a name="migration-process"></a>移行プロセス

1. Contoso は Azure SQL インスタンスをプロビジョニングし、SmartHotel データベースをそこに移行します。
2. Web アプリをプロビジョニングして構成し、SmartHotel アプリをそれらにデプロイします。

    ![移行プロセス](media/contoso-migration-refactor-web-app-sql/migration-process.png) 

### <a name="azure-services"></a>Azure サービス

**サービス** | **説明** | **コスト**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Contoso では DMA を使用して、Azure でのデータベースの機能に影響を与える可能性のある互換性の問題を評価し、検出します。 DMA は SQL のソースとターゲット間で機能パリティを評価し、パフォーマンスと信頼性の向上箇所を推奨します。 | これは無料でダウンロードできるツールです。
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | インテリジェントなフル マネージド リレーショナル クラウド データベース サービス。 | 機能、スループット、サイズに基づくコスト。 [詳細情報](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
[Azure App Services - Web Apps](https://docs.microsoft.com/azure/app-service/app-service-web-overview) | フルマネージド プラットフォームで強力なクラウド アプリを作成 | サイズ、場所、使用時間に基づくコスト。 [詳細情報](https://azure.microsoft.com/pricing/details/app-service/windows/)。

## <a name="prerequisites"></a>前提条件

このシナリオを実行するために作業担当者 (と Contoso) が必要とするものを以下に示します。

**要件** | **詳細**
--- | ---
**Azure サブスクリプション** | このシリーズの最初の記事で評価を行ったときに、サブスクリプションは既に作成しているはずです。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成してください。<br/><br/> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。<br/><br/> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。
**Azure インフラストラクチャ** | [Contoso で Azure インフラストラクチャを設定する方法](contoso-migration-infrastructure.md)を確認してください。


## <a name="scenario-steps"></a>シナリオのステップ

Contoso が移行を実行する方法を次に示します。

> [!div class="checklist"]
> * **手順 1: Azure に SQL Database インスタンスをプロビジョニングする**: Contoso は Azure に SQL インスタンスをプロビジョニングします。 アプリの Web サイトが Azure に移行されると、WCF サービス Web アプリはこのインスタンスを示します。
> * **手順 2: DMA を使用してデータベースを移行する**: Data Migration Assistant を使用してアプリ データベースを移行します。
> * **手順 3: Web アプリをプロビジョニングする**: 2 つの Web アプリをプロビジョニングします。
> * **手順 4: 接続文字列を構成する**: Web 層 Web アプリ、WCF サービス Web アプリ、および SQL インスタンスが通信できるように接続文字列を構成します。
> * **手順 5: Web アプリを発行する**: 最後の手順として、Contoso はアプリを Azure に発行します。


## <a name="step-1-provision-an-azure-sql-database"></a>手順 1: Azure SQL Database をプロビジョニングする

1. Azure で SQL Database を作成することを選択します。 

    ![SQL をプロビジョニングする](media/contoso-migration-refactor-web-app-sql/provision-sql1.png)

2. オンプレミス VM 上で実行されているデータベースと一致するようにデータベース名を指定します (**SmartHotel.Registration**)。 ContosoRG リソース グループにデータベースを配置します。 これは、Azure の運用リソースに使用するリソース グループです。

    ![SQL をプロビジョニングする](media/contoso-migration-refactor-web-app-sql/provision-sql2.png)

3. プライマリ リージョンに新しい SQL Server インスタンス (**sql-smarthotel-eus2**) を設定します。

    ![SQL をプロビジョニングする](media/contoso-migration-refactor-web-app-sql/provision-sql3.png)

4. サーバーとデータベースのニーズに合わせて価格レベルを設定します。 既に SQL Server のライセンスを持っているため、Azure ハイブリッド特典で費用を節約することにします。
5. サイズ設定については、仮想コアベースの購入を使用し、予想される要件の制限を設定します。

    ![SQL をプロビジョニングする](media/contoso-migration-refactor-web-app-sql/provision-sql4.png)

6. 次に、データベース インスタンスを作成します。

    ![SQL をプロビジョニングする](media/contoso-migration-refactor-web-app-sql/provision-sql5.png)

7. インスタンスの作成後、データベースを開き、移行に Data Migration Assistant を使用するときに必要な詳細情報を書き留めます。

    ![SQL をプロビジョニングする](media/contoso-migration-refactor-web-app-sql/provision-sql6.png)


**さらにサポートが必要な場合**

- SQL Database のプロビジョニングの[手順を参照](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)します。
- 仮想コア リソースの制限の詳細については[こちら](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)を参照してください。


## <a name="step-2-migrate-the-database-with-dma"></a>手順 2: DMA を使用してデータベースを移行する

Contoso は DMA を使用して SmartHotel データベースを移行します。

### <a name="install-dma"></a>DMA をインストールする

1. [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=53595) からオンプレミスの SQL Server VM (**SQLVM**) にツールをダウンロードします。
2. VM 上でセットアップ (DownloadMigrationAssistant.msi) を実行します。
3. **[完了]** ページで、ウィザードを終了する前に **[Microsoft Data Migration Assistant の起動]** を選択します。

### <a name="migrate-the-database-with-dma"></a>DMA を使用してデータベースを移行する

1. DMA で新しいプロジェクトを作成し (**SmartHotelDB**)、**[移行]** を選択します 
2. ソース サーバーの種類として **SQL Server**、ターゲットとして **Azure SQL Database** を選択します。 

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-1.png)

3. 移行の詳細では、ソース サーバーとして **SQLVM** を追加し、**SmartHotel.Registration** データベースを追加します。 

     ![DMA](media/contoso-migration-refactor-web-app-sql/dma-2.png)

4. 認証に関連しているようなエラーが表示されます。 ただし、調査の結果、問題はデータベース名のピリオド (.) であることがわかりました。 この問題を解決するため、回避策として、**SmartHotel-Registration** という名前を使用して新しい SQL Database をプロビジョニングすることに決めました。 再度 DMA を実行すると、**SmartHotel-Registration** を選択してウィザードを続行できます。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-3.png)

5. **[オブジェクトの選択]** で、データベース テーブルを選択し、SQL スクリプトを生成します。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-4.png)

6. DMS でスクリプトが作成されたら、**[スキーマの配置]** をクリックします。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-5.png)

7. DMA で配置が成功したことを確認します。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-6.png)

8. 次は移行の開始です。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-7.png)

9. 移行が完了すると、Contoso はデータベースが Azure SQL インスタンス上で実行されていることを確認できます。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-8.png)

10. Azure portal で余計な SQL データベース **SmartHotel.Registration** を削除します。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-9.png)


## <a name="step-3-provision-web-apps"></a>手順 3: Web アプリをプロビジョニングする

データベースの移行が完了し、Contoso は 2 つの Web アプリをプロビジョニングできるようになりました。

1. ポータルで **[Web アプリ]** を選択します。

    ![Web アプリ](media/contoso-migration-refactor-web-app-sql/web-app1.png)

2. アプリ名 (**SHWEB-EUS2**) を指定し、Windows 上で実行し、運用リソース グループ **ContosoRG** に配置します。 新しいアプリ サービスとプランを作成します。

    ![Web アプリ](media/contoso-migration-refactor-web-app-sql/web-app2.png)

3. Web アプリがプロビジョニングされた後、WCF サービス (**SHWCF-EUS2**) 用に Web アプリを作成するプロセスを繰り返します

    ![Web アプリ](media/contoso-migration-refactor-web-app-sql/web-app3.png)

4. 完了したら、アプリのアドレスを参照して、アプリが正常に作成されたことを確認します。

## <a name="step-4-configure-connection-strings"></a>手順 4: 接続文字列を構成する

Contoso は、Web アプリとデータベースがすべて通信できることを確認する必要があります。 そのために、コードと Web アプリで接続文字列を構成します。

1. WCF サービス (**SHWCF-EUS2**) の Web アプリで、**[設定]** > **[アプリケーションの設定]** の順に選択し、**DefaultConnection** という新しい接続文字列を追加します。
2. 接続文字列は **SmartHotel-Registration** データベースからプルされ、正しい資格情報で更新する必要があります。

    ![接続文字列](media/contoso-migration-refactor-web-app-sql/string1.png)

3. Contoso は Visual Studio を使用して、**SmartHotel360-internal-booking-apps** フォルダーからソリューション ファイルを開きます。 WCF サービス SmartHotel.Registration.Wcf の web.config ファイルの **connectionStrings** セクションを接続文字列で更新する必要があります。

     ![接続文字列](media/contoso-migration-refactor-web-app-sql/string2.png)

4. SmartHotel.Registration.Web の web.config ファイルの **client** セクションは、WCF サービスの新しい場所を指すように変更する必要があります。 これは、サービス エンドポイントをホストする WCF Web アプリの URL です。

    ![接続文字列](media/contoso-migration-refactor-web-app-sql/strings3.png)


## <a name="step-5-publish-web-apps"></a>手順 5: Web アプリを発行する

最後の手順として、Contoso は Web アプリを Azure に発行します。

1. Visual Studio で、SmartHotel.REgistration.Wcf プロジェクトを右クリックし、**[発行]** をクリックします。

    ![[発行]](media/contoso-migration-refactor-web-app-sql/publish-web1.png)

2. 発行を開始します。

    ![[発行]](media/contoso-migration-refactor-web-app-sql/publish-web2.png)

3. 既に Web アプリを作成しているため、既存の App Service を選択します。

    ![[発行]](media/contoso-migration-refactor-web-app-sql/publish-web3.png)

4. WCF アプリを選択すると、Visual Studio によってそのアプリがデプロイされます。

    ![[発行]](media/contoso-migration-refactor-web-app-sql/publish-web4.png)

5. 次に、Web アプリ SmartHotel.Registration.Web を発行するためにプロセスを繰り返します。

    ![[発行]](media/contoso-migration-refactor-web-app-sql/publish-web5.png)


この時点で、アプリケーションは正常に Azure に移行されています。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行後、Contoso は、以下のクリーンアップ手順を完了する必要があります。  

- vCenter のインベントリからオンプレミスの VM を削除します。
- ローカルのバックアップ ジョブからから VM を削除します。
- SmartHotel アプリの新しい場所を示すように社内ドキュメントを更新します。 Azure SQL Database でデータベースは実行中と表示され、2 つの Web アプリでフロント エンドは実行中と表示されます。
- 使用停止されている VM と対話しているリソースがないか確認します。また、関連する設定やドキュメントがあれば、更新して新しい構成を反映します。


## <a name="review-the-deployment"></a>デプロイ環境を検討する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティ保護する必要があります。

### <a name="security"></a>セキュリティ

- Contoso は新しい **SmartHotel-Registration** データベースがセキュリティで保護されていることを確認する必要があります。 [詳細情報](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)。
- 特に、証明書で SSL を使用するように Web アプリを更新する必要があります。

### <a name="backups"></a>バックアップ

- Contoso は、Azure SQL Database のバックアップ要件を確認する必要があります。 [詳細情報](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。
- データベースのリージョン内フェールオーバーを提供するようにフェールオーバー グループを実装することを検討する必要があります。 [詳細情報](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)。
- Contoso は、回復性のために、メインの米国東部 2 リージョンと米国中部リージョンに Web アプリをデプロイすることを検討する必要があります。 リージョンの障害が発生した場合に確実にフェールオーバーするように Traffic Manager を構成できます。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- すべてのリソースをデプロイした後、Contoso は[インフラストラクチャ計画](contoso-migration-infrastructure.md#set-up-tagging)に基づいて Azure タグを割り当てる必要があります。
- すべてのライセンスは、Contoso が使用している PaaS サービスのコストに組み込まれています。 これは EA から差し引かれます。
1. Contoso は、Microsoft の子会社である Cloudyn からライセンスが供与される Azure Cost Management を有効にします。 これは、Azure やその他のクラウド リソースの利用や管理に役立つ、マルチクラウド対応のコスト管理ソリューションです。  Azure Cost Management の詳細については、[こちら](https://docs.microsoft.com/azure/cost-management/overview)を参照してください。 

## <a name="conclusion"></a>まとめ

この記事で、Contoso は、アプリのフロントエンド VM を 2 つの Azure Web Apps に移行することで、Azure の SmartHotel アプリをリファクターしました。 アプリ データベースを Azure SQL Database に移行しました。







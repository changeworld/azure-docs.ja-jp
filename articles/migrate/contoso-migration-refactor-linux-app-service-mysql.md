---
title: Contoso Linux サービス デスク アプリを Azure App Service と Azure MySQL にリファクタリングする | Microsoft Docs
description: Contoso が Web 層向けの GitHub を使用する Azure App Service と Azure SQL Database に移行することによって、オンプレミスの Linux アプリをリファクタリングする方法を説明します。
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 5981c708abdaa12a662075cc5bf5aae14ccc35c2
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002162"
---
# <a name="contoso-migration-refactor-a-contoso-linux-service-desk-app-to-the-azure-app-service-and-azure-mysql"></a>Contoso の移行: Contoso Linux サービス デスク アプリを Azure App Service と Azure MySQL にリファクタリングする

この記事では、Contoso が GitHub と統合された Azure App Service と Azure MySQL に移行することによって、オンプレミスの 2 階層 Linux サービス デスク アプリ (osTicket) をリファクタリングする方法を示します。

このドキュメントは、架空の会社 Contoso が、オンプレミス リソースをどのようにして Microsoft Azure クラウドに移行するかを示すシリーズ記事の第 10 弾です。 このシリーズには、背景情報とシナリオが含まれており、移行インフラストラクチャのセットアップ方法や、さまざまな種類の移行を実施する方法を具体的に説明しています。 シナリオが複雑になってきているため、さらに記事が追加される予定です。

**記事** | **詳細** | **状態**
--- | --- | ---
[記事 1: 概要](contoso-migration-overview.md) | Contoso の移行戦略、記事シリーズ、および使用するサンプル アプリの概要を示します。 | 使用可能
[記事 2: Azure インフラストラクチャのデプロイ](contoso-migration-infrastructure.md) | Contoso が移行に備えて、オンプレミスと Azure のインフラストラクチャをどのように準備するかを説明します。 Contoso のすべての移行シナリオで、同じインフラストラクチャが使用されます。 | 使用可能
[記事 3: オンプレミス リソースの評価](contoso-migration-assessment.md)  | Contoso が、VMware 上で実行されるオンプレミスの 2 階層アプリ、SmartHotel の評価をどのように実行するかを示します。 アプリの VM については [Azure Migrate](migrate-overview.md) サービスで、アプリの SQL Server データベースについては [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) で評価を行います。 | 使用可能
[記事 4: Azure VM および SQL Managed Instance へのリホスト](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso が SmartHotel アプリを Azure に移行する方法を説明します。 SQL Managed Instance に移行するために、アプリの Web VM は [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) を使用して移行し、アプリのデータベースは [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) サービスを使用して移行します。 | 使用可能
[記事 5: Azure VM へのリホスト](contoso-migration-rehost-vm.md) | Contoso が Site Recovery サービスを使用して、どのように SmartHotel アプリを Azure IaaS VM を移行するかを示します。
[記事 6: Azure VM および SQL Server 可用性グループへのリホスト](contoso-migration-rehost-vm-sql-ag.md) | Contoso が SmartHotel アプリを移行する方法を示します。 Contoso は、Site Recovery を使用してアプリの VM を移行し、Database Migration サービスを使用してアプリのデータベースを SQL Server 可用性グループに移行します。 | 使用可能
[記事 7: Linux アプリの Azure VM への再ホスト](contoso-migration-rehost-linux-vm.md) | Contoso が、Azure Site Recovery を使用して、どのように osTicket Linux アプリを Azure IaaS VM 移行するかを示します。
[記事 8: Azure VM と Azure MySQL Server での Linux アプリのリホスト](contoso-migration-rehost-linux-vm-mysql.md) | Contoso がどのように osTicket Linux アプリを移行するかを具体的に説明します。 VM の移行には Site Recovery を使用し、Azure MySQL Server インスタンスへの移行には MySQL Workbench を使用します。 | 使用可能
[記事 9: Azure Web Apps と Azure SQL Database でのアプリのリファクタリング](contoso-migration-refactor-web-app-sql.md) | Contoso が SmartHotel アプリを Azure コンテナー ベースのWeb アプリに移行し、アプリ データベースを Azure SQL Server に移行する方法を示します。 | 使用可能
記事 10: Azure Web Apps と Azure MySQL での Linux アプリのリファクタリング | Contoso が osTicket Linux アプリを PHP 7.0 Docker コンテナーを使用する Azure App Service に移行する方法を示します。 デプロイするためのコード ベースが GitHub に移行されます。 アプリのデータベースが Azure MySQL に移行されます。 | この記事
[記事 11: VSTS での TFS のリファクタリング](contoso-migration-tfs-vsts.md) | Contoso がオンプレミスの Team Foundation Server (TFS) のデプロイを Azure の Visual Studio Team Services (VSTS) に移行する方法を示します。 | 使用可能
[記事 12: Azure コンテナーと Azure SQL Database でのアプリの再構築](contoso-migration-rearchitect-container-sql.md) | Contoso が SmartHotel アプリを Azure に移行して再構築する方法を示します。 アプリの Web 階層を Windows コンテナーとして再構築し、Azure SQL Database でアプリ データベースを再構築します。 | 使用可能
[記事 13: Azure でのアプリのリビルド](contoso-migration-rebuild.md) | Contoso が Azure のさまざまな機能とサービス (App Services、Azure Kubernetes、Azure Functions、Cognitive Services、Cosmos DB など) を使用して SmartHotel アプリをリビルドする方法を示します。 | 使用可能

この記事で Contoso は、2 階層の Linux Apache MySQL PHP (LAMP) サービスデスク アプリ (osTicket) を Azure に移行します。 このオープンソース アプリを使用したい場合は、[GitHub](https://github.com/osTicket/osTicket) からダウンロードできます。



## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らが何を達成しようとしているのかを理解しました。

- **ビジネスの成長に対処**: Contoso は成長し続けており、新しい市場に進出しつつあります。 Contoso は、顧客サービス担当者を追加する必要があります。 
- **スケーリング**: Contoso は、事業規模が拡大したときに、顧客サービス担当者を追加できるように、ソリューションを構築する必要があります。
- **回復性の向上**: 過去にシステムで発生した問題は、内部ユーザーのみに影響していました。 新しいビジネス モデルでは、外部ユーザーも影響を受けるため、Contoso は、アプリを常時稼働させる必要があります。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、最適な移行方法を決定するために、この移行の目標を明確にしました。

- アプリケーションは、現在のオンプレミスの容量とパフォーマンスを越えるようにスケーリングする必要があります。  Contoso は、Azure のオンデマンド スケーリングを活用するためにアプリケーションを移行します。
- Contoso は、アプリのコード ベースを継続的デリバリー パイプラインに移したいと考えています。  アプリの変更が GitHub にプッシュされたときに、Contoso は、運用スタッフのタスクなしでその変更をデプロイしたいと考えています。
- アプリケーションは、成長とフェールオーバーに対応するための機能を備えた回復力のあるものにする必要があります。 Contoso は、2 つの異なる Azure リージョンにアプリをデプロイし、自動的にスケーリングするように設定したいと考えています。
- Contoso は、アプリケーションがクラウドに移された後、データベース管理タスクを最小限にしたいと考えています。

## <a name="solution-design"></a>ソリューション設計
Contoso は、目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。


## <a name="current-architecture"></a>現在のアーキテクチャ

- アプリは 2 つの VM (OSTICKETWEB および OSTICKETMYSQL) にわたって階層化されます。
- VM は、VMware ESXi ホスト **contosohost1.contoso.com** (バージョン 6.5) 上に配置されます。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (**vcenter.contoso.com**) によって管理されています。
- Contoso にはオンプレミスのデータセンター (contoso-datacenter) があり、そこにオンプレミスのドメイン コントローラー (**contosodc1**) が含まれています。

![現在のアーキテクチャ](./media/contoso-migration-refactor-linux-app-service-mysql/current-architecture.png) 


## <a name="proposed-architecture"></a>提案されたアーキテクチャ

Contoso は、現在のアーキテクチャ、目標、および移行要件を特定した後、デプロイ ソリューションを設計し、移行に使用する Azure サービスを含め、移行プロセスを決めます。

- OSTICKETWEB 上の Web 層のアプリは、Azure App Service を 2 つの Azure リージョン内にビルドすることで移行されます。 Azure App Service for Linux は、PHP 7.0 Docker コンテナーを使用して実装されます。
- アプリ コードは GitHub に移動され、Azure Web Apps は GitHub を使用した継続的デリバリー用に構成されます。
- Azure App Servers は、プライマリ リージョン (E米国東部 2) とセカンダリ リージョン (米国中部) の両方にデプロイされます。
- 両方のリージョンで、2 つの Azure Web Apps の前に Traffic Manager が設定されます。
- Traffic Manager は、トラフィックを強制的に米国東部 2 を通過させるために、優先モードで構成されます。
- 米国東部 2 リージョン内の Azure App Server がオフラインになった場合でも、ユーザーは、米国中部リージョンにフェールオーバーされたアプリにアクセスできます。
- アプリのデータベースは、MySQL Workbench ツールを使用して Azure MySQL PaaS サービスに移行されます。 オンプレミスのデータベースはローカルでバックアップされ、Azure MySQL に直接 復元されます。
- このデータベースは、プライマリ リージョンである米国東部 2 で、運用ネットワーク (VNET-PROD-EUS2) のデータベース サブネット (PROD-DB-EUS2) に存在します。
- 運用ワークロードを移行しようとしているため、アプリの Azure リソースは、運用リソース グループ **ContosoRG** 内に存在することになります。
- Traffic Manager リソースは、Contoso のインフラストラクチャ リソース グループ **ContosoInfraRG** にデプロイされます。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。


![シナリオのアーキテクチャ](./media/contoso-migration-refactor-linux-app-service-mysql/proposed-architecture.png) 


## <a name="migration-process"></a>移行プロセス

Contoso は、次のようにして移行プロセスを完了します。

1. 最初のステップとして、Contoso は、Azure インフラストラクチャをセットアップします。これには、Azure App Services のプロビジョニング、Traffic Manager の設定、および Azure MySQL インスタンスのプロビジョニングが含まれます。
2. Azure の準備ができたら、MySQL Workbench を使用してデータベースを移行します。 
3. Azure でデータベースが実行されたら、継続的デリバリーで使用する Azure App Service 用の GitHub プライベート リポジトリを設定し、それを osTicket アプリで読み込みます。
4. Azure Portal で、GitHub から Azure App Service で実行される Docker コンテナーにアプリを読み込みます。 
5. DNS の設定を調整し、アプリの自動スケールを構成します。

![移行プロセス](./media/contoso-migration-refactor-linux-app-service-mysql/migration-process.png) 


### <a name="azure-services"></a>Azure サービス

**サービス** | **説明** | **コスト**
--- | --- | ---
[Mobile Apps](https://azure.microsoft.com/services/app-service/) | サービスを実行し、Web サイト向けの Azure PaaS サービスを使用して、アプリケーションをスケーリングします。  | 価格は、インスタンスのサイズと必要な機能に基づきます。 [詳細情報](https://azure.microsoft.com/pricing/details/app-service/windows/)。
[Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) | DNS を使用して、Azure、外部 Web サイト、またはサービスにユーザーを送るロード バランサー。 | 価格は、受信した DNS クエリの数と監視対象のエンドポイントの数に基づきます。 | [詳細情報](https://azure.microsoft.com/pricing/details/traffic-manager/)。
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) | データベースは、オープン ソースの MySQL Server エンジンに基づいています。 これは、アプリの開発とデプロイに向けたサービスとしての、完全管理型のエンタープライズ対応コミュニティ MySQL データベースです。 | 価格は、コンピューティング、ストレージ、およびバックアップ要件に基づきます。 [詳細情報](https://azure.microsoft.com/pricing/details/mysql/)。

 
## <a name="prerequisites"></a>前提条件

作業担当者 (と Contoso) がこのシナリオを実行する場合は、次のものが必要です。

**要件** | **詳細**
--- | ---
**Azure サブスクリプション** | このシリーズの最初の方の記事で、既にサブスクリプションを作成しているはずです。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成してください。<br/><br/> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。<br/><br/> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。<br/><br/> さらに詳細なアクセス許可が必要な場合は、[こちらの記事](../site-recovery/site-recovery-role-based-linked-access-control.md)をご覧ください。 
**Azure インフラストラクチャ** | Contoso は、[移行のための Azure インフラストラクチャ](contoso-migration-infrastructure.md)についての記事で説明されているように、Azure インフラストラクチャを設定します。



## <a name="scenario-steps"></a>シナリオのステップ

Azure で、この移行がどのように完了されるかを示します。

> [!div class="checklist"]
> * **ステップ 1: Azure App Services をプロビジョニングする**: Contoso は、プライマリ リージョンとセカンダリ リージョンに Web Apps をプロビジョニングします。
> * **ステップ 2: Traffic Manager を設定する**: Contoso は、トラフィックのルーティングと負荷分散を行う Traffic Manager を Web アプリの前に設定します。
> * **ステップ 3: MySQL をプロビジョニングする**: Azure で、Contoso は、Azure MySQL データベースのインスタンスをプロビジョニングします。
> * **ステップ 4: データベースを移行する**: Contoso は、MySQL Workbench を使用してデータベースを移行します。 
> * **ステップ 5: GitHub を設定する**: Contoso は、アプリの Web サイト/コード用のローカル GitHub リポジトリを設定します。
> * **ステップ 6: Web アプリをデプロイする**: Contoso は、GitHub から Web アプリをデプロイします。




## <a name="step-1-provision-azure-app-services"></a>ステップ 1: Azure App Services をプロビジョニングする

Contoso は、Azure App Services を使用する 2 つの Web アプリを (各リージョンに 1 つづつ) プロビジョニングします。

1. Azure Marketplace からプライマリ リージョンである米国東部 2 内に Web App リソース (**osticket eus2**) を作成します。
2. 運用リソース グループ **ContosoRG** にリソースを配置します。

    ![Azure アプリ](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app1.png) 

3. プライマリ リージョンで、標準サイズを使用する新しい App Service プラン (**APP-SVP-EUS2**) を作成します。

     ![Azure アプリ](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app2.png) 
    
4. Contoso は、Docker コンテナーである PHP 7.0 ランタイム スタックを使用する Linux OS を選択します。

    ![Azure アプリ](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app3.png) 

5. 米国中部リージョンで、2 つ目の Web アプリ (**osticket-cus**) とApp Service プランを作成します。

    ![Azure アプリ](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app4.png) 


**さらにサポートが必要な場合**

- 「[Web Apps の概要](https://docs.microsoft.com/azure/app-service/app-service-web-overview)」を参照します。
- 「[Azure App Service on Linux の概要](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)」を参照します。


## <a name="step-2-set-up-traffic-manager"></a>ステップ 2: Azure Traffic Manager を設定する

Contoso は、受信 Web 要求を osTicket Web 層で実行中の Web Apps に送る Traffic Manager を設定します。

1. Contoso は、Azure Marketplace から Traffic Manager リソース (**osticket.trafficmanager.net**) を作成します。 米国東部 2 がプライマリ サイトになるように、優先順位によるルーティングを使用します。 そのリソースを、Contoso のインフラストラクチャ リソース グループ (**ContosoInfraRG**) 内に配置します。 Traffic Manager はグローバルであり、特定の場所にバインドされないことに注意してください。

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager1.png) 

2. ここで、Contoso は、Traffic Manager のエンドポイントを構成します。 米国東部 2 の Web アプリをプライマリ サイト (**osticket eus2**) として追加し、米国中部のアプリをセカンダリ (**osticket cu**) として追加します。

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager2.png) 

3. エンドポイントを追加した後、それらを監視できます。

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager3.png)

**さらにサポートが必要な場合**

- 「[Traffic Manager の概要](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)」を参照します。
- [優先順位の高いエンドポイントへのトラフィックのルーティング](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-priority-routing-method)に関する記事を参照します。
 
## <a name="step-3-provision-azure-database-for-mysql"></a>ステップ 3: Azure Database for MySQL をプロビジョニングする

Contoso は、MySQL データベース インスタンスを、プライマリの米国東部 2 リージョン内にプロビジョニングします。

1. Azure Portal で、Azure Database for MySQL のリソースを作成します。 

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-1.png)

2. Azure データベースの名前 **contosoosticket** を追加します。 運用リソース グループ **ContosoRG** にデータベースを追加し、そのための資格情報を指定します。
3. オンプレミスの MySQL データベースはバージョン 5.7 なので、互換性のためにこのバージョンが選択されています。 既定のサイズを使用しますが、それらはデータベースの要件に適合しています。

     ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-2.png)

4. **バックアップ冗長オプション**については、Contoso は **Geo 冗長**を使用することを選択しています。 このオプションでは、停止が発生した場合、セカンダリの米国中部リージョンでデータベースを復元できます。 このオプションは、データベースをプロビジョニングするときにのみ構成できます。

    ![冗長性](./media/contoso-migration-refactor-linux-app-service-mysql/db-redundancy.png)

4. Contoso は、接続のセキュリティを設定します。 データベースの **[接続のセキュリティ]** で、データベースによる Azure サービスへのアクセスを許可するファイアウォール ルールを設定します。
5. 開始 IP アドレスと終了 IP アドレスに、ローカル ワークステーションのクライアント IP アドレスを追加します。 これにより、Web アプリが MySQL データベースと、移行を実行するデータベース クライアントにアクセスできるようになります。

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-3.png)



## <a name="step-4-migrate-the-database"></a>ステップ 4: データベースを移行する

Contoso は、MySQL ツールでバックアップと復元を使用して、データベースを移行します。 MySQL Workbench をインストールし、OSTICKETMYSQL からデータベースをバックアップして、Azure Database for MySQL Server に復元します。

### <a name="install-mysql-workbench"></a>MySQL Workbench のインストール

1. [MySQL Workbench の前提条件を確認してダウンロード](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool)します。
2. [インストールの指示](https://dev.mysql.com/doc/workbench/en/wb-installing.html)に従って、MySQL Workbench for Windows をインストールします。 インストール先のマシンは、OSTICKETMYSQL VM と、インターネット経由で Azure にアクセス可能である必要があります。
3. MySQL Workbench で、OSTICKETMYSQL への MySQL 接続を作成します。 

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench1.png)

4. データベースを **osticket** として、ローカルの自己完結型ファイルにエクスポートします。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench2.png)

5. データベースがローカルにバックアップされたら、Azure Database for MySQL インスタンスへの接続を作成します。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench3.png)

6. これで、自己完結型ファイルから、Azure MySQL インスタンス内にあるデータベースをインポート (復元) できます。 このインスタンスのために新しいスキーマ (osticket) が作成されます。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench4.png)

7. データが復元されると Workbench を使用してクエリを実行でき、Azure Portal に表示されます。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench5.png)

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench6.png)

8. 最後に、Contoso は、Web アプリ上のデータベース情報を更新する必要があります。 MySQL インスタンスで、**[接続文字列]** を開きます。 

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench7.png)

9. 文字列の一覧で、Web アプリの設定を見つけ、クリックしてそれらをコピーします。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench8.png)

10. メモ帳ウィンドウを開き、新しいファイルに文字列を貼り付け、osticket データベース、MySQL インスタンス、および資格情報の設定と一致するように文字列を更新します。

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench9.png)

11. Contoso は、Azure Portal で MySQL インスタンスの**概要**からサーバー名とのログインを確認できます。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench10.png)


## <a name="step-5-set-up-github"></a>ステップ 5: GitHub を設定する

Contoso は、新しいプライベート GitHub リポジトリを作成し、Azure MySQL の osTicket データベースへの接続を設定します。 次に、Azure Web Apps とアプリを読み込みます。  

1.  OsTicket ソフトウェアのパブリック GitHub リポジトリを参照し、Contoso GitHub アカウントにフォークします。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github1.png)

2. フォークしたら、**include** フォルダーに移動し、**ost-config.php** ファイルを検索します。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github2.png)


3. ブラウザーでファイルを開いて編集します。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github3.png)

4. エディターで、Contoso は、データベースの詳細 (具体的には **DBHOST** と **DBUSER**) を更新します。 

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github4.png)

5. 次に、変更をコミットします。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github5.png)

6. 各 Web アプリ (**osticket-eus2** と **osticket-cus**) に対して、Contoso は、Azure Portal で **[アプリケーション設定]** を変更します。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github6.png)

7. **osticket** という名前で接続文字列を入力し、メモ帳から**領域の値**に文字列をコピーします。 文字列の横のドロップダウン リストから **[MySQL]** を選択し、設定を保存します。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github7.png)

## <a name="step-6-configure-the-web-apps"></a>ステップ 6: Web アプリを構成する

移行プロセスの最後のステップとして、Contoso は、Web アプリと osTicket Web サイトを構成します。



1. プライマリ Web アプリ (**osticket-eus2**) で **[デプロイ オプション]** を開き、ソースを **[GitHub]** に設定します。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app1.png)

2. デプロイ オプションを選択します。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app2.png)

3. オプションを設定すると、その構成が Azure Portal に保留中として表示されます。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app3.png)

4. 構成が更新され、GitHub から Azure App Service を実行中の Docket コンテナーに osTicket Web アプリが読み込まれ、サイトがアクティブとして表示されます。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app4.png)

5. Contoso は、セカンダリ Web アプリ (**osticket-cus**) に対して、上記のステップを繰り返します。
6. サイトが構成されたら、Traffic Manager プロファイルを使用してアクセスできます。 DNS 名は、osTicket アプリの新しい場所になります。 [詳細情報](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record)。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app5.png)
    
7. Contoso では、覚えやすい DNS 名を求めています。 Traffic Manager の名を指すエイリアス レコード (CNAME) **osticket.contoso.com** を、ドメイン コントローラーの DNS 内に作成します。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app6.png)

8. カスタム ホスト名を許可するように **osticket-eus2** Web アプリと **osticket-cus** Web アプリの両方を構成します。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app7.png)

### <a name="set-up-autoscaling"></a>自動スケールを設定する

最後に、アプリの自動スケールを設定します。 これにより、エージェントがアプリを使用するときに、ビジネス ニーズに応じてアプリのインスタンスが増減することが保証されます。 

1. Contoso は、アプリ サービス **APP-SRV-EUS2** で、**[スケール ユニット]** を開きます。
2. 現在のインスタンスの CPU の割合が 10 分間 70% を超える場合はインスタンス数を 1 つ増加させるという単一のルールを持つ新しい自動スケール設定を構成します。

    ![自動スケール](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale1.png)

3. 同じ設定を **APP-SRV-CUS** でも構成して、アプリがセカンダリ リージョンにフェールオーバーした場合に、同じ動作が適用されるようにします。 唯一の違いは、これは目的がフェールオーバーのみであるため、インスタンス数の上限を 1 に設定することです。

   ![自動スケール](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale2.png)

##  <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了すると、osTicket アプリは、プライベート GitHub リポジトリを使用して継続的デリバリーを行う Azure Web Apps で実行されるようにリファクタリングされます。 アプリが 2 つのリージョンで実行されることで、回復性が向上します。 PaaS プラットフォームへの移行後、OsTicket データベースは、MySQL 用の Azure データベースで実行されます。

ここで、以下のことを行う必要があります。 
- VMware VM を vCenter インベントリから削除します。
- ローカルのバックアップ ジョブからからオンプレミスの VM を削除します。
- 社内のドキュメントを更新して、新しい場所と IP アドレスを示します。 
- こうしたオンプレミスの VM と対話しているリソースがないか確認し、新しい構成が反映されるように、関連する設定やドキュメントをすべて更新します。
- アプリが起動していることを追跡するために、osticket-trafficmanager.net URL をポイントするように監視を再構成します。

## <a name="review-the-deployment"></a>デプロイを再調査する

アプリが実行されるようになり、Contoso は新しいインフラストラクチャを完全に操作可能にして、セキュリティで保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso のセキュリティ チームは、アプリを再調査して、セキュリティの問題を特定します。 Contoso は、OsTicket アプリと MySQL データベース インスタンス間の通信が SSL 用に構成されていないことを識別しました。 データベース トラフィックをハッキングできないようにするため、これを行う必要があります。 [詳細情報](https://docs.microsoft.com/azure/mysql/howto-configure-ssl)。

### <a name="backups"></a>バックアップ

- OsTicket Web アプリには状態データが含まれていないため、バックアップは必要ありません。
- データベースのバックアップを構成する必要はありません。 Azure Database for MySQL は、サーバーのバックアップを自動的に作成して保存します。 彼らはデータベースのために geo 冗長性を使用することを選択したため、このデータベースは耐障害性があり、運用準備ができています。 バックアップを使用すると、サーバーを特定の時点に復元できます。 [詳細情報](https://docs.microsoft.com/azure/mysql/concepts-backup)。


### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- PaaS のデプロイにライセンスの問題はありません。
- Contoso は、Microsoft の子会社である Cloudyn からライセンスが供与される Azure Cost Management を有効にします。 これは、Azure やその他のクラウド リソースの利用や管理に役立つ、マルチクラウド対応のコスト管理ソリューションです。  Azure Cost Management の詳細については、[こちら](https://docs.microsoft.com/azure/cost-management/overview)を参照してください。




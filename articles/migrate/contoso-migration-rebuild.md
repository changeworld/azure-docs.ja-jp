---
title: Contoso のオンプレミス アプリを Azure にリビルドする | Microsoft Docs
description: Contoso が Azure App Services、Kubernetes サービス、CosmosDB、Azure Functions、および Cognitive サービスを使用して Azure にアプリをリビルドする方法について説明します。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: raynew
ms.openlocfilehash: 0d195d5fbede3100c0474ae9614a880cfb3acb19
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005001"
---
# <a name="contoso-migration-rebuild-an-on-premises-app-to-azure"></a>Contoso の移行: オンプレミス アプリを Azure にリビルドする

この記事では、Contoso が SmartHotel アプリを Azure に移行し、リビルドする方法について説明します。 アプリのフロントエンド VM を、Azure App Services の Web アプリに移行します。 アプリのバックエンドは、Azure Kubernetes Service (AKS) によって管理されるコンテナーにデプロイされたマイクロサービスを使用してビルドされます。 サイトは、ペットの写真の機能を提供する Azure Functions と対話します。 

このドキュメントは、架空の会社 Contoso がオンプレミス リソースを Microsoft Azure クラウドに移行する方法を説明するシリーズ記事の 1 つです。 このシリーズには背景情報やシナリオが含まれ、移行インフラストラクチャのセットアップ、移行のためのオンプレミス リソースへのアクセス、およびさまざまな種類の移行の実行が説明されています。 シナリオが複雑になってきているため、さらに記事が追加される予定です。

**記事** | **詳細** | **状態**
--- | --- | ---
[記事 1: 概要](contoso-migration-overview.md) | Contoso の移行戦略、記事シリーズ、および使用するサンプル アプリの概要を示します。 | 使用可能
[記事 2: Azure インフラストラクチャのデプロイ](contoso-migration-infrastructure.md) | Contoso が移行に備えてオンプレミスおよび Azure インフラストラクチャをどのように準備するかを説明します。 移行に関するすべてのアーティクルでは同じインフラストラクチャが使用されます。 | 使用可能
[記事 3: オンプレミス リソースの評価](contoso-migration-assessment.md)  | VMware で実行されているオンプレミスの 2 階層 SmartHotel アプリの評価を Contoso が実行する方法を説明します。 Contoso は、アプリの VM は [Azure Migrate](migrate-overview.md) サービスを使用して、アプリの SQL Server データベースは [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) を使用して評価します。 | 使用可能
[記事 4: Azure VM および SQL Managed Instance でのアプリのリホスト](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso が、SmartHotel アプリの Azure へのリフトアンドシフト移行を実行する方法を説明します。 Contoso は、[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) を使用してアプリのフロントエンド VM を移行し、[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) を使用してアプリのデータベースを SQL Managed Instance に移行します。 | 使用可能
[記事 5: Azure VM でのアプリのリホスト](contoso-migration-rehost-vm.md) | Contoso が Site Recovery のみを使用して SmartHotel アプリ VM を移行する方法を説明します。 | 使用可能
[記事 6: Azure VM および SQL Server Always On 可用性グループへのアプリのリホスト](contoso-migration-rehost-vm-sql-ag.md) | Contoso が SmartHotel アプリを移行する方法を示します。 Contoso は、Site Recovery を使用してアプリの VM を移行し、Database Migration Service を使用してアプリのデータベースを AlwaysOn 可用性グループで保護されている SQL Server クラスターに移行します。 | 使用可能
[記事 7: Linux アプリの Azure VM への再ホスト](contoso-migration-rehost-linux-vm.md) | Contoso が Site Recovery を使用して Azure VM への Linux osTicket アプリのリフトアンドシフト移行を実行する方法を説明します。 | 使用可能
[記事 8: Azure VM および Azure MySQL Server での Linux アプリのリホスト](contoso-migration-rehost-linux-vm-mysql.md) | Contoso が Site Recovery を使用して Linux osTicket アプリを Azure VM に移行する方法、および MySQL Workbench を使用してアプリのデータベースを Azure MySQL Server インスタンスに移行する方法を説明します。 | 使用可能
[記事 9: Azure Web Apps および Azure SQL Database でのアプリのリファクター](contoso-migration-refactor-web-app-sql.md) | Contoso が SmartHotel アプリを Azure Web アプリに移行して、アプリ データベースを Azure SQL Server インスタンスに移行する方法を示します。 | 使用可能
[記事 10: Azure Web Apps および Azure MySQL への Linux アプリのリファクター](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso が Linux osTicket アプリを複数のサイトの (継続的デリバリーのために GitHub と統合された) Azure Web Apps に移行する方法を示します。 Contoso は、アプリ データベースを Azure MySQL インスタンスに移行します。 | 使用可能
[記事 11: VSTS での TFS のリファクター](contoso-migration-tfs-vsts.md) | Contoso がオンプレミスの Team Foundation Server (TFS) デプロイを Azure の Visual Studio Team Services (VSTS) に移行する方法を示します。 | 使用可能
[記事 12: Azure コンテナーおよび Azure SQL Database へのアプリの再構築](contoso-migration-rearchitect-container-sql.md) | Contoso が SmartHotel アプリを Azure に移行して再構築する方法を示します。 Contoso は、アプリの Web 階層を Windows コンテナーとして再構築し、Azure SQL Database でアプリ データベースを再構築します。 | 使用可能
記事 13: Azure へのアプリのリビルド | Contoso が Azure のさまざまな機能とサービス (App Services、Azure Kubernetes、Azure Functions、Cognitive サービス、および Cosmos DB) を使用して SmartHotel アプリをリビルドする方法を示します。 | この記事

この記事では、Contoso は、VMware VM で実行されている 2 階層の Windows . NET SmartHotel アプリを Azure に移行します。 このアプリを使用したい場合は、オープン ソースとして提供されていますので、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードしてください。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応**: Contoso は成長を続けています。 自社の Web サイトで、差別化されたエクスペリエンスを顧客に提供したいと考えています。
- **機敏性**: Contoso は、グローバル経済で成功を収めるために、より迅速に市場の変化に対応できる必要があります。 
- **拡張性**: ビジネスが順調に成長していく中で、Contoso IT は、同じペースで拡張可能なシステムを提供する必要があります。
- **コスト**: Contoso はライセンス コストを最小限に抑えたいと考えています。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行のためのアプリの要件を設定しました。 これらの要件を使用して、最良の移行方法を決定しました。
 - このアプリは、Azure でも現在と同じくクリティカルです。 優れたパフォーマンスを発揮し、容易にスケールできる必要があります。
 - このアプリでは、IaaS コンポーネントを使用しません。 PaaS またはサーバーレス サービスを使用するようにすべてをビルドする必要があります。
 - アプリのビルドはクラウド サービスで実行する必要があり、コンテナーはクラウド内のプライベートなエンタープライズ規模のコンテナー レジストリに存在する必要があります。
 - アプリで下された決定がホテルで受け入れられる必要があるため、ペットの写真に使用される API サービスは正確かつ実世界で信頼できるものである必要があります。 アクセス権を付与されたペットは、ホテルでの滞在を許可されます。

## <a name="solution-design"></a>ソリューション設計

Contoso は目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。

### <a name="current-app"></a>現在のアプリ

- SmartHotel オンプレミス アプリは 2 つの VM (WEBVM と SQLVM) に階層化されています。
- VM は、VMware ESXi ホスト **contosohost1.contoso.com** (バージョン 6.5) 上にあります。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (**vcenter.contoso.com**) によって管理されています。
- Contoso にはオンプレミスのデータセンター (contoso-datacenter) があり、そこにオンプレミスのドメイン コントローラー (**contosodc1**) が含まれています。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。


### <a name="proposed-architecture"></a>提案されたアーキテクチャ

- アプリのフロントエンドは、プライマリ リージョンに Azure App Services の Web アプリとしてデプロイされます。
- Azure 関数がペットの写真のアップロードを提供し、サイトはこの機能と対話します。
- ペットの写真の関数は、Cognitive Services Vision API と Cosmos DB を利用します。
- サイトのバックエンドは、マイクロ サービスを使用してビルドされます。 これらは、Azure Kubernetes Service (AKS) で管理されるコンテナーにデプロイされます。
- コンテナーは VSTS を使用してビルドされ、Azure Container Registry (ACR) にプッシュされます。
- Contoso は当面、Visual Studio を使用して Web アプリと関数コードを手動でデプロイする予定です。
- マイクロサービスは、Kubernetes コマンドライン ツールを呼び出す PowerShell script スクリプトを使用してデプロイされます。

    ![シナリオのアーキテクチャ](./media/contoso-migration-rebuild/architecture.png) 

  
### <a name="solution-review"></a>ソリューションのレビュー
Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

**考慮事項** | **詳細**
--- | ---
**長所** | Contoso はエンドツーエンドのデプロイに PaaS とサーバーレス ソリューションを使用することで、管理に要する時間を大幅に削減できます。<br/><br/> マイクロサービス アーキテクチャへの移行により、Contoso は時間の経過とともにソリューションを容易に拡張できるようになります。<br/><br/> 新しい機能は、既存のソリューションのコード ベースを妨げずにオンライン状態にすることができます。<br/><br/> Web アプリは複数のインスタンスで構成されるため、単一障害点が発生しません。<br/><br/> アプリがさまざまなトラフィック量を処理できるように、自動スケールが有効化されます。<br/><br/> PaaS サービスへの移行に伴い、Contoso は Windows Server 2008 R2 オペレーティング システム上で実行されている古いソリューションを廃止できます。<br/><br/> CosmosDB にはフォールト トレランスが組み込まれており、Contoso による構成は必要ありません。 これは、データ層が単一のフェールオーバー ポイントでなくなることを意味します。
**短所** | コンテナーは他の移行オプションより複雑です。 Contoso にとって、学習曲線が問題になる可能性があります。  この曲線にもかかわらず、Contoso は多くの価値を提供する新しいレベルの複雑さを導入します。<br/><br/> Azure、コンテナー、アプリのマイクロサービスを理解し、サポートできるように、Contoso の運用チームを立ち上げる必要があります。<br/><br/> Contoso は、ソリューション全体の DevOps を完全に実装していません。 AKS、関数、および App Services へのサービスのデプロイに関して、そのことを考慮する必要があります。



### <a name="migration-process"></a>移行プロセス

1. Contoso は、ACR、AKS、および CosmosDB をプロビジョニングします。
2. デプロイのインフラストラクチャ (Azure Web App、ストレージ アカウント、関数、および API) をプロビジョニングします。 
3. インフラストラクチャの準備が整ったら、VSTS を使用してマイクロサービスのコンテナー イメージをビルドし、ACR にプッシュします。
4. Contoso は、PowerShell スクリプトを使用してこれらのマイクロサービスを ASK にデプロイします。
5. 最後に、Azure 関数と Web App をデプロイします。

    ![移行プロセス](./media/contoso-migration-rebuild/migration-process.png) 

### <a name="azure-services"></a>Azure サービス

**サービス** | **説明** | **コスト**
--- | --- | ---
[AKS](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Kubernetes の管理、デプロイ、操作を簡略化します。 完全に管理された Kubernetes コンテナー オーケストレーション サービスを提供します。  | AKS は無料サービスです。  仮想マシンと、関連するストレージとネットワーク リソースの使用した分に対してのみ料金が発生します。 [詳細情報](https://azure.microsoft.com/pricing/details/kubernetes-service/)。
[Azure Functions](https://azure.microsoft.com/services/functions/) | イベント ドリブン型のサーバーレス コンピューティング エクスペリエンスにより、開発を高速化できます。 オンデマンドでスケールできます。  | 使用したリソースに対してのみ料金が発生します。 プランでは、1 秒あたりのリソースの使用量と実行回数に基づいて課金されます。 [詳細情報](https://azure.microsoft.com/pricing/details/functions/)。
[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | あらゆる種類のコンテナー デプロイのイメージを保存します。 | コストは機能、ストレージ、使用期間に基づいて発生します。 [詳細情報](https://azure.microsoft.com/pricing/details/container-registry/)。
[Mobile Apps](https://azure.microsoft.com/services/app-service/containers/) | あらゆるプラットフォームで稼働するエンタープライズグレードの Web アプリ、モバイル アプリ、API アプリをすばやくビルド、デプロイ、およびスケーリングできます。 | App Service プランは、1 秒単位で課金されます。 [詳細情報](https://azure.microsoft.com/pricing/details/app-service/windows/)。

## <a name="prerequisites"></a>前提条件

このシナリオを実行するために作業担当者 (と Contoso) が必要とするものを以下に示します。

**要件** | **詳細**
--- | ---
**Azure サブスクリプション** | このシリーズの最初の記事で評価を行ったときに、サブスクリプションは既に作成しているはずです。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成してください。<br/><br/> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。<br/><br/> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。
**Azure インフラストラクチャ** | [Contoso で Azure インフラストラクチャを設定する方法](contoso-migration-infrastructure.md)を確認してください。
**開発者の前提条件** | Contoso は、開発者用ワークステーションに次のツールをインストールする必要があります。<br/><br/> - [Visual Studio 2017 Community エディション: バージョン 15.5](https://www.visualstudio.com/)<br/><br/> .NET ワークロードが有効。<br/><br/> [Git](https://git-scm.com/)<br/><br/> [Azure PowerShell](https://azure.microsoft.com/downloads/)<br/><br/> [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)<br/><br/> Windows コンテナーを使用するように設定された [Docker CE (Windows 10) または Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/)。



## <a name="scenario-steps"></a>シナリオのステップ

Contoso が移行を実行する方法を次に示します。

> [!div class="checklist"]
> * **ステップ 1: AKS と ACR をプロビジョニングする**: Contoso は PowerShell を使用して、管理対象の AKS クラスターと Azure コンテナー レジストリをプロビジョニングします。
> * **ステップ 2: Docker コンテナーをビルドする**: VSTS を使用して Docker コンテナーの CI を設定し、ACR にプッシュします。
> * **ステップ 3: バックエンドのマイクロサービスをデプロイする**: バックエンドのマイクロサービスによって利用される、残りのインフラストラクチャをデプロイします。
> * **ステップ 4: フロントエンドのインフラストラクチャをデプロイする**: フロントエンドのインフラストラクチャ (ペットの写真用の BLOB ストレージ、Cosmos DB、および Vision API) をデプロイします。
> * **ステップ 5: バックエンドを移行する**: マイクロサービスをデプロイして AKS 上で実行し、バックエンドを移行します。
> * **ステップ 6: フロントエンドを公開する**: SmartHotel アプリを、Azure App サービスと、ペット サービスによって呼び出される Function App に公開します。



## <a name="step-1-provision-an-aks-cluster-and-acr"></a>ステップ 1: AKS クラスターと ACR をプロビジョニングする

Contoso は、AKS と Azure Container Registry を使用して、管理対象の Kubernetes クラスターを作成するデプロイ スクリプトを実行します。

- このセクションの手順では、**SmartHotel360-Azure-backend** リポジトリを使用します。
- **SmartHotel360-Azure-backend** GitHub リポジトリには、デプロイのこの部分のためのソフトウェアがすべて含まれています。

プロビジョニングは次のように行います。

1. Contoso は開始する前に、使用している開発用マシンにすべての前提条件ソフトウェアがインストールされていることを確認します。 
2. Git を使用して、開発用マシンにリポジトリをローカルに複製します。

    **git clone https://github.com/Microsoft/SmartHotel360-Azure-backend.git**

3.  Contoso は Visual Studio Code を使用してフォルダーを開き、**/deploy/k8s** ディレクトリに移動します。ここに、スクリプト **gen-aks-env.ps1** が含まれています。
4. AKS と Container Registry を使用して、管理対象の Kubernetes クラスターを作成するスクリプトを実行します。

    ![AKS](./media/contoso-migration-rebuild/aks1.png)
 
5.  ファイルを開いた状態で $location パラメーターを **eastus2** に更新し、ファイルを保存します。

    ![AKS](./media/contoso-migration-rebuild/aks2.png)

6. **[表示]** > **[統合ターミナル]** をクリックして、Code の統合ターミナルを開きます。

    ![AKS](./media/contoso-migration-rebuild/aks3.png)

7. PowerShell 統合ターミナルで、Connect-AzureRmAccount コマンドを使用して Azure にサインインします。 PowerShell の使用の[詳細については、こちら](https://docs.microsoft.com/powershell/azure/get-started-azureps)を参照してください。

    ![AKS](./media/contoso-migration-rebuild/aks4.png)

8. **az login** コマンドを実行し、Web ブラウザーを使用した認証の手順に従って、Azure CLI の認証を行います。 Azure CLI でのログインの[詳細については、こちら](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)を参照してください。

    ![AKS](./media/contoso-migration-rebuild/aks5.png)

9. 次のコマンドを、リソース グループ名 ContosoRG、AKS クラスター名 smarthotel-aks-eus2、および新しいレジストリ名を渡して実行します。

    ```
    .\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2
    ```

    ![AKS](./media/contoso-migration-rebuild/aks6.png)

10. Azure によって、AKS クラスターのリソースを含んだ別のリソース グループが作成されます。

    ![AKS](./media/contoso-migration-rebuild/aks7.png)

11. デプロイが完了したら、Contoso は **kubectl** コマンドライン ツールをインストールします。 このツールは、Azure CloudShell では既にインストールされています。

    **az aks install-cli**

12. **kubectl get nodes** コマンドを実行して、クラスターへの接続を確認します。 ノードは、自動的に作成されたリソース グループ内の VM と同じ名前です。

    ![AKS](./media/contoso-migration-rebuild/aks8.png)

13. 次のコマンドを実行して、Kubernetes ダッシュボードを起動します。 

    **az aks browse --resource-group ContosoRG --name smarthotelakseus2**

14. ブラウザー タブが開き、ダッシュボードが表示されます。 これは Azure CLI を使用したトンネル接続です。 

    ![AKS](./media/contoso-migration-rebuild/aks9.png)




## <a name="step-2-build-a-docker-container"></a>ステップ 2: Docker コンテナーをビルドする

### <a name="create-a-vsts-and-build"></a>VSTS を作成してビルドする

Contoso は VSTS プロジェクトを作成し、CI ビルドを構成してコンテナーを作成し、それを ACR にプッシュします。 このセクションの手順では、[SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) リポジトリを使用します。

1. visualstudio.com から新しいアカウント (**contosodevops360.visualstudio.com**) を作成し、それを Git を使用するように構成します。

2. バージョン コントロールに Git、ワークフローにアジャイルを使用して、新しいプロジェクト (**smarthotelrefactor**) を作成します。

    ![VSTS](./media/contoso-migration-rebuild/vsts1.png) 


3. GitHub リポジトリをインポートします。

    ![VSTS](./media/contoso-migration-rebuild/vsts2.png)
    
4. **[Build and Release]\(ビルドとリリース\)** で、インポートされた **smarthotel** リポジトリから、[VSTS Git] をソースとして使用して新しい定義を作成します。 

    ![VSTS](./media/contoso-migration-rebuild/vsts3.png)

6. 空のパイプラインでの開始を選択します。

    ![VSTS](./media/contoso-migration-rebuild/vsts4.png)  

7. ビルド定義で **[Hosted Linux Preview]\(ホストされている Linux プレビュー\)** を選択します。

    ![VSTS](./media/contoso-migration-rebuild/vsts5.png) 
 
8. **[Phase 1]\(フェーズ 1\)** で、**[Docker Compose]** タスクを追加します。 このタスクは、Docker Compose をビルドします。

    ![VSTS](./media/contoso-migration-rebuild/vsts6.png) 

9. 手順を繰り返し、**Docker Compose** タスクをもう 1 つ追加します。 このタスクは、コンテナーを ACR にプッシュします。

     ![VSTS](./media/contoso-migration-rebuild/vsts7.png) 

8. 1 番目のタスク (ビルドの実行) を選択し、ビルドに Azure サブスクリプション、認可、および ACR を構成します。 

    ![VSTS](./media/contoso-migration-rebuild/vsts8.png)

9. リポジトリの **src** フォルダーにある **docket-compose.yaml** ファイルのパスを指定します。 サービス イメージのビルドを選択し、最新のタグを含めます。 アクションが **[Build service images]\(サービス イメージのビルド\)** に変わると、VSTS タスクの名前が **[Build services automatically]\(サービスを自動的にビルド\)** に変わります。

    ![VSTS](./media/contoso-migration-rebuild/vsts9.png)

10. Contoso は次に、2 番目の Docker タスク (プッシュの実行) を構成します。 サブスクリプションと **[smarthotelacreus2]** ACR を選択します。 

    ![VSTS](./media/contoso-migration-rebuild/vsts10.png)

11. 前回と同様に、ファイルには docker-compose.yaml ファイルを入力し、**[Push service images]\(サービス イメージのプッシュ\)** を選択し、最新のタグを含めます。 アクションが **[Push service images]\(サービス イメージのプッシュ\)** に変わると、VSTS タスクの名前が **[Push services automatically]\(サービスを自動的にプッシュ\)** に変わります。

    ![VSTS](./media/contoso-migration-rebuild/vsts11.png)

12. VSTS タスクが構成されたので、Contoso はビルド定義を保存し、ビルド プロセスを開始します。

    ![VSTS](./media/contoso-migration-rebuild/vsts12.png)

13. ビルド ジョブをクリックして、進行状況を確認します。

    ![VSTS](./media/contoso-migration-rebuild/vsts13.png)

14. ビルドが完了すると、ACR に、マイクロサービスによって使用されるコンテナーが設定された新しいリポジトリが表示されます。

    ![VSTS](./media/contoso-migration-rebuild/vsts14.png)


## <a name="step-3-deploy-back-end-microservices"></a>ステップ 3: バックエンドのマイクロサービスをデプロイする

AKS クラスターを作成し、Docker イメージをビルドしたので、Contoso は次に、バックエンドのマイクロサービスによって利用される残りのインフラストラクチャをデプロイします。

- このセクションの手順では、[SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) リポジトリを使用します。
- **/deploy/k8s/arm** フォルダーに、すべての項目を作成する 1 つのスクリプトが含まれています。 

デプロイは次のように行います。

1. Contoso は deploy.cmd ファイルを使用し、次のコマンドを入力して ContosoRG リソース グループと EUS2 リージョンに Azure リソースをデプロイします。

    **.\deploy azuredeploy ContosoRG -c eastus2**

    ![バックエンドのデプロイ](./media/contoso-migration-rebuild/backend1.png)

2. 後で使用するために、各データベースの接続文字列をキャプチャします。

    ![バックエンドのデプロイ](./media/contoso-migration-rebuild/backend2.png)

## <a name="step-4-deploy-front-end-infrastructure"></a>ステップ 4: フロントエンドのインフラストラクチャをデプロイする

Contoso は、フロントエンド アプリによって使用されるインフラストラクチャをデプロイする必要があります。 ペットの写真を格納するための BLOB ストレージ コンテナー、ペットの情報を含んだドキュメントを格納するための Cosmos データベース、および Web サイト用の Vision API を作成します。 

このセクションの手順では、[SmartHotel-public-web](https://github.com/Microsoft/SmartHotel360-public-web) リポジトリを使用します。

### <a name="create-storage-containers"></a>ストレージ コンテナーを作成する

1.  Azure portal で、Contoso は 作成済みのストレージ アカウントを開き、**[BLOB]** をクリックします。
2.  パブリック アクセス レベル をコンテナーに設定した新しいコンテナー (**Pets**) を作成します。 ユーザーは、ペットの写真をこのコンテナーにアップロードします。

    ![Storage Blob](./media/contoso-migration-rebuild/blob1.png)

3. **settings** という名前の 2 つめのコンテナーを作成します。 このコンテナーに、フロントエンド アプリのすべての設定が含まれるファイルが配置されます。

    ![Storage Blob](./media/contoso-migration-rebuild/blob2.png)

4. 後で参照できるように、ストレージ アカウントのアクセスの詳細をテキスト ファイルにキャプチャします。

    ![Storage Blob](./media/contoso-migration-rebuild/blob2.png)

## <a name="provision-a-cosmos-database"></a>Cosmos データベースをプロビジョニングする

Contoso は、ペットの情報に使用する Cosmos データベースをプロビジョニングします。

1. Azure Marketplace で、**Azure Cosmos DB** を作成します。

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos1.png)

2. 名前 (**contosomarthotel**) を指定し、SQL API を選択し、米国東部 2 メイン リージョンの ContosoRG リソース グループに配置します。

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos2.png)

3. 既定の容量およびスループットを指定した新しいコレクションをデータベースに追加します。

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos3.png)


4. 後で参照できるように、このデータベースの接続情報をメモします。 

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos4.png)


## <a name="provision-computer-vision"></a>Computer Vision をプロビジョニングする

Contoso は、Computer Vision API をプロビジョニングします。 この API は、ユーザーによってアップロードされた写真を評価するために関数によって呼び出されます。

1. Azure Marketplace で、**Computer Vision** インスタンスを作成します。

     ![Computer Vision](./media/contoso-migration-rebuild/vision1.png)

2. ContosoRG 運用リソース グループ、米国東部 2 メイン リージョンで、API (**smarthotelpets**) をプロビジョニングします。

    ![Computer Vision](./media/contoso-migration-rebuild/vision2.png)

3. 後で参照できるように、この API の接続文字列をテキスト ファイルに保存します。

     ![Computer Vision](./media/contoso-migration-rebuild/vision3.png)

## <a name="step-5-deploy-the-back-end-services-in-azure"></a>ステップ 5: Azure でバックエンドのサービスをデプロイする

Contoso は、サービスへの受信トラフィックを許可する NGINX イングレス コントローラーをデプロイし、マイクロサービスを AKS クラスターにデプロイする必要があります。

このセクションの手順では、[SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) リポジトリを使用します。



1. Visual Studio Code を使用して **/deploy/k8s/config_loal.yml** ファイルを更新します。 前にメモした情報に基づいて、さまざまなデータベース接続を更新します。

     ![マイクロサービスのデプロイ](./media/contoso-migration-rebuild/deploy-micro.png)

    > [!NOTE]
    > 一部の構成設定 (Active Directory B2C など) については、この記事では説明しません。 これらの設定についての詳細情報は、リポジトリにあります。

2. deploy.ps1 ファイルは、すべてのクラスター コンテンツ (イングレスおよびイングレス コントローラーを除く) を削除し、マイクロサービスをデプロイします。 これは次のように実行します。

  ```
  .\deploy.ps1 -configFile .\conf_local.yml -dockerUser smarthotelacreus2  -dockerPassword [passwordhere] -registry smarthotelacreus2.azurecr.io -imageTag latest -deployInfrastructure $false -buildImages $false -pushImages $false
  ```

3. 次のコマンドを実行して、サービスの状態を確認します。

    ```
    kubectl get services
    ```
4. Kubernetes ダッシュボードを開いて、デプロイを確認します。

    ```
    az aks browse --resource-group ContosoRG --name smarthotelakseus2
    ```


## <a name="step-6-publish-the-frontend"></a>ステップ 6: フロントエンドを公開する

Contoso は最後のステップとして、SmartHotel アプリを Azure App Service と、ペット サービスによって呼び出される Function App に公開します。

このセクションの手順では、[SmartHotel-public-web](https://github.com/Microsoft/SmartHotel360-public-web) リポジトリを使用します。 リポジトリ

### <a name="set-up-web-app-to-use-contoso-resources"></a>Contoso のリソースを使用するように Web アプリを設定する

1. Contoso は Git を使用して、開発用マシンにリポジトリをローカルに複製します。

    **git clone https://github.com/Microsoft/SmartHotel360-public-web.git**

2. Visual Studio でフォルダーを開いて、リポジトリ内のすべてのファイルを表示します。

    ![フロントエンドの公開](./media/contoso-migration-rebuild/front-publish1.png)

3. 既定の設定に必要な構成変更を加えます。

    - Web アプリは、起動時に **SettingsUrl** アプリ設定を探します。
    - この変数には、構成ファイルへの URL が含まれている必要があります。
    - 既定では、使用される設定はパブリック エンドポイントです。

4. **/config-sample.json/sample.json** ファイルを更新します。 これは、パブリック エンドポイントを使用する場合の Web 用の構成ファイルです。

    - **urls** セクションと **pets_config** セクションの両方を、AKS API エンドポイント、ストレージ アカウント、Cosmos データベースの値を使用して編集します。 
    - URL は、Contoso が作成する新しい Web アプリの DNS 名と一致している必要があります。
    - Contoso の場合、これは **smarthotelcontoso.eastus2.cloudapp.azure.com** です。

    ![フロントエンドの公開](./media/contoso-migration-rebuild/front-publish2.png)

5. ファイルが更新されたら、名前を **smarthotelsettingsurl** に変更し、前に作成したストレージ BLOB にアップロードします。

     ![フロントエンドの公開](./media/contoso-migration-rebuild/front-publish3.png)

6. ファイルをクリックして、URL を取得します。 この URL は、アプリが構成ファイルのプル ダウンを開始するときに使用されます。

    ![フロントエンドの公開](./media/contoso-migration-rebuild/front-publish4.png)

7. **appsettings.Production.json** ファイル内の **SettingsUrl** を、新しいファイルの URL に更新します。

    ![フロントエンドの公開](./media/contoso-migration-rebuild/front-publish5.png)


### <a name="deploy-the-website-to-the-azure-app-service"></a>Azure App Service に Web サイトをデプロイする

これで、Contoso は Web サイトを公開できます。


1. Visual Studio 2017 で、Application Insights の監視を有効にします。 これを行うには、ソリューション エクスプローラーで **PublicWeb** プロジェクトを選択し、**[Application Insights の追加]** を探します。 インフラストラクチャのデプロイ時に作成された Application Insight インスタンスにアプリを登録します。

    ![Web サイトの公開](./media/contoso-migration-rebuild/deploy-website1.png)

2. Visual Studio で、PublicWeb プロジェクトを App Insights に接続し、構成済みと表示されるように更新します。
 
    ![Web サイトの公開](./media/contoso-migration-rebuild/deploy-website2.png)

3. Visual Studio で、Web アプリを作成して公開します。

    ![Web サイトの公開](./media/contoso-migration-rebuild/deploy-website3.png)

5. アプリ名を指定し、**ContosoRG** 運用リソース グループ、米国東部 2 メイン リージョンに配置します。

    ![Web サイトの公開](./media/contoso-migration-rebuild/deploy-website4.png)

### <a name="deploy-the-function-to-azure"></a>関数を Azure にデプロイする

1. Visual Studio を使用して、関数を作成して公開します。 これを行うには、**[PetCheckerFunction]** > **[公開]** を右クリックします。 次に、新しい App Service 関数を作成します。

     ![関数のデプロイ](./media/contoso-migration-rebuild/function1.png)

2. 名前 **smarthotelpetchecker** を指定し、ContosoRG リソース グループ、新しい App Service プランに配置します。

     ![関数のデプロイ](./media/contoso-migration-rebuild/function2.png)

3. ストレージ アカウントを選択し、関数を作成します。

    ![関数のデプロイ](./media/contoso-migration-rebuild/function3.png)

4. デプロイは Azure への関数アプリのプロビジョニングから開始します。 **[公開]** で、Contoso はストレージ、Cosmos データベース、および Computer Vision API 用のアプリ設定を追加します。

    ![関数のデプロイ](./media/contoso-migration-rebuild/function4.png)

5. 関数をまずローカルで実行するために、**PetCheckerFunction/local.settings.json** の設定を更新します。

    ![関数のデプロイ](./media/contoso-migration-rebuild/function5.png)

6. 関数はデプロイされた後、Azure portal に **[実行中]** 状態で表示されます。

    ![関数のデプロイ](./media/contoso-migration-rebuild/function6.png)


7. Pet Checker AI が期待どおりに動作していることを確認するために、アプリを参照します ([http://smarthotel360public.azurewebsites.net/Pets](http://smarthotel360public.azurewebsites.net/Pets))。
8. アバターをクリックして、写真をアップロードします。

    ![関数のデプロイ](./media/contoso-migration-rebuild/function7.png)

9. 最初に確認したい写真は、小さい犬の写真です。

    ![関数のデプロイ](./media/contoso-migration-rebuild/function8.png)

10. アプリが受領のメッセージを返します。

    ![関数のデプロイ](./media/contoso-migration-rebuild/function9.png)




## <a name="review-the-deployment"></a>デプロイを再調査する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティ保護する必要があります。

### <a name="security"></a>セキュリティ

- Contoso は新しいデータベースが安全であることを確認する必要があります。 [詳細情報](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)。
- アプリは、SSL と証明書を使用するように更新する必要があります。 コンテナー インスタンスは 443 で応答するように再デプロイする必要があります。
- KeyVault を使用して、Service Fabric アプリのシークレットを保護することを検討する必要があります。 [詳細情報](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management)。

### <a name="backups-and-disaster-recovery"></a>バックアップとディザスター リカバリー

- Contoso は、Azure SQL Database のバックアップ要件を確認する必要があります。 [詳細情報](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。
- データベースのリージョン内フェールオーバーを提供するように SQL フェールオーバー グループを実装することを検討する必要があります。 [詳細情報](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)。
- ACR Premium SKU の geo レプリケーションを利用できます。 [詳細情報](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- すべてのリソースをデプロイした後、Contoso は[インフラストラクチャ計画](contoso-migration-infrastructure.md#set-up-tagging)に基づいて Azure タグを割り当てる必要があります。
- すべてのライセンスは、Contoso が使用している PaaS サービスのコストに組み込まれています。 これは EA から差し引かれます。
- Contoso は、Microsoft の子会社である Cloudyn からライセンスが供与される Azure Cost Management を有効にします。 これは、Azure やその他のクラウド リソースの利用や管理に役立つ、マルチクラウド対応のコスト管理ソリューションです。  Azure Cost Management の詳細については、[こちら](https://docs.microsoft.com/azure/cost-management/overview)を参照してください。

## <a name="conclusion"></a>まとめ

この記事で、Contoso は SmartHotel アプリを Azure にリビルドしました。 オンプレミス アプリのフロントエンド VM を Azure App Services の Web アプリに移行しました。 アプリのバックエンドを、Azure Kubernetes Service (AKS) によって管理されるコンテナーにデプロイされたマイクロサービスを使用してビルドしました。 アプリの機能を、ペットの写真アプリで強化しました。





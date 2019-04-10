---
title: Azure Stack とは | Microsoft Docs
description: Azure Stack を使用してお使いのデータセンターで Azure のサービスを実行する方法を説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: e5e05ad4f2ae7e718e160916144f03bfb74a2a52
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631264"
---
# <a name="azure-stack-overview"></a>Azure Stack の概要

Azure Stack は、オンプレミス環境でアプリケーションを実行し、データセンターで Azure サービスを実現する方法を提供する Azure の拡張機能です。 一貫性のあるクラウド プラットフォームにより、組織は、テクノロジの制約に基づいてビジネスを決定するのではなく、ビジネスの要件に基づいて自信を持ってテクノロジを決定できます。

## <a name="why-use-azure-stack"></a>Azure Stack を使用する理由

Azure によって、開発者が最新アプリケーションを構築するための機能豊富なプラットフォームが提供されます。 ただし、一部のクラウドベースのアプリケーションは、待機時間、間欠的な接続、規制などの障害に直面します。 Azure と Azure Stack によって、顧客向けアプリケーションと社内の基幹業務アプリケーションのどちらにも対応する、新しいハイブリッド クラウドのユース ケースが実現されます。

- **エッジおよび非接続ソリューション**。 Azure Stack でローカルで処理したデータを Azure で集計したあと、さらに詳しく分析し、Azure Stack と Azure の両方で共通するアプリケーションのロジックを使用して、待機時間や接続性の要件に対応できます。 インターネットから切断された状態で、Azure に接続せずに Azure Stack を展開することもできます。 例としては 工場、クルーズ船、立坑などがあります。

- **さまざまな規制に対応するクラウド アプリケーション**。 Azure でアプリケーションの開発とデプロイを行えば、コードを変更せずに、規制やポリシーの要件を満たすために Azure Stack でオンプレミスに展開する完全な柔軟性が得られます。 アプリケーションの例としては、グローバルな監査、財務報告書の作成、外国為替取引、オンライン ゲーム、経費報告書の作成が挙げられます。

- **オンプレミスのクラウド アプリケーション モデル**。 Azure のサービス、コンテナー、サーバーレス、マイクロサービスの各アーキテクチャを使用して、既存のアプリケーションの更新と拡張を行うか、新しいアプリケーションを構築することができます。 クラウドの Azure と Azure Stack オンプレミスで一貫性のある DevOps プロセスを使用し、コア ミッションクリティカル アプリケーションのアプリケーション最新化を迅速に行います。

Azure Stack によって以下の使用シナリオが実現します。

- 統合された提供エクスペリエンス。信頼できるハードウェア パートナーから提供される特定用途向けの Azure Stack 統合システムを使用すれば、ただちにサービスを提供できます。 Azure Stack は納入された後、System Center Operations Manager 管理パックまたは Nagios 拡張機能によって監視しながら、容易にデータセンターに統合できます。

- 柔軟性の高い ID 管理は、Azure および Azure Stack ハイブリッド環境向けの Azure Active Directory (Azure AD) を使用し、切断された展開では Active Directory Federation Services (AD FS) を利用します。 

- Azure の一貫性があるアプリケーション開発環境は、開発者の生産性を最大限に高め、一般的な DevOps アプローチをハイブリッド環境全体で実現します。

- ハイブリッド クラウド コンピューティング能力を使用したオンプレミスからの Azure サービス提供。 Azure と Azure Stack で共通の操作プラクティスを採用し、Azure と同じ管理エクスペリエンスとツールを使用して、Azure IaaS および PaaS サービスを展開して操作します。 マイクロソフトは、新しい Azure サービス、既存のサービスの更新プログラム、Azure Marketplace へのアプリケーションやイメージの追加など、Azure のイノベーションを絶え間なく Azure Stack にもたらしています。

## <a name="azure-stack-architecture"></a>Azure Stack のアーキテクチャ
Azure Stack 統合システムは、信頼できるパートナーによって構築された 4 - 16 のサーバー ラックで構成され、データセンターに直接納入されます。 納入後は、ソリューション プロバイダーによって統合システムが展開され、Azure Stack ソリューションがビジネス要件に合っていることが確認されます。 必要なすべての電源や冷却装置、境界接続、およびその他の必要なデータセンター統合要件が整っていることを確認してデータセンターを準備する必要があります。 

> Azure Stack とデータ センターの統合エクスペリエンスの詳細については、「[Azure Stack とデータセンターの統合](azure-stack-customer-journey.md)」を参照してください。

Azure Stack は、業界標準のハードウェアに構築され、Azure サブスクリプションの管理に既に使用しているのと同じツールを使用して管理されます。 その結果、Azure に接続しているかどうかに関係なく、一貫した DevOps プロセスを適用できます。 

Azure Stack アーキテクチャにより、インターネットから切断された遠隔地や接続が間欠的な場合など、エッジで Azure サービスを提供できるようになります。 データを Azure Stack でローカルに処理してから、追加の処理や分析のために Azure に集約するハイブリッド ソリューションを作成できます。 さらに、Azure Stack はオンプレミスにインストールされるため、コードを変更せずにクラウド アプリケーションをオンプレミスに柔軟に展開でき、特定の制約またはポリシー要件を満たすことができます。 

## <a name="deployment-options"></a>デプロイ オプション

### <a name="production-or-evaluation-environments"></a>運用環境または評価環境
Azure Stack には、ニーズに合わせて 2 つの展開オプションがあります。運用のための Azure Stack 統合システムと Azure Stack を評価するための Azure Stack Development Kit (ASDK) です。

- **Azure Stack 統合システム**。 Azure Stack 統合システムはマイクロソフトとハードウェア パートナーのパートナーシップによって提供され、クラウドと歩調を合わせた革新と、コンピューティングの管理のしやすさを両立させたソリューションを実現します。 Azure Stack は統合されたハードウェアおよび ソフトウェア システムとして提供されているため、必要な柔軟性やコントロールが得られるほか、クラウドから革新を図ることができます。 Azure Stack 統合システムには 4 ノードから 16 ノードまでのサイズがあり、ハードウェア パートナーと Microsoft によって共同でサポートされます。 Azure Stack 統合システムの使用により、運用ワークロードに向けて、新しいシナリオを作成し、新しいソリューションをデプロイできます。

- **Azure Stack Development Kit**。 [Azure Stack Development Kit (ASDK)](./asdk/asdk-what-is.md) は、Azure Stack の評価と学習に使用できる Azure Stack の無料単一ノード展開です。 ASDK を開発環境として使用し、Azure と一貫性のある API やツールを使用してアプリをビルドできます。 ただし、ASDK は運用環境で使用するためのものではありません。完全な統合システム運用展開と比べて次の制限があります。

    - ASDK は、単一の Azure Active Directory (Azure AD) または Active Directory フェデレーション サービス (AD FS) ID プロバイダーにしか関連付けることができません。
    - Azure Stack のコンポーネントは単一のホスト コンピューターにデプロイされるため、テナントのリソースに対して使用できる物理リソースは限られています。 この構成は、拡張性やパフォーマンスの評価のためのものではありません。
    - 単一のホストと NIC のデプロイ要件により、ネットワークのシナリオは限られています。

### <a name="connection-models"></a>接続モデル
Azure Stack をインターネット (および Azure) に**接続した状態**で展開するか、**接続していない状態**で展開するかを選択できます。 この選択により、ID ストア (Azure AD または AD FS) と課金モデル (従量制課金すなわち容量に基づいた課金) の使用可能なオプションが定義されます。

> 詳しくは、[接続されている](azure-stack-connected-deployment.md)展開モデルと[接続されていない](azure-stack-disconnected-deployment.md)展開モデルの考慮事項をご覧ください。 

### <a name="identity-provider"></a>ID プロバイダー 
Azure Stack は、ID プロバイダーとして Azure Active Directory (Azure AD) または Active Directory フェデレーション サービス (AD FS) のいずれかを使用して、Azure Stack ID を設定します。 

> [!IMPORTANT]
> これは重要な意思決定のポイントです。 ID プロバイダーとして Azure AD または AD FS を選択するのは、展開時に 1 回だけ行う決定です。 システム全体を再デプロイせずに、後でこれを変更することはできません。

Azure AD は、マイクロソフトが提供するクラウドベースのマルチテナント対応 ID プロバイダーです。 展開がインターネットに接続されているほとんどのハイブリッド シナリオでは、ID ストアとして Azure AD を使用します。 ただし、Azure Stack のオフラインでの展開には Active Directory フェデレーション サービス (AD FS) を使用することを選択できます。 Azure Stack、リソース プロバイダー、およびその他のアプリケーションは、Azure AD を使用するのとほぼ同じ方法で AD FS を使用します。 Azure Stack には、独自の Active Directory インスタンスと、Active Directory Graph API が含まれています。 

> Azure Stack の ID の考慮事項について詳しくは、「[Azure Stack の ID の概要](azure-stack-identity-overview.md)」をご覧ください。

## <a name="how-is-azure-stack-managed"></a>Azure Stack の管理方法
Azure Stack を統合システム展開または ASDK インストールのいずれかに展開した後で、Azure Stack を操作する主な方法は管理ポータル、ユーザー・ポータル、および PowerShell です。 Azure Stack のポータルは、それぞれが Azure Resource Manager の個別のインスタンスでサポートされています。 **Azure Stack オペレーター**は、管理ポータルを使用して Azure Stack を管理し、テナント オファーリングの作成などの操作を行い、統合システムの正常性の維持や状態の監視を行います。 ユーザー ポータル (テナント ポータルとも呼ばれます) は、仮想マシンやストレージ アカウント、Web アプリなどのクラウド リソースを消費するためのセルフ サービス エクスペリエンスを提供します。 

> 管理ポータルを使用した Azure Stack の管理について詳しくは、[Azure Stack 管理ポータルのクイック スタート](azure-stack-manage-portals.md)のページをご覧ください。

Azure Stack オペレーターは、[仮想マシン](azure-stack-tutorial-tenant-vm.md)、[Web アプリケーション](azure-stack-app-service-overview.md)、高可用性 [SQL Server](azure-stack-tutorial-sql.md)、[MySQL Server](azure-stack-tutorial-mysql.md) データベースなど、多岐にわたるサービスとアプリケーションを提供できます。 また、[Azure Stack クイックスタートの Azure Resource Manager テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates)を使用して、SharePoint や Exchange などを展開できます。 

管理ポータルを使用すると、プラン、クォータ、オファー、およびサブスクリプションを使用してテナントに[サービスを提供するように Azure Stack を構成](azure-stack-plan-offer-quota-overview.md)できます。 テナント ユーザーは複数のオファーにサブスクライブできます。 オファーは 1 つまたは複数のプランを含むことができ、プランは 1 つまたは複数のサービスを含むことができます。 さらに、オペレーターは、容量を管理し、アラートに対処します。 

Azure Stack が構成されると、Azure Stack **ユーザー** (テナントとも呼ばれます) は、オペレーターが提供するサービスを使用します。 ユーザーは、サブスクライブしたサービス (Web アプリ、Storage、Virtual Machines など) のプロビジョニング、監視、管理を行うことができます。

> どこでどのアカウントを使用するか、通常のオペレーターの役割、ユーザーに何を伝えるか、ヘルプを利用する方法など、Azure Stack の管理について詳しくは、「[Azure Stack の管理の基本](azure-stack-manage-basics.md)」をご覧ください。

## <a name="resource-providers"></a>リソース プロバイダー 
リソース プロバイダーは、Azure Stack のあらゆる IaaS サービスと PaaS サービスの基盤となる Web サービスです。 Azure Resource Manager は、さまざまなリソース プロバイダーに依存して、サービスへのアクセスを提供しています。 各リソース プロバイダーを使用して、それぞれのリソースを構成および制御できます。 サービス管理者は、新しいカスタム リソース プロバイダーを追加することもできます。 

### <a name="foundational-resource-providers"></a>基本のリソース プロバイダー 
基本の IaaS リソース プロバイダーは 3 つあります。コンピューティング、ネットワーク、およびストレージです。

- **コンピューティング**。 コンピューティング リソース プロバイダーでは、Azure Stack テナントが独自の仮想マシンを作成できます。 コンピューティング リソース プロバイダーには、仮想マシンと仮想マシン拡張機能を作成する機能があります。 仮想マシン拡張機能サービスは、Windows および Linux の仮想マシンで使用する IaaS 機能を提供します。  たとえば、コンピューティング リソース プロバイダーを使用して Linux 仮想マシンをプロビジョニングし、展開中に Bash スクリプトを実行して VM を構成できます。
- **ネットワーク リソース プロバイダー**。 ネットワーク リソース プロバイダーは、プライベート クラウドでソフトウェアによるネットワーク制御 (SDN) およびネットワーク機能の仮想化 (NFV) に使用する一連の機能を提供します。 ネットワーク リソース プロバイダーを使用して、ソフトウェア ロード バランサー、パブリック IP、ネットワーク セキュリティ グループ、仮想ネットワークなどのリソースを作成できます。
- **ストレージ リソース プロバイダー**。 ストレージ リソース プロバイダーによって、一貫性のある 4 つの Azure ストレージ サービス ([blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage)、[queue](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage)、[table](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage)、および KeyVault アカウント管理) が実現し、管理機能とシークレット (パスワードや証明書など) の監査機能が提供されます。 ストレージ リソース プロバイダーは、Azure との一貫性があるストレージ サービスのサービス プロバイダーによる管理を容易にするためのストレージ クラウド管理サービスも提供します。 Azure Storage には柔軟性があり、ドキュメントやメディア ファイルなどの大量の構造化されていないデータは Azure Blobs で、構造化された NoSQL ベースのデータは Azure Tables で格納と取得が行われます。 

### <a name="optional-resource-providers"></a>オプションのリソース プロバイダー
Azure Stack に展開して使用できるオプションの PaaS リソース プロバイダーは 3 つあります。App Service、SQL Server、および MySQL サーバー リソース プロバイダーです。

- **App Service**。 [Azure App Service on Azure Stack](azure-stack-app-service-overview.md) は、Azure Stack で利用できる Microsoft Azure の、サービスとしてのプラットフォーム (PaaS) サービスです。 このサービスにより、内部または外部の顧客はあらゆるプラットフォームやデバイス用の Web、API、Azure Functions アプリケーションを作成できるようになります。 
- **SQL Server**。 [SQL Server リソース プロバイダー](azure-stack-sql-resource-provider.md)を使用して、SQL データベースを Azure Stack のサービスとして提供します。 リソース プロバイダーをインストールし、それを 1 つ以上の SQL Server インスタンスに接続した後で、クラウドネイティブ アプリ、SQL を使用する Web サイト、SQL を使用する他のワークロードのためにデータベースを作成できます。
- **MySQL サーバー**。 [MySQL サーバー リソース プロバイダー](azure-stack-mysql-resource-provider-deploy.md)を使用して、MySQL データベースを Azure Stack サービスとして公開します。 MySQL リソース プロバイダーは、Windows Server 2016 Server Core 仮想マシン (VM) 上でサービスとして実行されます。

## <a name="providing-high-availability"></a>高可用性の提供
Azure でのマルチ VM による実稼働システムの高可用性を実現するため、VM は、複数の障害ドメインと更新ドメインに分散される[可用性セット](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)に配置されます。 より小さいスケールの Azure Stack では、可用性セット内の障害ドメインは、スケール ユニット内の 1 つのノードとして定義されます。  

Azure Stack のインフラストラクチャは既に障害に対する回復性を備えていますが、基盤となっているテクノロジ (フェールオーバー クラスタリング) では、ハードウェアが故障した場合にその影響を受ける物理サーバー上の VM に多少のダウンタイムが発生します。 Azure Stack では、Azure との一貫性がある最大 3 つの障害ドメインを持つ可用性セットを用意することをサポートしています。

- **障害ドメイン**。 可用性セットに配置された VM は、複数の障害ドメイン (Azure Stack ノード) にできる限り均等に分散させることによって、互いに物理的に分離されます。 ハードウェアが故障した場合、障害が発生した障害ドメインの VM は他の障害ドメインで再起動されますが、可能であれば、同じ可用性セット内の他の VM とは別の障害ドメインに保持されます。 ハードウェアがオンラインに戻ると、高可用性を維持するために VM の再分配が行われます。 
 
- **更新ドメイン**。 更新ドメインは、可用性セットに高可用性を提供する Azure の別の概念です。 更新ドメインは、メンテナンスを同時に実行できる、基盤となるハードウェアの論理グループです。 同じ更新ドメイン内の VM は、計画済みメンテナンス中に同時に再起動されます。 テナントが可用性セット内に VM を作成すると、Azure プラットフォームは、これらの更新ドメインに VM を自動的に分散します。 Azure Stack では、VM のホストが更新される前に、クラスター内の他のオンライン ホストに VM がライブで移行されます。 ホスト更新の際にテナントのダウンタイムは発生しないため、Azure Stack の更新ドメイン機能は、Azure とテンプレートの互換性を保つためにのみ存在します。 

## <a name="role-based-access-control"></a>ロール ベースのアクセス制御
ロール ベースのアクセス制御 (RBAC) を使用して、承認されたユーザー、グループ、およびサービスに対してサブスクリプション、リソース グループ、または個々のリソース レベルでロールを割り当てることによって、システムへのアクセスを許可することができます。 各ロールは、Microsoft Azure Stack のリソースに対するユーザー、グループ、またはサービスのアクセス レベルを定義します。

Azure Stack RBAC には、すべてのリソースの種類に適用される 3 つの基本的なロールがあります。所有者、共同作成者、および閲覧者です。 所有者は、他のユーザーへアクセス権を委任する権限を含め、すべてのリソースへのフル アクセス権を持ちます。 作成協力者は、Azure リソースのすべてのタイプを作成および管理できますが、他のユーザーへアクセス権を付与することはできません。 閲覧者は、既存のリソースの表示しかできません。 残りの RBAC ロールでは、特定の Azure リソースの管理が許可されます。 たとえば、仮想マシンの作成協力者ロールでは、仮想マシンの作成と管理は許可されますが、仮想マシンが接続する仮想ネットワークまたはサブネットの管理は許可されません。

> 詳しくは、「[ロールベースのアクセス制御の管理](azure-stack-manage-permissions.md)」をご覧ください。 

## <a name="reporting-usage-data"></a>使用量データの報告
Microsoft Azure Stack は、すべてのリソース プロバイダーの使用状況データを収集して集約し、Azure コマースによる処理のために Azure に送信します。 Azure Stack で収集された使用状況データは、REST API を使用して表示できます。 Azure と一貫性のある Tenant API だけでなく、Provider および Delegated Provider API を使用して、すべてのテナント サブスクリプション全体の使用状況データを取得できます。 このデータは、請求や配賦のために外部のツールやサービスと統合できます。 Azure コマースによって処理された使用状況は、Azure の課金ポータルに表示できます。

> 詳しくは、「[Azure Stack 使用状況データの Azure への報告](azure-stack-usage-reporting.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

[Azure Stack とグローバル Azure の比較](compare-azure-azure-stack.md)

[管理の基本](azure-stack-manage-basics.md)

[クイック スタート: Azure Stack 管理ポータルを使用する](azure-stack-manage-portals.md)
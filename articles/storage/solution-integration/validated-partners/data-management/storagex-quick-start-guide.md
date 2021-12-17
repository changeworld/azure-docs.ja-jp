---
title: Data Dynamics StorageX によるファイル データの分析と Azure への移行
titleSuffix: Azure Storage
description: Data Dynamics StorageX を導入するためのスタートガイドです。 ガイドでは、データを Azure Files、Azure NetApp Files、Azure Blob Storage、または利用可能な ISV の NAS ソリューションに移行する方法を説明します
author: dukicn
ms.author: nikoduki
ms.date: 06/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 4b3f85c06c8dccd5873556c8c1ebfe1f0087b11e
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407610"
---
# <a name="migrate-data-to-azure-with-data-dynamics-storagex"></a>Data Dynamics StorageX を使用して Azure にデータを移行する

このアーティクルは、Data Dynamics StorageX を Microsoft Azure にデプロイする場合に役立ちます。 StorageX のしくみ、デプロイの前提条件、インストール手順、運用の手引きとなるハウツーなど、重要な概念を説明します。 詳細については[Data Dynamics カスタマー ポータル](https://www.datdynsupport.com/)を参照してください。

Data Dynamics StorageX は、異種混合のストレージ環境において、データの分析、管理、移動を可能にする統合非構造化データ管理プラットフォームです。 主な機能は次のとおりです。
- データ移動機能
    - NAS から NAS (分析の使用または使用なし)
    - NAS からオブジェクトへのレプリケーション
    - NAS からオブジェクトへのアーカイブ
- 重複ファイルの検査レポート
- オープン シェア レポート
- カスタム タグ付けを使用したファイル メタデータ分析
  
StorageX の詳細な機能や、類似ツールとの比較は、[比較表](./migration-tools-comparison.md)を参照してください。

このドキュメントでは、次の 3 つの主要なトピックについて説明します。
- Azure 環境に StorageX オファリングをデプロイするプロセス、
- StorageX にファイルおよびクラウド ストレージ リソースを追加するための基本的な手順、
- データ移動ポリシーの作成。

## <a name="reference-architecture"></a>参照アーキテクチャ

次の図は、StorageX をオンプレミスから Azure、Azure 内にデプロイする場合の参照アーキテクチャを示しています。

:::image type="content" source="./media/storagex-quick-start-guide/storagex-architecture.png" alt-text="StorageX 実装の参照アーキテクチャを示す図":::

## <a name="storagex-interoperability-matrix"></a>StorageX の相互運用性マトリックス

| 機能 | SMB | NFS | メモ |
| ------- |--- | --- | --- |
| **移行** | SMB から SMB | NFSv3 から NFSv3 | マルチプロトコルに対して組み合わせ可能 |
| **ファイルからオブジェクトへのアーカイブ** | SMB から Azure Blob Storage | NFSv3 から Azure Blob Storage | コストを最適化するためにデータをアーカイブする |
| **ファイルからオブジェクトへのレプリケーション** | SMB から Azure Blob Storage | NFSv3 から Azure Blob Storage | データをレプリケートして保護を強化する |

## <a name="before-you-begin"></a>開始する前に

前もって計画することで、移行を容易にし、リスクを減らすことができます。 移行を容易にするためのヒントの一部を次に示します。

- ソース サービスとターゲット サービスに関するデータを収集し、
- 接続ポイントのリストと必要なネットワーク ポートをすべて確認してください。
- Azure の StorageX サーバーは、Active Directory インフラストラクチャの一部である必要があります。 Active Directory にサーバーを追加するには、Active Directory 特権を持つ管理者に問い合わせてください。
- より簡単に導入するには [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=Overview) をご利用ください。

### <a name="get-started-with-azure"></a>Azure を使用して作業を開始する

Microsoft では、Azure の使用を開始する際に従うフレームワークを提供しています。 [クラウド導入フレームワーク](/azure/architecture/cloud-adoption/) (CAF) は、エンタープライズ デジタル変革に向けた詳細なアプローチであり、運用レベルのクラウド導入を計画するための包括的なガイドです。 CAF には、迅速かつ安全に運用するのに役立つステップバイステップの [Azure セットアップガイド](/azure/cloud-adoption-framework/ready/azure-setup-guide/)が含まれています。 対話型バージョンは [Azure portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)で見つけることができます。 アプリケーションをデプロイするためのサンプル アーキテクチャ、具体的なベスト プラクティス、および Azure の専門知識を活用できる無料のトレーニング リソースが記載されています。

### <a name="considerations-for-migrations"></a>移行に関する考慮事項

Azure へのファイル データの移行を検討する際には、いくつかの点が重要になります。 続行する前に、以下の詳細を確認してください。

- [Storage の移行の概要](../../../common/storage-migration-overview.md)
- [移行ツール比較表](./migration-tools-comparison.md)では、Data Dynamics StorageX の最新の対応機能を紹介しています。

運用アプリケーションに影響を与えることなく、移行をサポートするには、十分なネットワーク容量が必要になることに注意してください。 このセクションでは、ネットワークのニーズを評価するために使用できるツールと手法について概説します。

#### <a name="determine-unutilized-internet-bandwidth"></a>未使用のインターネット帯域幅を確認する

日常的に利用可能な通常は未使用の帯域幅 (つまり *ヘッドルーム*) がどれだけあるかを把握しておくことが重要です。 以下に関する目標を達成できるかどうかを評価するのに役立ちます。

- オフライン方式の Azure Data Box を使用しない場合の初期の移行時間
- 目的のファイル サービスに最終的に切り替わるまでの増分再同期に要する時間

以下の方法を使用して、無料で利用できる Azure への帯域幅を特定します。

- 既存の Azure ExpressRoute を使用している場合は、Azure portal で[回線使用量](../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics)を確認します。
- 既存の日単位および月単位の使用状況を表示するには、ISP にレポートの取得をお問い合わせください。
- ルーターまたはスイッチ レベルでネットワーク トラフィックを監視することで、使用率を測定できるツールがいくつかあります。
  - [Solarwinds Bandwidth Analyzer パック](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco ネットワーク アシスタント](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

#### <a name="creating-a-storagex-service-account"></a>StorageX サービス アカウントの作成

SMB 移行の場合は、StorageX サービスを実行するサービス アカウントを推奨します。 サービス アカウントは、サポート上の理由でアクセスを追跡できるようにする一方で、フル アクセスを許可するための適切なアクセス許可を与えるものです。

StorageX ユニバーサル データ エンジンでデータ移動ポリシーを実行するプロセスは、StorageX サービス アカウントを使用します。 アカウントには、Active Directory ドメインで必要なすべての特権が割り当てられている必要があります。

環境で許可されている場合は、ドメイン管理者のようなアカウントを作成できます。 推奨される方法は、NAS サーバーで直接アクセス許可を付与する方法です。 後者を行うには、サービス アカウントを管理者および Backup オペレーター グループに配置します。

StorageX サービス アカウントは次の特権が必要です。

- SeServiceLogonRight
- SeSecurityPrivilege 
- SeBackupPrivilege 
- SeRestorePrivilege 
- SeAuditPrivilege
- SeInteractiveLogonRight

NFS アクセスには、StorageX サーバーの IP に対するエクスポート ルールと、RW と root に対するアクセス許可が必要です。 たとえば、「root=10.2.3.12、rw=10.2.3.12」。ここで、10.2.3.12 は StorageX サーバーの IP アドレスです。

## <a name="deployment-guide"></a>デプロイ ガイド

このセクションでは、StorageX サーバー、および Azure 環境内の他の StorageX コンポーネントをデプロイする方法に関するガイダンスを提供します。

実装を開始する前に、いくつかの前提条件が重要です。

- ターゲット ストレージ サービスを識別して作成する必要があります。
- ストレージ サービスにアクセスするための適切なポートが開いている必要があります。 Microsoft の[portqryui](https://www.microsoft.com/download/details.aspx?id=24009) ツールを使用して、必要なポートが開いていることを確認ができます。

詳細については、次の記事を参照してください。

- [Azure ファイル共有](../../../files/storage-how-to-create-file-share.md)を作成する方法
- Azure NetApp Files で [SMB ボリューム](../../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)または [NFS エクスポート](../../../../azure-netapp-files/azure-netapp-files-create-volumes.md)を作成する方法

すべての SMB 移行では、リソースを追加する前に、Active Directory が適切に設定されている必要があります。 移行先では、既存の Azure NetApp Files の実装に新しいボリュームを使用する予定です。 新しい Azure NetApp Files ボリュームを作成する前に、次を行う必要があります。

- [Azure NetApp Files アカウントを作成する](../../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal#create-a-netapp-account)
- [容量プールの作成](../../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal#create-a-capacity-pool)
- [Active Directory ドメインに Azure NetApp Files アカウントを参加させる](../../../../azure-netapp-files/create-active-directory-connections.md)

Azure NetApp Files の構成が完了したら、移行に使用する共有を作成します。 

1. [既存の Azure NetApp Files アカウント] に移動し、 **[Volumes (ボリューム)]** を選択します。 **[Add volume (ボリュームの追加)]** を選択します。 名前を付け、[capacity pool (容量プール)] と [networking setup (ネットワークの設定)] を選択します。

    :::image type="content" source="./media/storagex-quick-start-guide/anf-create-volume-1.png" alt-text="新しい Azure NetApp Files ボリュームを追加するためのスクリーンショット":::

1. **SMB** プロトコルを選択し、既存の Active Directory 接続を選択して、 **[共有名]** を指定します。

    :::image type="content" source="./media/storagex-quick-start-guide/anf-create-volume-2.png" alt-text="Azure NetApp Files ボリュームの SMB プロパティのスクリーンショット":::

1. **タグ** を使用する場合は追加し、共有を **作成** します。

    ボリュームが作成されたら、StorageX のデプロイを続行できます。

1.  Data Dynamics データ移動とモビリティ オファリングを Azure にデプロイします。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=Overview) の使用を推奨します。 デプロイされたサーバーを Active Directory ドメインに追加します。
1.  デプロイされたサーバーで、必要なすべてのネットワーク ポートが開いていることを確認します。
1.  StorageX インストール スクリプトを実行します。 インストールスクリプトは、Azure のデプロイ済みサーバーにログインとデスクトップで利用できます。 
    すべてのインストール ファイルと出力ログは、**C:\ProgramData\data Dynamics\StorageX** フォルダーにあります。 そのフォルダ内の **InstallationFiles** サブフォルダには zip 圧縮されたソースのインストール ファイルが、**SilentInstaller-DD** フォルダには構成 XML ファイルとインストール ログが入っています。

    既定では、インストール スクリプトによって次のサービスがデプロイされます。

    - StorageX
    - StorageX API
    - StorageX 処理エンジン
    - StorageX ユニバーサル データ エンジン
    - StorageX ファイルの回復
    - Elasticsearch
    - Microsoft SQL Express
    - Microsoft SQL Management Studio
    - Kibana
    - すべてのサポート ソフトウェア (Java など)

    インストール スクリプトには、これらのさまざまなサービスをカスタマイズできる XML 構成ファイルがありますが、手動で構成を変更することは推奨しません。

    インストールするには、デスクトップ上の **StorageXPOCSilentInstaller - ショートカット** を右クリックし、 **[管理者として実行]** を選択します。 インストールが開始されると、StorageX サービス アカウントとそのアカウントのパスワードの入力が求められます。

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-cli-1.jpg" alt-text="インストール CLI のスクリーンショット":::

    インストール スクリプトは、すべてのサービスのインストールを続行します。 インストール プロセスが完了すると、コマンドライン インターフェイスにメッセージが表示されます。

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-cli-2.jpg" alt-text="インストールが完了したことを示すスクリーンショット":::

    StorageX は自動的に開始されます。

## <a name="migration-guide"></a>移行ガイド

インストールが完了し、すべてのサービスが開始されたら、移行プロセスを開始できます。

1.  StorageX コンソールを開きます。
1.  **[Storage Resources (ストレージ リソース)]** タブをクリックします。
1.  **[My Resources (マイ リソース)]** ビューで、NAS デバイスまたはストレージ サービス (StorageX では **ストレージ リソース** と呼びます) を追加します。 さまざまな種類を追加できます。
    - オンプレミスの NAS デバイス、Windows ファイル サーバー、Azure NetApp Files、または汎用ファイル サーバー用の **ファイル ストレージ リソースを追加** し、
    - S3 または Azure Blob Storage (Azure Files サービスを含む) の **オブジェクト ストレージ リソースを追加** し、
    - DFS ファイル サーバー用の **DFS 名前空間を追加** します。

    **[Add file storage resource (ファイル ストレージ リソースの追加)]** を選択して、Azure NetApp Files の移行先を追加するウィザードを開始します。

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-1.png" alt-text="StorageX デプロイに新しいリソースを追加するためのスクリーンショット":::

1. CIFS と SMB の移行には、 **[Generic CIFS (一般的な CIFS)]** を選択します。

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-2.png" alt-text="StorageX デプロイに一般的な CIFS リソースを追加するためのスクリーンショット":::

1. Azure NetApp Files コンピューター アカウントを検索します。 Active Directory ドメインに追加されたときに、Azure NetApp Files コンピューターアカウントが作成されました。
   
    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-3.png" alt-text="Azure NetApp Files コンピューター アカウントを追加するためのスクリーンショット":::

    ウィザードを **終了** し、アカウントが追加されていること、および作成した共有が含まれていることを確認します。

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-4.png" alt-text="追加されたリソースを参照するためのスクリーンショット":::

    すべてのソースとターゲットは、個別のリソースとして追加する必要があります。 例では Azure NetApp Files 共有を追加していますが、他のサービスでは若干異なる場合があります。

1.  **[データ移動]** タブをクリックします。

1.  ツリー ウィンドウで、新しいポリシーを作成するフォルダーを右クリックし、 **[Phased Migration Policy (段階的な移行ポリシー)** ] を選択します。

1.  ウィザードの手順に従って、段階的な移行ポリシーを作成します。 適切な **ソース** と **移行先** のターゲットを選択します。

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-migration-1.png" alt-text="適切なソース ターゲットと移行先ターゲットを選択するためのスクリーンショット":::

1. ポリシーの名前と説明を入力し、ウィザードを **終了** します。

1.  新しいポリシーを右クリックし、 **[実行]** を選択してテスト移行を実行します。 ソース リソースの複数のファイルのセキュリティ構成を確認し、必要なアクセス許可が与えられていることを確認することをお勧めします。

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-migration-2.png" alt-text="定義済みポリシーを実行するスクリーンショット":::

1. ポリシーの状態を確認して、初期移行が正常に実行されたことを確認してください。 

## <a name="support"></a>サポート

問題が発生した場合、Microsoft と Data Dynamics は通常のサポート チャネルを使用して支援できます。 インフラストラクチャの問題または任意の Azure サービスについては、Microsoft にお問い合わせください。 Data Dynamics StorageX のインストールに関連する問題については、Data Dynamics にお問い合わせください。

### <a name="how-to-open-a-case-with-azure"></a>Azureでケースを開く方法

[Azure portal](https://portal.azure.com) で、上部にある検索バーで「サポート」を検索します。 **[ヘルプとサポート]**  ->  **[新しいサポート要求]** の順に選択します。

### <a name="how-to-open-a-case-with-data-dynamics"></a>Data Dynamics でケースを開く方法

[Data Dynamics Support Portal](https://www.datdynsupport.com/) にアクセスします。 登録していない場合は、電子メール アドレスを入力すると、サポート チームによってアカウントが作成されます。 サインインしたら、ユーザー要求を開きます。 Azure サポート ケースを既に開いている場合は、要求作成時にサポート要求番号をメモしてください。

## <a name="next-steps"></a>次のステップ

詳細については、各種のリソースを参照してください。

- [Storage の移行の概要](../../../common/storage-migration-overview.md)
- [移行ツール比較表](./migration-tools-comparison.md)の Data Dynamics StorageX がサポートしている機能
- [Data Dynamics](https://www.datadynamicsinc.com/)
- [Data Dynamics Customer Portal](https://www.datdynsupport.com/) の StorageX に関する完全なドキュメント
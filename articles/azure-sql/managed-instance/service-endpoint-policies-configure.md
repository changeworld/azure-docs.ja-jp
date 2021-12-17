---
title: サービス エンドポイント ポリシーを構成する
description: 承認Azure Storageアカウントへの侵入から Azure SQL Managed Instanceを保護するように、サービス エンドポイント Azure Storage構成します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: references_regions, ignite-fall-2021
ms.devlang: ''
ms.topic: how-to
author: zoran-rilak-msft
ms.author: zoranrilak
ms.reviewer: mathoma
ms.date: 11/02/2021
ms.openlocfilehash: 9760285da827e884c5dfa49ca1da58b3e76292de
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091175"
---
# <a name="configure-service-endpoint-policies-preview-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance のサービス エンドポイント ポリシー (プレビュー) を構成する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Virtual Network (VNet) Azure Storage [サービス エンドポイントポリシー](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)を使用すると、エグレス仮想ネットワーク トラフィックを Azure Storage にフィルター処理し、特定のストレージ アカウントへのデータ転送を制限できます。

エンドポイント ポリシーを構成し、それらを自分のエンドポイント ポリシー SQL Managed Instance現在プレビュー中です。 

## <a name="key-benefits"></a>主な利点

Azure Storageサービス エンドポイント ポリシーの仮想ネットワーク を構成すると、Azure SQL Managed Instanceは以下の利点があります：

- __Azure Storageへの Azure SQL Managed Instance トラフィックのセキュリティの向上__: エンドポイント ポリシーは、ビジネスに不可欠なデータの誤ったまたは悪意のある侵入を防ぐセキュリティ制御を確立します。 トラフィックは、データ ガバナンス要件に準拠しているストレージ アカウントにのみ制限できます。

- __アクセスできるストレージ アカウントをきめ細かく制御する__: サービス エンドポイント ポリシーでは、サブスクリプション、リソース グループ、個々のストレージ アカウント レベルでストレージ アカウントへのトラフィックを許可できます。 管理者は、サービス エンドポイント ポリシーを使用して、Azure で組織のデータ セキュリティ アーキテクチャへの準拠を強制できます。

- __システム トラフィックは影響を受けません__:サービス エンドポイント ポリシーは、Azure SQL Managed Instanceサービスが機能するために必要なストレージへのアクセス妨げない。 これには、バックアップ、データ ファイル、トランザクション ログ ファイル、その他の資産のストレージが含まれます。

> [!IMPORTANT]
> サービス エンドポイント ポリシーは、サブネットから送信され、Azure storage SQL Managed Instance終了するトラフィックのみを制御します。 ポリシーは、たとえば、データベースをオンプレム BACPAC ファイルへのエクスポート、Azure Data Factory 統合、Azure Diagnostic 設定 を使用した診断情報の収集、または Azure Storage を直接ターゲットにしないデータ抽出のその他のメカニズムには影響を与え "されません"。

## <a name="limitations"></a>制限事項

Azure SQL Managed Instance のサービス エンドポイント ポリシーを有効にする場合、次の制限があります。

- プレビュー中は、**中国東部 2**、**中国北部 2**、**米国中部 EUAP**、**米国東部 2 EUAP**、**US Gov アリゾナ**、**US Gov テキサス**、**US Gov バージニア**、**米国中西部を除き**、SQL Managed Instanceがサポートされているすべての Azure リージョンでこの機能を利用できます。
- この機能は、Azure Resource Manager デプロイ モデルを使ってデプロイされた仮想ネットワークでのみ使用できます。
- この機能は、[サービス エンドポイント](../../virtual-network/virtual-network-service-endpoints-overview.md)が有効になっているサブネットでのみAzure Storageできます。
- Azure Storage のサービス エンドポイントを有効にすると、読み取りアクセス geo 冗長ストレージ (RA-GRS) と geo 冗長ストレージ (GRS) トラフィックをサポートするために仮想ネットワークをデプロイするペアリージョンも含まれるまで拡張されます。
- サービス エンドポイントにサービス エンドポイント ポリシーを割り当てると、エンドポイントがリージョンスコープからグローバル スコープにアップグレードされます。 つまり、ストレージ アカウントがAzure Storageリージョンに関係なく、すべてのトラフィックがサービス エンドポイントを通過します。

## <a name="prepare-storage-inventory"></a>ストレージ インベントリを準備する

サブネットでサービス エンドポイント ポリシーの構成を開始する前に、そのサブネットでマネージド インスタンスがアクセスできる必要があるストレージ アカウントのリストを作成します。

次に示すのは、Azure Storageに連絡する可能性があるワークフローのリストです。

- Azure Storageへの[監査](auditing-configure.md)。
- Azure Storageへ[コピー専用のバックアップ](/sql/relational-databases/backup-restore/copy-only-backups-sql-server)を実行します。
- Azure Storageからデータベースの[復元](restore-sample-database-quickstart.md)。
- [BULK INSERTまたはOPENROWSET(BULK ...)](/sql/relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server) を使用したデータのインポート。
- [拡張イベント](../database/xevent-db-diff-from-svr.md)をAzure Storageのイベント ファイルターゲットにログ。
- [Azure DMS オフラインから](../../dms/tutorial-sql-server-to-managed-instance.md) Azure SQL Managed Instanceへの移行。
- [Log Replayサービスから](log-replay-service-migrate.md) Azure SQL Managed Instance への移行。
- [トランザクション レプリケーション](replication-transactional-overview.md) を使用したテーブル の同期。

これらに参加しているストレージ アカウント、またはストレージにアクセスする他のワークフローのアカウント名、リソース グループ、サブスクリプションに注意してください。 


## <a name="configure-policies"></a>ポリシーの構成

まず、サービス エンドポイント ポリシーを作成し、そのポリシーを新しいサブネットに関連SQL Managed Instanceがあります。 ビジネス ニーズに合わせて、このセクションのワークフローを変更します。


> [!NOTE]
> - SQL Managed Instanceサブネットには、/Services/Azure/ManagedInstance サービス 別名を含むポリシーが必要です (ステップ5 を参照)。 
> - サービス エンドポイント ポリシーが既に含まれているサブネットにデプロイされたマネージド インスタンスは、/Services/Azure/ManagedInstance サービス 別名に自動的にアップグレードされます。

### <a name="create-a-service-endpoint-policy"></a>サービス エンドポイント ポリシーを作成する

サービス エンドポイント ポリシーを作成するには、次のステップに従います：

1. [Azure portal](https://portal.azure.com) にサインインします。 
1. **[+ リソースの作成]** を選択します。 
1. 検索ウィンドウで、 _「サービス エンドポイント ポリシー」_ と入力し、 **[サービス エンドポイント ポリシー]** を選択してから、 **[作成]** を選択します。

   ![サービス エンドポイント ポリシーを作成する](../../virtual-network/media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

1. **[基本]** ページで以下の値を入力します：

   - [サブスクリプション]: ドロップダウンからポリシーのサブスクリプションを選択します。
   - リソース グループ: マネージド インスタンスがあるリソース グループを選択するか、または **[Create new]** を選択し、新しいリソース グループの名前を入力します。
   - 名前: ポリシーの名前( **mySEP** など) を指定します。
   - 場所: マネージド インスタンスをホストする仮想ネットワークのリージョンを選択します。 

   ![サービス エンドポイント ポリシーの基本を作成する](../../virtual-network/media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

1. **[ポリシー定義]** で **[別名の追加]** を選択し、 **[別名の追加]** ペインに以下の情報を入力します：
   - サービス別名: [/Services/Azure/ManagedInstance] を選択します。
   - **[追加]** を選択して、サービス別名の追加を完了します。

   ![サービス エンドポイント ポリシーに別名を追加する](./media/service-endpoint-policies-configure/add-an-alias.png)

1. [ポリシー定義] で、 **[リソース]** の **[+追加]** を選択し、 **[リソースの追加]** ペインで以下の情報を入力または選択します：
   - サービス: **[Microsoft.Storage]** を選択します。
   - スコープ: **[サブスクリプションのすべてのアカウント]** を選択します。
   - サブスクリプション: ストレージ アカウントを含むサブスクリプションを選択して、許可します。 前に作成した [Azure ストレージ アカウントのインベントリ](#prepare-storage-inventory) を参照してください。
   - [追加 **] を** 選択して、リソースの追加を完了します。
   - サブスクリプションを追加するには、このステップを繰り返します。 

   ![サービス エンドポイント ポリシーにリソースを追加する](./media/service-endpoint-policies-configure/add-a-resource.png)

1. 省略可能: **[タグ]** のサービス エンドポイント ポリシーでタグを構成できます。
1.  **[確認および作成]** を選択します。 情報を検証し、  **[作成]** を選択します。 さらに編集するには、 **[前へ]** を選択します。

   > [!TIP]
   > まず、サブスクリプション全体へのアクセスを許可するポリシーを構成します。 すべてのワークフローが正常に動作し、構成を検証します。 次に、必要に応じて、個々のストレージ アカウント、またはリソース グループ内のアカウントを許可するポリシーを再構成します。 これを行うには、代わりに **[スコープ:]** フィールドで **[単一アカウント]** または _[リソース グループのすべてのアカウント]_ を選択し、必要に応じて他のフィールドに入力します。

### <a name="associate-policy-with-subnet"></a>ポリシーをサブネットに関連付ける

サービス エンドポイント ポリシーが作成された後、ポリシーをサブネットに関連SQL Managed Instanceします。

ポリシーを関連付けるには、次のステップに従います：

1. Azure portalの _[すべてのサービス]_ ボックスに _仮想ネットワーク_ を検索します。 **[仮想ネットワーク]** を選択します。
1. マネージド インスタンスをホストする仮想ネットワークを見つけて選択します。
1. **[サブネット]** を選択し、マネージド インスタンス専用のサブネットを選択します。 サブネット ペインに以下の情報を入力します：
    - サービス: **[Microsoft.Storage]** を選択します。 このフィールドが空の場合は、このサブネットにサービス エンドポイントをAzure Storageに構成する必要があります。
    - サービス エンドポイント ポリシー: SQL Managed Instanceサブネットに適用するサービス エンドポイント ポリシーを選択します。

   ![サービス エンドポイント ポリシーをサブネットに関連付ける](./media/service-endpoint-policies-configure/associate-service-endpoint-policy.png)

1. **[保存]** を選択して、仮想ネットワークの構成を完了します。

> [!WARNING]
> このサブネットのポリシーに `/Services/Azure/ManagedInstance` 別名が設定されていない場合は、以下のエラーが表示される場合があります： ` Failed to save subnet 'subnet'. Error: 'Found conflicts with NetworkIntentPolicy.`
> `Details: Service endpoint policies on subnet are missing definitions`
> これを解決するには、サブネットのすべてのポリシーを更新して`/Services/Azure/ManagedInstance` 別名を含める必要があります。

## <a name="next-steps"></a>次の手順

- [Azure Storageアカウントの保護](../../storage/common/storage-network-security.md)の詳細について学習します。
- [SQL Managed Instanceのセキュリティ機能](../database/security-overview.md)に関して読み取ってください。
- SQL Managed Instanceの[接続アーキテクチャ](connectivity-architecture-overview.md)を探検する。

---
title: Azure Synapse ワークスペースのアクセス制御の概要
description: この記事では、Synapse ワークスペースとそこに含まれるリソースとコード成果物へのアクセスを制御するために使用されるメカニズムについて説明します。
services: synapse-analytics
author: meenalsri
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 11/02/2021
ms.author: mesrivas
ms.reviewer: wiassaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: 39eba039a8f7b381e998be3d8bfe5cd213217ab3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013424"
---
# <a name="azure-synapse-access-control"></a>Azure Synapse アクセス制御 

この記事では、Azure Synapse のコンピューティング リソースとデータへのアクセスを制御するために使用できるメカニズムについて概説します。  

## <a name="overview"></a>概要

Azure Synapse は、以下を統合する包括的できめ細かなアクセス制御システムを提供します。 
- リソースを管理し、ストレージ内のデータにアクセスするための **Azure ロール**。 
- コードへのライブ アクセスと実行を管理するための **Synapse ロール**。 
- SQL プール内のデータへのデータ プレーン アクセスのための **SQL ロール**。 
- 継続的インテグレーションと継続的デプロイのサポートを含む、ソース コード管理のための **Git アクセス許可**。  

Azure Synapse ロールは、さまざまなスコープで適用できるアクセス許可のセットを提供します。 この細分性により、管理者、開発者、セキュリティ担当者、およびオペレーターに対して、コンピューティング リソースとデータへの適切なアクセス権を簡単に付与できます。

アクセス制御は、対象者の職務に合ったセキュリティ グループを使用することで簡素化できます。 アクセスを管理するには、適切なセキュリティ グループのユーザーを追加するか削除するだけで済みます。

## <a name="access-control-elements"></a>アクセス制御の要素

### <a name="create-and-manage-azure-synapse-compute-resources"></a>Azure Synapse コンピューティング リソースの作成と管理

Azure ロールは、次のものの管理を制御するために使用されます。 
- 専用 SQL プール
- Data Explorer プール
- Apache Spark プール
- 統合ランタイム

これらのリソースを "*作成*" するには、リソース グループの Azure 所有者または共同作成者である必要があります。 作成したリソースを "*管理*" するには、リソース グループまたは個々のリソースの Azure 所有者または共同作成者である必要があります。 

### <a name="develop-and-execute-code-in-azure-synapse"></a>Azure Synapse でコードを開発して実行する 

Synapse では、次の 2 つの開発モデルがサポートされています。

- **Synapse ライブ開発**。 Synapse Studio でコードを開発およびデバッグした後、**発行** して保存および実行します。  Synapse サービスが、コードの編集と実行に関する信頼できるソースになります。  Synapse Studio を終了すると、発行されていない作業はすべて失われます。  
- **Git 対応の開発**。 Synapse Studio でコードを開発およびデバッグし、Git リポジトリの作業ブランチに変更を **コミット** します。 1 つ以上のブランチからの作業はコラボレーション ブランチに統合されます。そしてそこからサービスに **発行** します。 Git リポジトリがコード編集に関する信頼できるソースとなり、サービスが実行に関する信頼できるソースとなります。 Synapse Studio を終了する前に、変更を Git リポジトリにコミットするか、サービスに発行する必要があります。 Synapse Analytics を Git と共に使用する方法の[詳細を確認](../cicd/continuous-integration-delivery.md)してください。

どちらの開発モデルでも、Synapse Studio にアクセスできるすべてのユーザーがコード成果物を作成できます。 ただし、サービスへの成果物の発行、発行された成果物の読み取り、Git への変更のコミット、コードの実行、資格情報で保護されているリンクされたデータへのアクセスを行うには、追加のアクセス許可が必要です。

### <a name="azure-synapse-roles"></a>Azure Synapse ロール

Azure Synapse ロールは、次の操作を許可する Synapse サービスへのアクセスを制御するために使用されます。 
- 発行されたコード成果物を一覧表示する。 
- コード成果物、リンクされたサービス、および資格情報の定義を発行する。
- Synapse コンピューティング リソースを使用するコードまたはパイプラインを実行する。
- 資格情報で保護されているリンクされたデータにアクセスするコードまたはパイプラインを実行する。
- 発行されたコード成果物に関連付けられた出力を表示する。
- コンピューティング リソースの状態を監視し、ランタイム ログを表示する。

Azure Synapse ロールは、ワークスペース スコープまたはよりきめ細かいスコープで割り当てられます。これにより、特定の Azure Synapse リソースに付与されるアクセス許可を制限できます。

### <a name="git-permissions"></a>Git アクセス許可

Git モードで Git 対応の開発を使用する場合は、リンクされたサービスと資格情報の定義などのコード成果物を読み取るために、Synapse ユーザー ロールまたは Synapse RBAC (ロールベースのアクセス制御) ロールに加えて、Git アクセス許可が必要です。 Git モードでコード成果物への変更をコミットするには、Git アクセス許可、ワークスペースの Azure 共同作成者 (Azure RBAC) ロール、および Synapse 成果物発行元 (Synapse RBAC) ロールが必要です。

   
### <a name="access-data-in-sql"></a>SQL 内のデータへのアクセス

専用およびサーバーレスの SQL プールを使用する場合、データ プレーン アクセスは SQL アクセス許可を使用して制御されます。 

ワークスペースの作成者は、そのワークスペースにおける Active Directory 管理者として割り当てられます。 作成後、このロールは、Azure portal で別のユーザーまたはセキュリティ グループに割り当てることができます。

**サーバーレス SQL プール**: Synapse 管理者には、"組み込み" サーバーレス SQL プールに対する `db_owner` (`DBO`) アクセス許可が付与されます。 サーバーレス SQL プールへのアクセス権を他のユーザーに付与するには、Synapse 管理者が各サーバーレス プールで SQL スクリプトを実行する必要があります。  

**専用 SQL プール**: Synapse 管理者は、専用 SQL プール内のデータに対するフル アクセス権と、他のユーザーにアクセス権を付与する権限を持っています。 Synapse 管理者は、データベースの削除以外の構成およびメンテナンスのアクティビティを専用プールで実行することもできます。 Active Directory 管理者のアクセス許可は、ワークスペースとワークスペース MSI の作成者に付与されます。  それ以外、専用 SQL プールにアクセスするためのアクセス許可は自動的には付与されません。 専用 SQL プールへのアクセス権を他のユーザーまたはグループに付与するには、Active Directory 管理者または Synapse 管理者が各専用 SQL プールに対して SQL スクリプトを実行する必要があります。

SQL プールで SQL アクセス許可を付与するための SQL スクリプトの例については、[Synapse アクセス制御の設定方法](./how-to-set-up-access-control.md)に関するページを参照してください。  

### <a name="accessing-data-in-data-explorer-pools"></a>Data Explorer プール内のデータへのアクセス

Data Explorer プールを使用する場合、データ プレーン アクセスは Data Explorer のアクセス許可によって制御されます。 Synapse 管理者には、Data Explorer プールに対する `All Database admin` アクセス許可が付与されます。 Synapse 管理者は、他のユーザーまたはグループに Data Explorer プールへのアクセス権を付与するために、「[セキュリティ ロール管理](/azure/data-explorer/kusto/management/security-roles?context=/azure/synapse-analytics/context/context)」を参照してください。 データ プレーン アクセスの詳細については、[Data Explorer アクセス制御の概要](/azure/data-explorer/kusto/management/access-control/index?context=/azure/synapse-analytics/context/context)に関するページを参照してください。


 ### <a name="accessing-system-managed-data-in-storage"></a>ストレージ内のシステム マネージド データへのアクセス

サーバーレス SQL プールと Apache Spark テーブルでは、ワークスペースに関連付けられた ADLS Gen2 コンテナーにデータが格納されます。 ユーザーがインストールした Apache Spark ライブラリも、同じストレージ アカウントで管理されます。 これらのユース ケースを可能にするには、ユーザーとワークスペース MSI に、このワークスペースの ADLS Gen2 ストレージ コンテナーに対する **Storage Blob データ共同作成者** のアクセス権を付与する必要があります。  

## <a name="using-security-groups-as-a-best-practice"></a>ベスト プラクティスとしてのセキュリティ グループの使用

アクセス制御の管理を簡素化するために、セキュリティ グループを使用して、個人およびグループにロールを割り当てることができます。 セキュリティ グループは、Synapse リソースまたは成果物にアクセスする必要がある、組織内のペルソナまたは職務をミラーリングするために作成できます。  これらのペルソナベースのセキュリティ グループには、1 つ以上の Azure ロール、Synapse ロール、SQL アクセス許可、または Git アクセス許可を割り当てることができます。 適切に選ばれたセキュリティ グループを使用すると、適切なセキュリティ グループにユーザーを追加することで、ユーザーに対して必要なアクセス許可を簡単に割り当てることができます。 

>[!Note]
>セキュリティ グループを使用してアクセスを管理する場合、変更が有効になる前に Azure Active Directory によって追加の待ち時間が発生します。 

## <a name="access-control-enforcement-in-synapse-studio"></a>Synapse Studio でのアクセス制御の適用

Synapse Studio の動作は、アクセス許可と現在のモードによって異なったものとなります。
- **Synapse ライブ モード**: Synapse Studio では、必要なアクセス許可がない場合、発行されたコンテンツの表示、コンテンツの発行、またはその他のアクションを実行することはできません。  場合によっては、自分が使用または保存できないコード成果物を作成できないこともあります。 
- **Git モード:** 現在のブランチに変更をコミットするための Git アクセス許可があり、ライブ サービスに変更を発行するためのアクセス許可 (Synapse 成果物発行元ロール) およびワークスペースの Azure 共同作成者ロールをお持ちの場合は、コミット アクションが許可されます。  

場合によっては、発行またはコミットするためのアクセス許可がなくてもコード成果物を作成できることがあります。 これにより、(必要な実行アクセス許可で) コードを実行できます。 一般的なタスクに必要なロールの[詳細をご覧ください](./synapse-workspace-understand-what-role-you-need.md)。 

Synapse Studio で機能が無効になっている場合は、必要なアクセス許可を示すヒントが表示されます。 [Synapse RBAC ロール ガイド](./synapse-workspace-synapse-rbac-roles.md#synapse-rbac-actions-and-the-roles-that-permit-them)を使用して、不足しているアクセス許可を提供するために必要なロールを検索できます。


## <a name="next-steps"></a>次のステップ

- [Synapse RBAC](./synapse-workspace-synapse-rbac.md) についてさらに学習する
- [Synapse RBAC ロール](./synapse-workspace-synapse-rbac-roles.md)についてさらに学習する
- セキュリティ グループを使用して Synapse ワークスペースの[アクセス制御を設定する](./how-to-set-up-access-control.md)方法について学習する。
- [Synapse RBAC ロールの割り当てを確認する](./how-to-review-synapse-rbac-role-assignments.md)方法について学習する
- [Synapse RBAC ロールの割り当てを管理する](./how-to-manage-synapse-rbac-role-assignments.md)方法について学習する

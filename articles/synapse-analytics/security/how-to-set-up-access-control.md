---
title: Synapse ワークスペースのアクセス制御を設定する方法
description: この記事では、Azure ロール、Synapse ロール、SQL アクセス許可、および Git アクセス許可を使用して Azure Synapse ワークスペースへのアクセスを制御する方法について説明します。
services: synapse-analytics
author: meenalsri
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 8/05/2021
ms.author: ronytho
ms.reviewer: jrasnick, wiassaf
ms.custom: subject-rbac-steps
ms.openlocfilehash: d80b12e807e6c6f0999927bc373fe64c1feb1b40
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846598"
---
# <a name="how-to-set-up-access-control-for-your-azure-synapse-workspace"></a>Azure Synapse ワークスペースのアクセス制御を設定する方法 

この記事では、Azure ロール、Azure Synapse ロール、SQL アクセス許可、および Git アクセス許可を使用して Microsoft Azure Synapse ワークスペースへのアクセスを制御する方法について説明します。   

このガイドでは、ワークスペースを設定し、多くの Azure Synapse プロジェクトに適した基本的なアクセス制御システムを構成します。  きめ細かな制御が必要な場合のために、より高度なオプションについても説明します。  

Azure Synapse のアクセス制御は、組織内のロールとペルソナに合ったセキュリティ グループを使用することで簡素化できます。  アクセスを管理するには、セキュリティ グループのユーザーを追加するか削除するだけで済みます。

このチュートリアルを開始する前に、[Azure Synapse アクセス制御の概要](./synapse-workspace-access-control-overview.md)に関するページを参照して、Azure Synapse Analytics で使用されるアクセス制御メカニズムについて理解してください。   

## <a name="access-control-mechanisms"></a>アクセス制御メカニズム

> [!NOTE]
> このガイドで採用するアプローチでは、複数のセキュリティ グループを作成してから、それらのグループにロールを割り当てます。 グループの設定後、セキュリティ グループ内のメンバーシップを管理するだけで、ワークスペースへのアクセスを制御できます。

Azure Synapse ワークスペースをセキュリティで保護するには、次の項目を構成するパターンに従います。

- **セキュリティ グループ**。類似のアクセス要件を持つユーザーをグループ化します。
- **Azure ロール**。SQL プール、Apache Spark プールと統合ランタイムを作成および管理でき、ADLS Gen2 ストレージにアクセスできるユーザーを制御します。
- **Synapse ロール**。発行されたコード成果物へのアクセス、Apache Spark コンピューティング リソースと統合ランタイムの使用を制御します 
- **SQL アクセス許可**。SQL プールへの管理アクセスとデータ プレーン アクセスを制御します。 
- **Git アクセス許可**。 ワークスペースの Git サポートを構成した場合に、ソース管理のコード成果物にアクセスできるユーザーを制御します 
 
## <a name="steps-to-secure-an-azure-synapse-workspace"></a>Azure Synapse ワークスペースをセキュリティ保護する手順

このドキュメントでは、手順を簡素化するために、標準名を使用しています。 それらを任意の名前に置き換えてください。

|設定 | 標準名 | 説明 |
| :------ | :-------------- | :---------- |
| **Synapse ワークスペース** | `workspace1` |  Azure Synapse ワークスペースに使用される名前。 |
| **ADLSGEN2 アカウント** | `storage1` | ワークスペースで使用する ADLS アカウント。 |
| **コンテナー** | `container1` | ワークスペースで既定で使用される STG1 内のコンテナー。 |
| **Active Directory テナント** | `contoso` | Active Directory テナント名。|
||||

## <a name="step-1-set-up-security-groups"></a>手順 1: セキュリティ グループを設定する

>[!Note] 
>プレビュー期間中は、Azure Synapse の "**Synapse SQL 管理者**" および "**Synapse Apache Spark 管理者**" ロールにマップされるセキュリティ グループを作成することが推奨されていました。  きめ細かな Synapse RBAC ロールとスコープが新たに導入されたため、これらの新しい機能を使用してワークスペースへのアクセスを制御することをお勧めします。  これらの新しいロールとスコープでは、構成の柔軟性が向上するほか、開発者が分析アプリケーションを作成する際に多くの場合は SQL と Spark を組み合わせて使用し、ワークスペース全体ではなく特定のリソースへのアクセスを必要とすることが認識されています。 [Synapse RBAC](./synapse-workspace-synapse-rbac.md) の詳細を確認してください。

ワークスペースに以下のセキュリティ グループを作成します。

- **`workspace1_SynapseAdministrators`** 。ワークスペースを完全に制御する必要があるユーザー向けです。  少なくとも最初は、自分をこのセキュリティ グループに追加してください。
- **`workspace1_SynapseContributors`** 。コードの開発、デバッグ、およびサービスへの発行を行う必要がある開発者向けです。   
- **`workspace1_SynapseComputeOperators`** 。Apache Spark プールと統合ランタイムを管理および監視する必要があるユーザー向けです。
- **`workspace1_SynapseCredentialUsers`** ワークスペース MSI (マネージド サービス ID) 資格情報を使用してオーケストレーション パイプラインをデバッグおよび実行する必要があり、パイプラインの実行をキャンセルする必要があるユーザー向けです。   

これらのグループには、この後すぐにワークスペース スコープの Synapse ロールを割り当てます。  

また、次のセキュリティ グループも作成します。 
- **`workspace1_SQLAdmins`** 。ワークスペースの SQL プール内の SQL Active Directory 管理権限を必要とするユーザー向けのグループです。 

`workspace1_SQLAdmins` グループは、SQL プールの作成時に SQL のアクセス許可を構成するときに使用されます。 

基本的なセットアップには、これらの 5 つのグループで十分です。 後でセキュリティ グループを追加して、より特殊化されたアクセスを必要とするユーザーを処理したり、特定のリソースへのアクセスのみをユーザーに許可したりすることができます。

> [!NOTE]
>- セキュリティ グループを作成する方法については、[こちらの記事](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)を参照してください。
>- 別のセキュリティ グループからセキュリティ グループを追加する方法については、[こちらの記事](../../active-directory/fundamentals/active-directory-groups-membership-azure-portal.md)を参照してください。

>[!Tip]
>個々の Synapse ユーザーは、Azure portal の Azure Active Directory を使用してグループ メンバーシップを表示し、付与されているロールを確認できます。

## <a name="step-2-prepare-your-adls-gen2-storage-account"></a>手順 2: ADLS Gen2 ストレージ アカウントを準備する

Azure Synapse ワークスペースでは、既定のストレージ コンテナーが次のことに使用されます。
  - Spark テーブル用のバッキング データ ファイルの格納
  - Spark ジョブの実行ログ
  - インストールすることにしたライブラリの管理

ストレージに関する次の情報を特定します。

- ワークスペースに使用する ADLS Gen2 アカウント。 このドキュメントでは、それを `storage1` と呼びます。 `storage1` は、ワークスペースの "プライマリ" ストレージ アカウントと見なされます。
- Synapse ワークスペースで既定で使用される `workspace1` 内のコンテナー。 このドキュメントでは、それを `container1` と呼びます。 
 
- **[アクセス制御 (IAM)]** を選択します。

- **[追加]**  >  **[ロールの割り当ての追加]** を選択して、[ロールの割り当ての追加] ページを開きます。

- 次のロールを割り当てます。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。
    
    | 設定 | 値 |
    | --- | --- |
    | Role | ストレージ BLOB データ共同作成者 |
    | アクセスの割り当て先 |サービス プリンシパル |
    | メンバー |workspace1_SynapseAdmins、workspace1_SynapseContributors、およびworkspace1_SynapseComputeOperators|

    ![Azure portal でロール割り当てページを追加します。](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

## <a name="step-3-create-and-configure-your-azure-synapse-workspace"></a>手順 3: Azure Synapse ワークスペースを作成して構成する

Azure portal で、Azure Synapse ワークスペースを作成します。

- サブスクリプションを選択します

- Azure "**所有者**" ロールを持っているリソース グループを選択または作成します。

- ワークスペースに `workspace1` という名前を指定します

- ストレージ アカウントとして `storage1` を選択します

- "Filesystem" として使用されているコンテナーに `container1` を選択します。

- Synapse Studio で WS1 を開きます

- **[管理]**  >  **[アクセス制御]** に移動して、次のようにセキュリティ グループに Synapse ロールを *ワークスペース スコープ* で割り当てます。
  - "**Synapse 管理者**" ロールを `workspace1_SynapseAdministrators` に割り当てます 
  - "**Synapse 共同作成者**" ロールを `workspace1_SynapseContributors` に割り当てます 
  - "**Synapse コンピューティング オペレーター**" ロールを `workspace1_SynapseComputeOperators` に割り当てます

## <a name="step-4-grant-the-workspace-msi-access-to-the-default-storage-container"></a>手順 4:既定のストレージ コンテナーに対するワークスペース MSI アクセスを許可する

Azure Synapse では、パイプラインを実行してシステム タスクを実行するために、ワークスペース マネージド サービス ID (MSI) に既定の ADLS Gen2 アカウントの `container1` に対するアクセス権が必要です。 詳細については、「[Azure Synapse ワークスペース マネージド ID](../../data-factory/data-factory-service-identity.md?context=/azure/synapse-analytics/context/context&tabs=synapse-analytics)」を参照してください。

- Azure ポータルを開きます
- ストレージ アカウント `storage1`、次に `container1` を見つけます
- **[アクセス制御 (IAM)]** を選択します。
- **[追加]**  >  **[ロールの割り当ての追加]** を選択して、[ロールの割り当ての追加] ページを開きます。
- 次のロールを割り当てます。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。
    
    | 設定 | 値 |
    | --- | --- |
    | Role | ストレージ BLOB データ共同作成者 |
    | アクセスの割り当て先 | マネージド ID |
    | メンバー | マネージド ID 名  |

    > [!NOTE]
    > マネージド ID の名前は、ワークスペース名でもあります。

    ![Azure portal でロール割り当てページを追加します。](../../../includes/role-based-access-control/media/add-role-assignment-page.png)


## <a name="step-5-grant-synapse-administrators-the-azure-contributor-role-on-the-workspace"></a>手順 5:ワークスペースの "Azure 共同作成者" ロールを Synapse 管理者に付与する 

SQL プール、Apache Spark プールと統合ランタイムを作成するには、ユーザーはワークスペースで少なくとも Azure 共同作成者ロールを持っている必要があります。 共同作成者ロールでは、一時停止やスケーリングなど、リソースの管理もユーザーに許可されます。 Azure portal または Synapse Studio を使用して SQL プール、Apache Spark プール、統合ランタイムを作成する場合は、Azure 共同作成者ロールがリソース グループ レベルで必要になります。 

- Azure ポータルを開きます
- ワークスペース `workspace1` を見つけます
- **[アクセス制御 (IAM)]** を選択します。
- **[追加]**  >  **[ロールの割り当ての追加]** を選択して、[ロールの割り当ての追加] ページを開きます。
- 次のロールを割り当てます。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。
    
    | 設定 | 値 |
    | --- | --- |
    | Role | 共同作成者 |
    | アクセスの割り当て先 | サービス プリンシパル |
    | メンバー | workspace1_SynapseAdministrators  |

    ![Azure portal でロール割り当てページを追加します。](../../../includes/role-based-access-control/media/add-role-assignment-page.png) 

## <a name="step-6-assign-sql-active-directory-admin-role"></a>手順 6:"SQL Active Directory 管理者" ロールを割り当てる

ワークステーションの作成者は、ワークスペースの SQL Active Directory 管理者として自動的に設定されます。  このロールは、1 人のユーザーまたは 1 つのグループにのみ付与できます。 この手順では、ワークスペースの SQL Active Directory 管理者を `workspace1_SQLAdmins` セキュリティ グループに割り当てます。  このロールを割り当てると、このグループには、ワークスペース内のすべての SQL プールとデータベースに対する高い特権を持つ管理者アクセス権が付与されます。   

- Azure ポータルを開きます
- `workspace1` に移動します
- **[設定]** で、 **[SQL Active Directory 管理者]** を選択します
- **[管理者の設定]** を選択し、 **`workspace1_SQLAdmins`** を選択します

>[!Note]
>手順 6 は省略可能です。  `workspace1_SQLAdmins` グループに権限の低いロールを付与することもできます。 `db_owner` またはその他の SQL ロールを割り当てるには、各 SQL データベースでスクリプトを実行する必要があります。 

## <a name="step-7-grant-access-to-sql-pools"></a>手順 7:SQL プールへのアクセス権を付与する

既定では、Synapse 管理者ロールが割り当てられているすべてのユーザーには、ワークスペース内のサーバーレス SQL プールに対する SQL `db_owner` ロールも割り当てられます。

他のユーザーの SQLプールへのアクセスは、SQLアクセス許可を使用されます。  SQL アクセス許可を割り当てるには、作成後に各 SQL データベースで SQL スクリプトを実行する必要があります。  これらのスクリプトを実行する必要があるケースは 3 つあります。
1. サーバーレス SQL プール (組み込み) およびそのデータベースへのアクセス権を他のユーザーに付与する
2. 専用 SQL プール データベースへのアクセス権を任意のユーザーに付与する

SQL スクリプトの例を以下に示します。

専用 SQL プール データベースへのアクセス権を付与するには、ワークスペースの作成者または `workspace1_SynapseAdministrators` グループの任意のメンバーがスクリプトを実行できます。  

サーバーレス SQL プール (組み込み) へのアクセス権を付与するには、 `workspace1_SQLAdmins` グループまたは `workspace1_SynapseAdministrators` グループの任意のメンバーがスクリプトを実行できます。 

> [!TIP]
> 以下の手順は、すべての SQL データベースへのユーザー アクセスを付与するために、SQL プール **ごと** に実行する必要があります。ただし、ワークスペース レベルでユーザーに sysadmin ロールを割り当てることができる「[ワークスペース スコープのアクセス許可](#workspace-scoped-permission)」セクションは除きます。

### <a name="step-71-serverless-sql-pool-built-in"></a>手順 7.1:サーバーレス SQL プール (組み込み)

このセクションでは、サーバーレス SQL プール (組み込み) 内の特定のデータベースまたはすべてのデータベースへのアクセス権をユーザーに付与する方法を示したスクリプト例が記されています。

> [!NOTE]
> スクリプトの例で、 *alias* はアクセス権を付与するユーザーまたはグループのエイリアスに置き換え、*domain* は使用している会社のドメインに置き換えてください。

#### <a name="database-scoped-permission"></a>データベース スコープのアクセス許可

ユーザーに **単一の** サーバーレス SQL データベースへのアクセスを許可するには、次の例の手順に従います。

1. ログインを作成します

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. ユーザーを作成します

    ```sql
    use yourdb -- Use your database name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. 指定したロールのメンバーにユーザーを追加します

    ```sql
    use yourdb -- Use your database name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

#### <a name="workspace-scoped-permission"></a>ワークスペース スコープのアクセス許可

ワークスペース内の **すべての** サーバーレス SQL プールに対するフル アクセス権を付与するには、次の例のスクリプトを使用します。

```sql
use master
go
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE sysadmin ADD MEMBER [alias@domain.com];
```

### <a name="step-72-dedicated-sql-pools"></a>手順 7.2:専用 SQL プール

**単一の** 専用 SQL プール データベースへのアクセス権を付与するには、Azure Synapse SQL スクリプト エディターで次の手順を実行します。

1. *[接続先]* ドロップダウンを使用して選択したターゲット データベースで次のコマンドを実行して、データベースにユーザーを作成します。

    ```sql
    --Create user in the database
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. データベースにアクセスするためのロールをユーザーに付与します。

    ```sql
    --Grant role to the user in the database
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_owner* アクセス許可を付与することが望ましくない場合は、*db_datareader* および *db_datawriter* を読み取りまたは書き込みアクセス許可のために使用することができます。
> Spark ユーザーが Spark と SQL プールとの間で直接読み取りまたは書き込みを行うには、*db_owner* アクセス許可が必要です。

ユーザーを作成した後、クエリを実行して、サーバーレス SQL プールでストレージ アカウントにクエリを実行できることを検証します。

## <a name="step-8-add-users-to-security-groups"></a>手順 8:セキュリティ グループにユーザーを追加する

アクセス制御システムの初期構成が完了しました。

アクセスを管理するために、設定したセキュリティ グループにユーザーを追加したり、削除したりできます。  ユーザーを Azure Synapse ロールに手動で割り当てることもできますが、そうすると、アクセス許可の構成の整合性が取れなくなります。 代わりに、セキュリティ グループに対してユーザーを追加または削除するだけにしてください。

## <a name="step-9-network-security"></a>手順 9: ネットワークのセキュリティ

ワークスペースをセキュリティで保護するための最後の手順として、[ワークスペース ファイアウォール](./synapse-workspace-ip-firewall.md)を使用してネットワーク アクセスをセキュリティで保護する必要があります。

- [マネージド仮想ネットワーク](./synapse-workspace-managed-vnet.md)を使用しても、または使用しなくても、公衆ネットワークからワークスペースに接続できます。 詳細については、[接続設定](connectivity-settings.md)に関するページを参照してください。
- 公衆ネットワークからのアクセスは、[公衆ネットワーク アクセス機能](connectivity-settings.md#public-network-access)または[ワークスペース ファイアウォール](./synapse-workspace-ip-firewall.md)を有効にすることで制御できます。
- または、[マネージド プライベート エンドポイント](synapse-workspace-managed-private-endpoints.md)および[プライベート リンク](../../azure-sql/database/private-endpoint-overview.md)を使用してワークスペースに接続できます。 [Azure Synapse Analytics マネージド仮想ネットワーク](synapse-workspace-managed-vnet.md)のない Azure Synapse ワークスペースは、マネージド プライベート エンドポイントを介して接続する能力を持ちません。

## <a name="step-10-completion"></a>手順 10:Completion

これで、ワークスペースが完全に構成され、セキュリティ保護されました。

## <a name="supporting-more-advanced-scenarios"></a>より高度なシナリオをサポートする

このガイドでは、基本的なアクセス制御システムのセットアップに重点を置いてきました。 より高度なシナリオをサポートするには、追加のセキュリティ グループを作成し、よりきめ細かいロールをより具体的なスコープでそれらのグループに割り当てます。 次のケースについて考えてみましょう。

CI/CD を含む高度な開発シナリオのためにワークスペースの **Git サポートを有効にする**。  Git モードでは、Git のアクセス許可と Synapse RBAC によって、ユーザーが作業ブランチに変更をコミットできるかどうかが決定されます。  サービスへの発行は、コラボレーション ブランチからのみ行われます。  作業ブランチで更新プログラムの開発とデバッグを行う必要はあっても、ライブ サービスに変更を発行する必要はない開発者向けに、セキュリティ グループの作成を検討してください。

**開発者のアクセスを特定のリソースに限定する**。  特定のリソースへのアクセスのみを必要とする開発者向けに、より細分化されたセキュリティ グループを作成してください。  これらのグループに、特定の Spark プール、統合ランタイム、または資格情報をスコープとする適切な Azure Synapse ロールを割り当てます。

**オペレーターによるコード成果物へのアクセスを制限する**。  Synapse コンピューティング リソースの動作状態を監視し、ログを表示する必要はあっても、コードにアクセスしたりサービスに更新を発行したりする必要はないオペレーター向けに、セキュリティ グループを作成してください。 これらのグループに、特定の Spark プールと統合ランタイムをスコープとする "コンピューティング オペレーター" ロールを割り当てます。  

## <a name="next-steps"></a>次のステップ

 - [Azure Synapse RBAC ロールの割り当てを管理する方法](./how-to-manage-synapse-rbac-role-assignments.md)について学習する
 - [Synapse ワークスペース](../quickstart-create-workspace.md)を作成する

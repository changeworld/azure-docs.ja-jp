---
title: Azure Data Lake Storage (ADLS) Gen1 の登録とスキャン
description: この記事では、Azure Data Lake Storage Gen1 ソースを認証してやり取りする手順を含め、Azure Purview に Azure Data Storage Lake Storage Gen1 データ ソースを登録するプロセスについて説明します
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 743db90c66f4499b356ca0413ab1827aa73d5f2e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852752"
---
# <a name="connect-to-azure-data-lake-gen1-in-azure-purview"></a>Azure Purview で Azure Data Lake Gen1 に接続する

この記事では、Azure Data Lake Storage Gen1 ソースを認証してやり取りする手順を含め、Azure Purview に Azure Data Storage Lake Storage Gen1 データ ソースを登録するプロセスについて説明します。

> [!Note]
> Azure Data Lake Storage Gen2 の一般提供が開始されました。 今すぐ使用を開始することをお勧めします。 詳細については、[製品に関するページ](https://azure.microsoft.com/services/storage/data-lake-storage/)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register) | [あり](#scan)|[あり](#scan) | [あり](#scan)|[あり](#scan)| いいえ |制限あり** |

\** データセットが [Data Factory Copy アクティビティ](how-to-link-azure-data-factory.md)でソース/シンクとして使用される場合、系列はサポートされています 

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

## <a name="register"></a>登録

このセクションでは、ADLS Gen1 データ ソースを登録し、適切な認証メカニズムを設定して、データ ソースが正常にスキャンされるようにします。

### <a name="steps-to-register"></a>登録する手順

データ ソースのスキャンを設定する前に、Azure Purview にデータ ソースを登録することが重要です。

1. [Azure portal](https://portal.azure.com) にアクセスし、 **[Purview アカウント]** ページに移動して、自分の _Purview アカウント_ をクリックします

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-purview-acct.png" alt-text="データ ソースの登録に使用する Purview アカウントを示すスクリーンショット":::

1. **Purview Studio を開き**、 **[Data Map] > [ソース]** の順に移動します

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-open-purview-studio.png" alt-text="Purview Studio を開くリンクを示すスクリーンショット":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sources.png" alt-text="Data Map の [ソース] リンクへの移動を示すスクリーンショット":::

1. **コレクション** メニューを使用して [コレクション階層](./quickstart-create-collection.md)を作成し、必要に応じて個々のサブコレクションへのアクセス許可を割り当てます

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-collection.png" alt-text="コレクション階層を作成するコレクション メニューを示すスクリーンショット":::

1. **ソース** メニューの該当するコレクションに移動し、 **[登録]** アイコンをクリックして新しい ADLS Gen1 データ ソースを登録します

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-register.png" alt-text="データ ソースの登録に使用するコレクションを示すスクリーンショット":::

1. **[Azure Data Lake Storage Gen1]** データ ソースを選択し、 **[続行]** をクリックします

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-select-source.png" alt-text="データ ソースの選択を示すスクリーンショット":::

1. データ ソースに適切な **名前** を指定し、関連する **Azure サブスクリプション**、既存の **Data Lake Store アカウント名**、**コレクション** を選択し、 **[適用]** をクリックします

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-source-details.png" alt-text="データ ソースを登録するために入力する詳細を示すスクリーンショット":::

1. 選択したコレクションの下に ADLS Gen1 ストレージ アカウントが表示されます

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-source-hierarchy.png" alt-text="スキャンを開始するためにコレクションにマップされたデータ ソースを示すスクリーンショット":::

## <a name="scan"></a>スキャン

### <a name="prerequisites-for-scan"></a>スキャンの前提条件

データ ソースをスキャンするアクセス権を得るには、ADLS Gen1 ストレージ アカウントの認証方法を構成する必要があります。
次のオプションがサポートされています。

> [!Note]
> ストレージ アカウントに対してファイアウォールが有効になっている場合は、スキャンを設定するときに、 [マネージド ID] の認証方法を使用する必要があります。

* **マネージド ID (推奨)** - Azure Purview アカウントが作成されるとすぐに、システムの **マネージド ID** が Azure AD テナント内に自動的に作成されます。 Azure Purview MSI でスキャンを実行するには、リソースの種類に応じて、特定の RBAC ロールを割り当てる必要があります。

* **サービス プリンシパル** - この方法では、Azure Active Directory テナントで新しく作成することも、既存のサービス プリンシパルを使用することもできます。

### <a name="authentication-for-a-scan"></a>スキャンの認証

#### <a name="using-managed-identity-for-scanning"></a>スキャンでのマネージド ID の使用

Purview アカウントに、ADLS Gen1 データ ソースをスキャンするためのアクセス許可を付与することは重要です。 スキャンのアクセス許可を付与する対象に応じて、サブスクリプション、リソースグループ、またはリソースのレベルに Catalog の MSI を追加できます。

> [!Note]
> Azure リソースにマネージド ID を追加できるようにするには、サブスクリプションの所有者である必要があります。

1. [Azure portal](https://portal.azure.com) で、カタログによるスキャンを許可するサブスクリプション、リソース グループ、またはリソース (Azure Data Lake Storage Gen1 ストレージ アカウントなど) のいずれかを検索します。
1. **[概要]** をクリックして、 **[データ エクスプローラー]** を選択します。

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-data-explorer.png" alt-text="ストレージ アカウントを示すスクリーンショット":::

1. 上部ナビゲーションの **[アクセス]** をクリックします

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-storage-access.png" alt-text="ストレージ アカウントのデータ エクスプローラーを示すスクリーンショット":::

1. **[選択]** をクリックし、 **[ユーザーまたはグループの選択]** の選択肢で _[Purview カタログ]_ を追加します。
1. **[読み取り]** と **[実行]** のアクセス許可を選択します。 下のスクリーンショットに示すように、[追加先] オプションで、 **[このフォルダーとすべての子]** および **[アクセス許可エントリと既定のアクセス許可エントリ]** を必ず選択してください。 [設定] メニューの **[OK]**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-assign-permissions.png" alt-text="Purview アカウントにアクセス許可を割り当てる方法の詳細を示すスクリーンショット":::

> [!Tip]
> **アクセス許可エントリ** とは、"_現在_" のファイルとフォルダーに対するアクセス許可エントリです。 **既定のアクセス許可エントリ** とは、新しいファイルとフォルダーによって "_継承_" されるアクセス許可エントリです。
現在存在するファイルにのみアクセス許可を付与するには、**アクセス許可エントリを選択します**。
今後追加されるファイルやフォルダーをスキャンするアクセス許可を付与するには、**既定のアクセス許可エントリを含めます**。

#### <a name="using-service-principal-for-scanning"></a>スキャンでのサービス プリンシパルの使用

##### <a name="creating-a-new-service-principal"></a>新しいサービス プリンシパルの作成

[新しいサービス プリンシパルを作成する](./create-service-principal-azure.md)必要がある場合は、Azure AD テナントにアプリケーションを登録し、データ ソースでサービス プリンシパルへのアクセスを付与する必要があります。 Azure AD 全体管理者やアプリケーション管理者などの他のロールで、この操作を実行できます。

##### <a name="getting-the-service-principals-application-id"></a>サービス プリンシパルのアプリケーション ID の取得

1. 既に作成されている [_サービス プリンシパル_](./create-service-principal-azure.md)の **[概要]** にある **アプリケーション (クライアント) ID** をコピーします

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-appl-id.png" alt-text="サービス プリンシパルのアプリケーション (クライアント) ID を示すスクリーンショット":::

##### <a name="granting-the-service-principal-access-to-your-adls-gen1-account"></a>ADLS Gen1 アカウントへのアクセス権をサービス プリンシパルに付与する

ADLS Gen2 データ ソースをスキャンするアクセス許可をサービス プリンシパルに付与することは重要です。 スキャンのアクセス許可を付与する対象に応じて、サブスクリプション、リソースグループ、またはリソースのレベルに Catalog の MSI を追加できます。

> [!Note]
> Azure リソースにサービス プリンシパルを追加できるようにするには、サブスクリプションの所有者である必要があります。

1. ストレージ アカウントを開き、**概要** --> **データ エクスプローラー** をクリックして、ストレージ アカウントへのサービス プリンシパルのアクセス権を付与します

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-data-explorer.png" alt-text="ストレージ アカウントを示すスクリーンショット":::

1. 上部ナビゲーションの **[アクセス]** をクリックします

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-storage-access.png" alt-text="ストレージ アカウントのデータ エクスプローラーを示すスクリーンショット":::

1. **[選択]** をクリックし、 **[ユーザーまたはグループの選択]** の選択肢で _[サービス プリンシパル]_ を追加します。
1. **[読み取り]** と **[実行]** のアクセス許可を選択します。 [追加先] オプションで、 **[このフォルダーとすべての子]** および **[アクセス許可エントリと既定のアクセス許可エントリ]** を必ず選択してください。 [設定] メニューの **[OK]**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-permissions.png" alt-text="サービス プリンシパルにアクセス許可を割り当てる方法の詳細を示すスクリーンショット":::

### <a name="creating-the-scan"></a>スキャンの作成

1. **Purview アカウント** を開き、 **[Purview Studio を開く]** をクリックします

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-purview-acct.png" alt-text="[Purview Studio を開く] を示すスクリーンショット":::

1. **[データ マップ]**  -->  **[ソース]** に移動してコレクション階層を表示します

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-open-purview-studio.png" alt-text="コレクション階層を示すスクリーンショット":::

1. 前に登録した **ADLS Gen1 データ ソース** の下にある **[新しいスキャン]** アイコンをクリックします

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-new-scan.png" alt-text="[新しいスキャン] アイコンが表示されたデータ ソースを示すスクリーンショット":::

#### <a name="if-using-managed-identity"></a>マネージド ID を使用した場合

スキャンの **名前** を指定し、 **[資格情報]** で **[Purview MSI]** を選択し、スキャンに適したコレクションを選択して、 **[テスト接続]** をクリックします。 接続に成功したら **[続行]** をクリックします。

:::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-managed-identity.png" alt-text="スキャンを実行する [マネージド ID] オプションを示すスクリーンショット":::

#### <a name="if-using-service-principal"></a>サービス プリンシパルを使用する場合

1. スキャンの **名前** を指定し、スキャンに適したコレクションを選択して、 **[資格情報]** で **[+ 新規]** をクリックします

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp.png" alt-text="サービス プリンシパル オプションを示すスクリーンショット":::

1. _サービス プリンシパル_ の作成時に使用した適切な **キー コンテナー接続** と **シークレット名** を選択します。 上に示したように、**サービス プリンシパル ID** は、コピーされた **アプリケーション (クライアント) ID** です

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-key-vault.png" alt-text="サービス プリンシパルのキー コンテナー オプションを示すスクリーンショット":::

1. **[テスト接続]** をクリックします。 接続に成功したら **[続行]** をクリックします

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-test-connection.png" alt-text="サービス プリンシパルのテスト接続を示すスクリーンショット":::

### <a name="scoping-and-running-the-scan"></a>スキャンのスコープと実行

1. 特定のフォルダーおよびサブフォルダーをリストから選んで、スキャンのスコープに指定できます。

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scope-scan.png" alt-text="スキャンの範囲を指定する":::

1. 次に、スキャン ルール セットを選択します。 システムの既定のものを選択するか、既存のカスタム ルール セットを使用するか、新しいルール セットをインラインで作成することができます。

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-rule-set.png" alt-text="スキャン ルール セット":::

1. 新しい _スキャン ルール セット_ を作成する場合は、スキャン ルールに含める **ファイルの種類** を選択します。 

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-file-types.png" alt-text="スキャン ルール セットのファイルの種類":::

1. スキャン ルールに含める **分類ルール** を選択できます

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-classification-rules.png" alt-text="スキャン ルール セットの分類ルール":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-select-scan-rule-set.png" alt-text="スキャン ルール セットの選択":::

1. スキャン トリガーを選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-trigger.png" alt-text="スキャン トリガー":::

    :::image type="content" source="media/register-scan-adls-gen1/register-register-adls-gen1-scan-trigger-selection.png" alt-text="スキャン トリガーの選択":::

1. スキャンを確認し、 **[保存および実行]** を選択します。

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-review-scan.png" alt-text="スキャンの確認":::

### <a name="viewing-scan"></a>スキャンの表示

1. _[コレクション]_ 内の _データ ソース_ に移動し、 **[詳細の表示]** をクリックしてスキャンの状態を確認します

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-view-scan.png" alt-text="スキャンの表示":::

1. スキャンの詳細には、**最終実行状態** でのスキャンの進行状況と、_スキャン_ および _分類_ されたアセットの数が示されます

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-details.png" alt-text="スキャンの詳細の表示":::

1. スキャン全体が正常に実行されると、**最後の実行状態** が **[進行中]** に更新され、その後 **[完了]** に更新されます

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-in-progress.png" alt-text="進行中のスキャンの表示":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-completed.png" alt-text="完了したスキャンの表示":::

### <a name="managing-scan"></a>スキャンの管理

スキャンは、完了時に管理したり、再度実行したりできます。

1. **スキャン名** をクリックしてスキャンを管理します

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-manage-scan.png" alt-text="スキャンの管理":::

1. _スキャンを再実行_ したり、_スキャンを編集_ したり、_スキャンを削除_ したりできます  

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-manage-scan-options.png" alt-text="スキャン オプションの管理":::

    > [!NOTE]
    > * スキャンを削除しても、以前のスキャンから作成されたカタログ アセットは削除されません。
    > * Purview の [スキーマ] タブの説明を編集した後に、ソース テーブルが変更され、ソース テーブルを再スキャンした場合、アセットはスキーマの変更によって更新されなくなります。

1. _増分スキャンを実行_ することも、_フル スキャン_ を再度実行することもできます。

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-full-inc-scan.png" alt-text="スキャンの完全または増分の管理":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-manage-scan-results.png" alt-text="スキャン結果の管理":::

## <a name="next-steps"></a>次のステップ

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)

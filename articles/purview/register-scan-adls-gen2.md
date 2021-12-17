---
title: Azure Data Lake Storage (ADLS) Gen2 の登録とスキャン
description: この記事では、Azure Data Lake Storage Gen2 ソースを認証して操作する手順を含め、Azure Purview に Azure Data Storage Lake Storage Gen2 データ ソースを登録するプロセスについて説明します
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 18076fa234650f5d176f2487e2c0ff487a61bb49
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842001"
---
# <a name="connect-to-azure-data-lake-gen2-in-azure-purview"></a>Connect Purview で Azure Data Lake Gen2 にアクセスする

この記事では、Azure Data Lake Storage Gen2 ソースを認証して操作する手順を含め、Azure Purview に Azure Data Storage Lake Storage Gen2 データ ソースを登録するプロセスについて説明します

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register) | [あり](#scan)|[あり](#scan) | [あり](#scan)|[あり](#scan)| はい | 制限あり** |

\** データセットが [Data Factory Copy アクティビティ](how-to-link-azure-data-factory.md)でソース/シンクとして使用される場合、系列はサポートされています 

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

## <a name="register"></a>登録

このセクションでは、データ ソースのADLS Gen2を登録し、適切な認証メカニズムを設定して、データ ソースが正常にスキャンされたことを確認できます。

### <a name="steps-to-register"></a>登録する手順

データ ソースのスキャンを設定する前に、Azure Purview にデータ ソースを登録することが重要です。

1. [Azure portal](https://portal.azure.com) にアクセスし、 **[Purview アカウント]** ページに移動して、自分の _Purview アカウント_ をクリックします

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-purview-acct.png" alt-text="データ ソースの登録に使用する Purview アカウントを示すスクリーンショット":::

1. **Purview Studio を開き**、 **[Data Map] > [ソース]** の順に移動します

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-open-purview-studio.png" alt-text="Purview Studio を開くリンクを示すスクリーンショット":::

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-sources.png" alt-text="Data Map の [ソース] リンクへの移動を示すスクリーンショット":::

1. **コレクション** メニューを使用して [コレクション階層](./quickstart-create-collection.md)を作成し、必要に応じて個々のサブコレクションへのアクセス許可を割り当てます

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-collections.png" alt-text="コレクション階層を作成するコレクション メニューを示すスクリーンショット":::

1. **[ソース]** メニューの適切なコレクションに移動し、 **[登録]** アイコンをクリックして新しいデータ ソースADLS Gen2を登録します

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-register-source.png" alt-text="データ ソースの登録に使用するコレクションを示すスクリーンショット":::

1. Azure **Data Lake Storage Gen2 データ ソースを選択し、[** 続行] を **クリックします**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-select-data-source.png" alt-text="データ ソースの選択を示すスクリーンショット":::

1. データ ソースに適切な **名前** を指定し、関連する **Azure サブスクリプション**、既存の **Data Lake Store アカウント名**、**コレクション** を選択し、 **[適用]** をクリックします

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-data-source-details.png" alt-text="データ ソースを登録するために入力する詳細を示すスクリーンショット":::

1. 選択ADLS Gen2ストレージ アカウントの下にストレージ アカウントが表示されます

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-data-source-collection.png" alt-text="スキャンを開始するためにコレクションにマップされたデータ ソースを示すスクリーンショット":::

## <a name="scan"></a>スキャン

### <a name="prerequisites-for-scan"></a>スキャンの前提条件

データ ソースをスキャンするアクセス権を持つには、ADLS Gen2 Storageアカウントの認証方法を構成する必要があります。
次のオプションがサポートされています。

> [!Note]
> ストレージ アカウントに対してファイアウォールが有効になっている場合は、スキャンを設定するときに、 [マネージド ID] の認証方法を使用する必要があります。

* **マネージド ID (推奨)** - Azure Purview アカウントが作成されるとすぐに、システムの **マネージド ID** が Azure AD テナント内に自動的に作成されます。 Azure Purview MSI でスキャンを実行するには、リソースの種類に応じて、特定の RBAC ロールを割り当てる必要があります。

* **アカウント キー** - Azure Purview へのアクセスを有効にし、シークレットを使用してデータソースを安全にスキャンするために、Azure Key Vault 内にシークレットを作成して資格情報を格納できます。 シークレットには、ストレージ アカウント キー、SQL ログイン パスワード、またはパスワードを指定できます。

   > [!Note]
   > このオプションを使用した場合、サブスクリプションに _Azure キー コンテナー_ リソースをデプロイし、_Azure キー コンテナー_ 内のシークレットへの必要なアクセス許可を持つ _Azure Purview アカウントの_ MSI を割り当てる必要があります。

* **サービス プリンシパル** - この方法では、Azure Active Directory テナントで新しく作成することも、既存のサービス プリンシパルを使用することもできます。

### <a name="authentication-for-a-scan"></a>スキャンの認証

#### <a name="using-managed-identity-for-scanning"></a>スキャンでのマネージド ID の使用

Purview アカウントに、データ ソースをスキャンするアクセス許可を付与ADLS Gen2重要です。 スキャンのアクセス許可を付与する対象に応じて、サブスクリプション、リソースグループ、またはリソースのレベルに Catalog の MSI を追加できます。

> [!Note]
> Azure リソースにマネージド ID を追加できるようにするには、サブスクリプションの所有者である必要があります。

1. [Azure portal](https://portal.azure.com)から、カタログのスキャンを許可するサブスクリプション、リソース グループ、またはリソース (Azure Data Lake Storage Gen2 ストレージ アカウントなど) を検索します。

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-storage-acct.png" alt-text="ストレージ アカウントを示すスクリーンショット":::

1. 左側のナビゲーション **Access Control (IAM)** をクリックし、[+ ロールの割り当ての **追加**]  --> **をクリックします**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-access-control.png" alt-text="ストレージ アカウントのアクセス制御を示すスクリーンショット":::

1. [ロール]**を [BLOB** **Storage閲覧者]** に設定し、[入力ボックスの選択] に _Azure Purview_ アカウント **名を** 入力します。 次に、 **[保存]** を選択して、このロールの割り当てを Purview アカウントに付与します。

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-assign-permissions.png" alt-text="Purview アカウントにアクセス許可を割り当てる方法の詳細を示すスクリーンショット":::

> [!Note]
> 詳細については、「[Azure Active Directory を使用して BLOB とキューへのアクセスを承認する](../storage/blobs/authorize-access-azure-active-directory.md)」の手順を参照してください

> [!NOTE]
> ストレージ アカウントに対してファイアウォールが有効になっている場合は、スキャンを設定するときに、 **[マネージド ID]** の認証方法を使用する必要があります。

1. [Azure portal](https://portal.azure.com) で ADLS Gen2 ストレージ アカウントに移動します
1. **[セキュリティとネットワーク] に移動>ネットワーク**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-networking.png" alt-text="ファイアウォール アクセスを提供する詳細を示すスクリーンショット":::

1. **[許可するアクセス元]** の **[選択されたネットワーク]** を選択します

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-network-access.png" alt-text="選択したネットワークへのアクセスを許可する詳細を示すスクリーンショット":::

1. **[例外]** セクションで、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** を選択し、 **[保存]** を選択します

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-permission-microsoft-services.png" alt-text="信頼されたユーザーがストレージ アカウントにアクセスMicrosoft サービス例外を示すスクリーンショット":::

#### <a name="using-account-key-for-scanning"></a>スキャンにアカウント キーを使用する

選択した認証方法が **アカウント キー** の場合は、アクセス キーを取得して、キー コンテナーに格納する必要があります。

1. ADLS Gen2 ストレージ アカウントに移動する
1. **[セキュリティとネットワーク] > [アクセス キー]** を選択します

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-access-keys.png" alt-text="ストレージ アカウント内のアクセス キーを示すスクリーンショット":::

1. *キー* をコピーし、次の手順のために個別に保存します

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-key.png" alt-text="コピーするアクセス キーを示すスクリーンショット":::

1. お使いのキー コンテナーに移動する

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-key-vault.png" alt-text="キー コンテナーを示すスクリーンショット":::

1. [設定 >**シークレット] を** 選択し **、[+ 生成/** インポート] をクリックして、シークレットを生成するキー コンテナー オプションを 
:::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-generate-secret.png" alt-text="示すスクリーンショット":::

1. ストレージ アカウント **の** キー **として[** 名前] *と [値* ] を入力します

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-secret-values.png" alt-text="シークレット値を入力するキー コンテナー オプションを示すスクリーンショット":::

1. **[作成]** を選択して完了します

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-secret.png" alt-text="シークレットを生成するキー コンテナー オプションを示すスクリーンショット":::

1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、キーを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します

#### <a name="using-service-principal-for-scanning"></a>スキャンでのサービス プリンシパルの使用

##### <a name="creating-a-new-service-principal"></a>新しいサービス プリンシパルの作成

[新しいサービス プリンシパルを作成する](./create-service-principal-azure.md)必要がある場合は、Azure AD テナントにアプリケーションを登録し、データ ソースでサービス プリンシパルへのアクセスを付与する必要があります。 Azure AD 全体管理者やアプリケーション管理者などの他のロールで、この操作を実行できます。

##### <a name="getting-the-service-principals-application-id"></a>サービス プリンシパルのアプリケーション ID の取得

1. 既に作成されている [_サービス プリンシパル_](./create-service-principal-azure.md)の **[概要]** にある **アプリケーション (クライアント) ID** をコピーします

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-sp-appln-id.png" alt-text="サービス プリンシパルのアプリケーション (クライアント) ID を示すスクリーンショット":::

##### <a name="granting-the-service-principal-access-to-your-adls-gen2-account"></a>サービス プリンシパルに自分のアカウントへのアクセス権ADLS Gen2付与する

ADLS Gen2 データ ソースをスキャンするアクセス許可をサービス プリンシパルに付与することは重要です。 スキャンのアクセス許可を付与する対象に応じて、サブスクリプション、リソースグループ、またはリソースのレベルに Catalog の MSI を追加できます。

> [!Note]
> Azure リソースにサービス プリンシパルを追加できるようにするには、サブスクリプションの所有者である必要があります。

1. [Azure portal](https://portal.azure.com)から、カタログのスキャンを許可するサブスクリプション、リソース グループ、またはリソース (Azure Data Lake Storage Gen2 ストレージ アカウントなど) を検索します。

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-storage-acct.png" alt-text="ストレージ アカウントを示すスクリーンショット":::

1. 左側のナビゲーション **Access Control (IAM)** をクリックし、[+ ロールの割り当ての **追加**]  --> **をクリックします**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-access-control.png" alt-text="ストレージ アカウントのアクセス制御を示すスクリーンショット":::

1. [ロール]**を [BLOB** **Storage閲覧者] に設定** し、[入力の選択] ボックス _にサービス_ プリンシパル **を** 入力します。 次に、 **[保存]** を選択して、このロールの割り当てを Purview アカウントに付与します。

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-sp-permission.png" alt-text="サービス プリンシパルにストレージ アカウントのアクセス許可を提供するための詳細を示すスクリーンショット":::

### <a name="create-the-scan"></a>スキャンを作成する

1. **Purview アカウント** を開き、 **[Purview Studio を開く]** をクリックします
1. **[データ マップ]**  -->  **[ソース]** に移動してコレクション階層を表示します
1. 前に登録 **したデータ ソース** の下にある **[ADLS Gen2 スキャン]** アイコンをクリックします

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-new-scan.png" alt-text="新しいスキャンを作成する画面を示すスクリーンショット":::

#### <a name="if-using-managed-identity"></a>マネージド ID を使用した場合

1. スキャンの **名前** を指定し、 **[資格情報]** で **[Purview MSI]** を選択し、スキャンに適したコレクションを選択して、 **[テスト接続]** をクリックします。 接続に成功したら **[続行]** をクリックします

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-managed-identity.png" alt-text="スキャンを実行するマネージド ID オプションのスクリーンショット":::

#### <a name="if-using-account-key"></a>アカウント キーを使用している場合

1. スキャンの **名前を** 指定し、スキャンに適したコレクションを選択し、アカウント キーとして **[認証方法]** を _選択します_

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-acct-key.png" alt-text="スキャン用の [アカウント キー] オプションを示すスクリーンショット":::

#### <a name="if-using-service-principal"></a>サービス プリンシパルを使用する場合

1. スキャンの **名前** を指定し、スキャンに適したコレクションを選択して、 **[資格情報]** で **[+ 新規]** をクリックします

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-sp-option.png" alt-text="スキャンを有効にするサービス プリンシパル オプションのスクリーンショット":::

1. _サービス プリンシパル_ の作成時に使用した適切な **キー コンテナー接続** と **シークレット名** を選択します。 サービス **プリンシパル ID** は、前にコピーした **アプリケーション (クライアント) ID** です。

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-service-principal-option.png" alt-text="サービス プリンシパル オプションを示すスクリーンショット":::

1. **[テスト接続]** をクリックします。 接続に成功したら **[続行]** をクリックします

### <a name="scope-and-run-the-scan"></a>スキャンのスコープと実行

1. 特定のフォルダーおよびサブフォルダーをリストから選んで、スキャンのスコープに指定できます。

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-scope-scan.png" alt-text="スキャンの範囲を指定する":::

1. 次に、スキャン ルール セットを選択します。 システムの既定のものを選択するか、既存のカスタム ルール セットを使用するか、新しいルール セットをインラインで作成することができます。

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-scan-rule-set.png" alt-text="スキャン ルール セット":::

1. 新しい _スキャン ルール セット_ を作成する場合は、スキャン ルールに含める **ファイルの種類** を選択します。

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-file-types.png" alt-text="スキャン ルール セットのファイルの種類":::

1. スキャン ルールに含める **分類ルール** を選択できます

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-classification rules.png" alt-text="スキャン ルール セットの分類ルール":::

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-select-scan-rule-set.png" alt-text="スキャン ルール セットの選択":::

1. スキャン トリガーを選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-scan-trigger.png" alt-text="スキャン トリガー":::

1. スキャンを確認し、 **[保存および実行]** を選択します。

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-review-scan.png" alt-text="スキャンの確認":::

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次の手順

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)

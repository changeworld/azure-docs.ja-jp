---
title: Azure Data Lake Storage (ADLS) Gen1 の登録とスキャン
description: このチュートリアルでは、Azure Data Lake Storage Gen1 からのデータをスキャンして Azure Purview に取り込む方法について説明します。
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: 1c4801814e77efdb681f32ea35d4dfb68618900b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736371"
---
# <a name="register-and-scan-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 の登録とスキャン

この記事では、Azure Purview にデータ ソースとして Azure Data Lake Storage Gen1 を登録し、そのスキャンを設定する方法について説明します。

> [!Note]
> Azure Data Lake Storage Gen2 の一般提供が開始されました。 今すぐ使用を開始することをお勧めします。 詳細については、[製品に関するページ](https://azure.microsoft.com/services/storage/data-lake-storage/)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

Azure Data Lake Storage Gen1 データ ソースでは、以下の機能がサポートされています。

- Data Lake Storage Gen1 のメタデータと分類をキャプチャする **完全および増分スキャン**

- ADF コピーまたはデータフロー アクティビティのための、データ資産間の **データ系列**

csv、tsv、psv、ssv などのファイルの種類では、次のロジックが適用されている場合にスキーマが抽出されます。

1. 最初の行の値が空ではない
2. 最初の行の値が一意である
3. 最初の行の値が日付でも数値でもない

## <a name="prerequisites"></a>前提条件

- データ ソースを登録する前に、Azure Purview アカウントを作成します。 Purview アカウントの作成の詳細については、[Azure Purview アカウントの作成](create-catalog-portal.md)に関するクイックスタートを参照してください。
- Azure Purview データ ソース管理者である必要があります

## <a name="setting-up-authentication-for-a-scan"></a>スキャンの認証の設定

Azure Data Lake Storage Gen1 では、次の認証方法がサポートされています。

- マネージド ID
- サービス プリンシパル

### <a name="managed-identity-recommended"></a>マネージド ID (推奨)

使いやすさとセキュリティのために、Purview MSI を使用してデータ ストアに対する認証を行うことをお勧めします。

Data Catalog の MSI を使用してスキャンを設定するには、まず、データ ソースをスキャンするためのアクセス許可を Purview アカウントに付与する必要があります。 この手順を行って "*から*" スキャンを設定する必要があります。そうしないと、スキャンは失敗します。

#### <a name="adding-the-purview-msi-to-an-azure-data-lake-storage-gen1-account"></a>Azure Data Lake Storage Gen1 アカウントへの Purview MSI の追加

スキャンのアクセス許可を付与する対象に応じて、サブスクリプション、リソースグループ、またはリソースのレベルに Catalog の MSI を追加できます。

> [!Note]
> Azure リソースにマネージド ID を追加できるようにするには、サブスクリプションの所有者である必要があります。

1. [Azure portal](https://portal.azure.com) で、カタログによるスキャンを許可するサブスクリプション、リソース グループ、またはリソース (Azure Data Lake Storage Gen1 ストレージ アカウントなど) のいずれかを検索します。

2. **[概要]** をクリックして、 **[データ エクスプローラー]** を選択します。

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="アクセス制御を選択する":::

3. 上部ナビゲーションの **[追加]** をクリックします。

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="[アクセス]"::: をクリックします。

4. **[追加]** をクリックします。 [ユーザーまたはグループの選択] の選択肢の中から **Purview カタログ** を追加します。 **[読み取り]** と **[実行]** のアクセス許可を選択します。 下のスクリーンショットに示すように、[追加先] オプションで、 **[このフォルダーとすべての子]** および **[アクセス許可エントリと既定のアクセス許可エントリ]** を必ず選択してください。 **[OK]** をクリックします。
   :::image type="content" source="./media/register-scan-adls-gen1/gen1-managed-service-identity-authentication.png" alt-text="MSI 認証の詳細":::
   
> [!Tip]
> **アクセス許可エントリ** とは、"*現在*" のファイルとフォルダーに対するアクセス許可エントリです。
> **既定のアクセス許可エントリ** とは、新しいファイルとフォルダーによって "*継承*" されるアクセス許可エントリです。
> 
> 現在存在するファイルにのみアクセス許可を付与するには、**アクセス許可エントリを選択します**。
> 
> 今後追加されるファイルやフォルダーをスキャンするアクセス許可を付与するには、**既定のアクセス許可エントリを含めます**。
> 
> 詳細については、[アクセス許可のドキュメント](../data-lake-store/data-lake-store-access-control.md#default-permissions-on-new-files-and-folders)を参照してください。

5. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。

6. 最後に、サービス プリンシパルを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します

> [!Note]
> データ ソースにカタログの MSI を追加したら、アクセス許可が反映されるまで最大 15 分間待機してから、スキャンを設定します。

約 15 分後には、リソースをスキャンできるようにするための適切なアクセス許可がカタログに付与されます。

### <a name="service-principal"></a>サービス プリンシパル

サービス プリンシパルを使用するには、まず、それを以下の手順に従って作成する必要があります。

1. [Azure Portal](https://portal.azure.com) に移動します。

2. 左側のメニューで、 **[Azure Active Directory]** を選択します。

3. **[アプリの登録]** を選択します。

4. **[+ 新しいアプリケーションの登録]** を選択します。

5. **アプリケーション** の名前 (サービス プリンシパル名) を入力します。

6. **[この組織のディレクトリ内のアカウントのみ]** を選択します。

7. **[リダイレクト URI]** で **[Web]** を選択し、必要な URL を入力します。これは実際の URL である必要はなく、機能しなくてもかまいません。

8. 次に、 **[登録]** を選択します。

9. 表示名とアプリケーション ID の両方から値をコピーします。

#### <a name="adding-the-purview-service-principal-to-an-azure-data-lake-storage-gen1-account"></a>Azure Data Lake Storage Gen1 アカウントへの Purview サービス プリンシパルの追加

1. [Azure portal](https://portal.azure.com) で、カタログによるスキャンを許可するサブスクリプション、リソース グループ、またはリソース (Azure Data Lake Storage Gen1 ストレージ アカウントなど) のいずれかを検索します。

2. **[概要]** をクリックして、 **[データ エクスプローラー]** を選択します。

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="アクセス制御を選択する":::

3. 上部ナビゲーションの **[追加]** をクリックします。

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="[アクセス]"::: をクリックします。

4. **[追加]** をクリックします。 [ユーザーまたはグループの選択] の選択肢の中から **サービス プリンシパル アプリケーション** を追加します。 **[読み取り]** と **[実行]** のアクセス許可を選択します。 下のスクリーンショットに示すように、[追加先] オプションで、 **[このフォルダーとすべての子]** および **[アクセス許可エントリと既定のアクセス許可エントリ]** を必ず選択してください。 **[OK]** をクリックします。
   :::image type="content" source="./media/register-scan-adls-gen1/gen1-service-principal-permissions.png" alt-text="サービス プリンシパルの認証の詳細":::

> [!Tip]
> **アクセス許可エントリ** とは、"*現在*" のファイルとフォルダーに対するアクセス許可エントリです。
> **既定のアクセス許可エントリ** とは、新しいファイルとフォルダーによって "*継承*" されるアクセス許可エントリです。
>
> 現在存在するファイルにのみアクセス許可を付与するには、**アクセス許可エントリを選択します**。
>
> 今後追加されるファイルやフォルダーをスキャンするアクセス許可を付与するには、**既定のアクセス許可エントリを含めます**。
>
> 詳細については、[アクセス許可のドキュメント](../data-lake-store/data-lake-store-access-control.md#default-permissions-on-new-files-and-folders)を参照してください。

5. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。

6. 最後に、サービス プリンシパルを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します。

## <a name="register-azure-data-lake-storage-gen1-data-source"></a>Azure Data Lake Storage Gen1 データ ソースを登録する

新しい ADLS Gen1 アカウントをデータ カタログに登録するには、次の手順を行います。

1. ご利用の Purview Data Catalog に移動します。
2. 左側のナビゲーションで **[Data Map]** を選択します。
3. **[登録]** を選択します
4. **[ソースの登録]** で、 **[Azure Data Lake Storage Gen1]** を選択します。 
5. **[続行]** を選択します

[ソースの登録 (Azure Data Lake Storage Gen1)] 画面で、次の手順を行います。

1. データ ソースがカタログに表示されるときの **名前** を入力します。
2. サブスクリプションを選択して、ストレージ アカウントをフィルター処理します。
3. ストレージ アカウントを選択します。
4. コレクションを選択するか、新しいものを作成します (省略可能)。
5. **[登録]** を選択してデータ ソースを登録します。

:::image type="content" source="media/register-scan-adls-gen1/register-sources.png" alt-text="ソースの登録のオプション" border="true":::

## <a name="creating-and-running-a-scan"></a>スキャンを作成し、実行する

新しいスキャンを作成して実行するには、次の操作を行います。

1. Purview Studio の左側にあるペインで **[Data Map]** タブを選択します。

1. 登録した Azure Data Lake Storage Gen1 ソースを選択します。

1. **[新しいスキャン]** を選択します。

1. 対象のデータ ソースに接続するための資格情報を選択します。

   :::image type="content" source="media/register-scan-adls-gen1/set-up-scan-adls-gen1.png" alt-text="スキャンを設定する":::

1. 特定のフォルダーおよびサブフォルダーをリストから選んで、スキャンのスコープに指定できます。

   :::image type="content" source="media/register-scan-adls-gen1/gen1-scope-your-scan.png" alt-text="スキャンの範囲を指定する":::

1. 次に、スキャン ルール セットを選択します。 システムの既定のものを選択するか、既存のカスタム ルール セットを使用するか、新しいルール セットをインラインで作成することができます。

   :::image type="content" source="media/register-scan-adls-gen1/select-scan-rule-set.png" alt-text="スキャン ルール セット":::

1. スキャン トリガーを選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

   :::image type="content" source="media/register-scan-adls-gen1/trigger-scan.png" alt-text="trigger":::

1. スキャンを確認し、 **[保存および実行]** を選択します。

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)

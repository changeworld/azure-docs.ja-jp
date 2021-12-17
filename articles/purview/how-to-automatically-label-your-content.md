---
title: Azure Purview のデータに秘密度ラベルを自動的に適用する方法
description: 秘密度ラベルを作成し、スキャン中にデータに自動的に適用する方法について説明します。
author: ajaykar
ms.author: ajaykar
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 6bcb751c58b4ba14e84f529ec268beacc93fdcf4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339563"
---
# <a name="how-to-automatically-apply-sensitivity-labels-to-your-data-in-azure-purview"></a>Azure Purview のデータに秘密度ラベルを自動的に適用する方法

## <a name="create-or-extend-existing-sensitivity-labels-to-azure-purview"></a>Azure Purview で秘密度ラベルを作成するか、既存のものを拡張する

> [!IMPORTANT]
> Azure Purview 秘密度ラベルは、現在プレビューの段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。
>

秘密度ラベルがまだない場合は、それを作成して、Azure Purview で使用できるようにする必要があります。 既存の秘密度ラベルを変更して、Azure Purview で使用できるようにすることもできます。

### <a name="step-1-licensing-requirements"></a>ステップ 1: ライセンスの要件

秘密度ラベルは、Microsoft 365 コンプライアンス センターで作成および管理します。 Azure Purview で使用する秘密度ラベルを作成するには、秘密度ラベルの自動適用機能という利点を持つ、アクティブな Microsoft 365 ライセンスを保持している必要があります。

ライセンスの完全な一覧については、[Azure Purview における秘密度ラベルの FAQ](sensitivity-labels-frequently-asked-questions.yml) に関するページを参照してください。 必要なライセンスをまだ保持していない場合は、[Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion) の試用版にサインアップできます。

### <a name="step-2-consent-to-use-sensitivity-labels-in-azure-purview"></a>ステップ 2: Azure Purview で秘密度ラベルを使用することに同意する

次のステップでは、秘密度ラベルを拡張し、ファイルやデータベースの列に秘密度ラベルを適用できる Azure Purview で使用できるようにします。

1. Microsoft 365 で、 **[Information Protection]** ページに移動します。</br>
   Information Protection のためのサブスクリプションをプロビジョニングしたのが最近の場合、**Information Protection** ページが表示されるまで数時間かかることがあります。
1. **[Azure Purview の資産へのラベル付けを拡張する]** の領域で、 **[有効にする]** ボタンを選択し、表示される確認ダイアログで **[はい]** を選択します。

次に例を示します。

:::image type="content" source="media/how-to-automatically-label-your-content/extend-sensitivity-labels-to-purview-small.png" alt-text="[有効にする] ボタンを選択して、秘密度ラベルを Purview に拡張します" lightbox="media/how-to-automatically-label-your-content/extend-sensitivity-labels-to-purview.png":::

:::image type="content" source="media/how-to-automatically-label-your-content/extend-sensitivity-labels-to-purview-confirmation-small.png" alt-text="秘密度ラベルを Purview に拡張する選択を確定します" lightbox="media/how-to-automatically-label-your-content/extend-sensitivity-labels-to-purview-confirmation.png":::。

> [!TIP]
>このボタンが表示されず、Purview の資産にラベル付けを拡張することの同意が承認されたかどうかわからない場合は、[この FAQ](sensitivity-labels-frequently-asked-questions.yml#how-can-i-determine-if-consent-has-been-granted-to-extend-labeling-to-purview) 項目で状態を判断する方法を確認してください。
>

Azure Purview の資産にラベル付けを拡張すると、すべての公開済みの秘密度ラベルを Purview で使用できるようになります。

### <a name="step-3-create-or-modify-existing-label-to-automatically-label-content"></a>ステップ 3: コンテンツに自動的にラベルを付けるよう、ラベルを作成するか既存のラベルを変更する

**新しい秘密度ラベルを作成または既存のラベルを変更するには**:

1. [Microsoft 365 コンプライアンス センター](https://compliance.microsoft.com/)を開きます。

1. **[ソリューション]** で、 **[Information Protection]** を選択し、 **[ラベルの作成]** を選択します。

    :::image type="content" source="media/how-to-automatically-label-your-content/create-sensitivity-label-full-small.png" alt-text="Microsoft 365 コンプライアンス センターで秘密度ラベルを作成する" lightbox="media/how-to-automatically-label-your-content/create-sensitivity-label-full.png":::

1. ラベルに名前を付けます。 その後、 **[Define the scope for this label]\(このラベルのスコープを定義\)** で、次を実行します。

    - すべての場合において、 **[Azure Purview assets]\(Azure Purview の資産\)** を選択します。
    - ファイルにラベルを付けるには、 **[ファイル & 電子メール]** も選択します。 このオプションは、データベース資産にのみラベルを付ける場合は必要ありません

    :::image type="content" source="media/how-to-automatically-label-your-content/create-label-scope-small.png" alt-text="Microsoft 365 コンプライアンス センターで自動的にラベル付けを行う" lightbox="media/how-to-automatically-label-your-content/create-label-scope.png":::

1. 残りの指示に従って、ラベルの設定を構成します。

    特にファイルとデータベースの列の自動ラベル付け規則を定義します。

    - [ファイルの自動ラベル付け規則を定義する](#autolabeling-for-files)
    - [データベースの列の自動ラベル付け規則を定義する](#autolabeling-for-database-columns)

    構成オプションの詳細については、Microsoft 365 のドキュメントの「[機密ラベルでできること](/microsoft-365/compliance/sensitivity-labels#what-sensitivity-labels-can-do)」を参照してください。

1. 上記の手順を繰り返して、追加のラベルを作成します。

    サブラベルを作成するには、親ラベル > **...**  >  **[その他のアクション]**  >  **[サブ ラベルの追加]** を選択します。

1. 既存のラベルを変更するには、 **[Information Protection]**  >  **[ラベル]** を参照して、ラベルを選択します。

    次に、 **[ラベルの編集]** を選択して、ラベルの作成時に定義したすべての設定を使用して、**秘密度ラベルの編集** の構成を再度開きます。

    :::image type="content" source="media/how-to-automatically-label-your-content/edit-sensitivity-label-full-small.png" alt-text="既存の秘密度ラベルを編集します" lightbox="media/how-to-automatically-label-your-content/edit-sensitivity-label-full.png":::

1. すべてのラベルの作成が完了したら、ラベルの順序を表示し、必要に応じて順序を変更します。

    ラベルの順序を変更するには、 **...** > **[その他のアクション]**  >  **[上へ移動]** または **[下へ移動]** を選択します。

    詳細については、Microsoft 365 のドキュメントの「[ラベルの優先度 (順序の問題)](/microsoft-365/compliance/sensitivity-labels#label-priority-order-matters)」を参照してください。

#### <a name="autolabeling-for-files"></a>ファイルの自動ラベル付け

ファイルの自動ラベル付け規則は、ラベルを作成または編集するときに定義します。

**[Office アプリの自動ラベル付け]** ページで、 **[Office アプリの自動ラベル付け]** を有効にして、データにラベルを自動的に適用する条件を定義します。

例:

:::image type="content" source="media/how-to-automatically-label-your-content/create-auto-labeling-rules-files-small.png" alt-text="Microsoft 365 コンプライアンス センターでファイルの自動ラベル付け規則を定義する" lightbox="media/how-to-automatically-label-your-content/create-auto-labeling-rules-files.png":::

詳細については、Microsoft 365 のドキュメントの[データへの秘密度ラベルの自動適用](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps)に関するページを参照してください。

#### <a name="autolabeling-for-database-columns"></a>データベース列の自動ラベル付け

データベースの列の自動ラベル付け規則は、ラベルを作成または編集するときに定義します。

**データベース列** オプションの下で、以下を行います:

1. **[データベースの列の自動ラベル付け]** スライダーを選択します。

1. **[機密情報の種類の選択]** を選択して、ラベルに適用する機密情報の種類を選択します。

例:

:::image type="content" source="media/how-to-automatically-label-your-content/create-auto-labeling-rules-db-columns-small.png" alt-text="Microsoft 365 コンプライアンス センターで SQL 列の自動ラベル付け規則を定義する" lightbox="media/how-to-automatically-label-your-content/create-auto-labeling-rules-db-columns.png":::

### <a name="step-4-publish-labels"></a>ステップ 4: ラベルを発行する

ラベルを作成したら、定義した自動ラベル付け規則に基づいて、作成したラベルが自動的に適用されるよう、Azure Purview でデータをスキャンする必要があります。

## <a name="scan-your-data-to-apply-sensitivity-labels-automatically"></a>データをスキャンして秘密度ラベルを自動的に適用する

Azure Purview でデータをスキャンすると、定義した自動ラベル付け規則に基づいて、作成したラベルが自動的に適用されます。 秘密度ラベルの変更が Purview に反映されるまで、最大で 24 時間かかります。

Azure Purview のさまざまな資産でスキャンを設定する方法の詳細については、以下を参照してください。

|source  |リファレンス  |
|---------|---------|
|**ストレージ内のファイル** | [Azure Blob Storage の登録とスキャン](register-scan-azure-blob-storage-source.md) </br> [Azure Files の登録とスキャン](register-scan-azure-files-storage-source.md) [Azure Data Lake Storage Gen1 の登録とスキャン](register-scan-adls-gen1.md) </br>[Azure Data Lake Storage Gen2 の登録とスキャン](register-scan-adls-gen2.md)</br>[Amazon S3 の登録とスキャン](register-scan-amazon-s3.md) |
|**データベースの列** | [Azure SQL Database の登録とスキャン](register-scan-azure-sql-database.md) </br>[Azure SQL Database Managed Instance の登録とスキャン](register-scan-azure-sql-database-managed-instance.md) </br> [専用 SQL プールの登録とスキャン](register-scan-azure-synapse-analytics.md)</br> [Azure Synapse Analytics ワークスペースを登録してスキャンする](register-scan-azure-synapse-analytics.md) </br> [Azure Cosmos Database (SQL API) の登録とスキャン](register-scan-azure-cosmos-database.md) </br> [Azure MySQL データベースの登録とスキャン](register-scan-azure-mysql-database.md) </br> [Azure database for PostgreSQL の登録とスキャン](register-scan-azure-postgresql.md) |
| | |

## <a name="view-labels-on-assets-in-the-catalog"></a>カタログ内の資産のラベルを表示する

Microsoft 365 でラベルの自動ラベル付け規則を定義し、Azure Purview でデータをスキャンすると、ラベルが資産に自動的に適用されます。

**Azure Purview Catalog の資産に適用されているラベルを表示するには:**

Azure Purview Catalog で、 **[ラベル]** フィルター オプションを使用して、特定のラベルを持つ資産のみを表示します。 例:

:::image type="content" source="media/how-to-automatically-label-your-content/filter-search-results-small.png" alt-text="ラベルで資産を検索します" lightbox="media/how-to-automatically-label-your-content/filter-search-results.png":::

見つかった分類や適用されたラベルを含む資産の詳細を表示するには、結果で資産をクリックします。

例:

:::image type="content" source="media/how-to-automatically-label-your-content/view-labeled-files-blob-storage-small.png" alt-text="Azure Blob Storage 内のファイルの秘密度ラベルを表示します" lightbox="media/how-to-automatically-label-your-content/view-labeled-files-blob-storage.png":::

## <a name="view-insight-reports-for-the-classifications-and-sensitivity-labels"></a>分類と秘密度ラベルの分析情報レポートを表示する

Azure Purview の分類されてラベル付けされたデータに関する分析情報を取得するには、**分類** と **秘密度ラベル** のレポートを使用します。

> [!div class="nextstepaction"]
> [分類の分析情報](./classification-insights.md)

> [!div class="nextstepaction"]
> [秘密度ラベルの分析情報](sensitivity-insights.md)

> [!div class="nextstepaction"]
> [Azure Purview でのラベル付けの概要](create-sensitivity-label.md)

> [!div class="nextstepaction"]
> [ラベル付けについてよく寄せられる質問](sensitivity-labels-frequently-asked-questions.yml)
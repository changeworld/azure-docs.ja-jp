---
title: Azure Data Factory に接続する
description: この記事では、データ系列を追跡するために Azure Data Factory と Azure Purview を接続する方法について説明します。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/01/2021
ms.openlocfilehash: 2ba2c135a0b07df6c6a4802c241b064b2ef94335
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851491"
---
# <a name="how-to-connect-azure-data-factory-and-azure-purview"></a>Azure Data Factory と Azure Purview を接続する方法

このドキュメントでは、データ系列を追跡するために Azure Data Factory アカウントと Azure Purview アカウントを接続するために必要な手順について説明します。 また、対象範囲スコープとサポートされる系列パターンの詳細についても説明します。

## <a name="view-existing-data-factory-connections"></a>既存の Data Factory 接続を表示する

系列情報をプッシュするために、複数の Azure Data Factory を 1 つの Azure Purview に接続できます。 現在の制限では、Purview の管理センターから一度に最大 10 個の Data Factory アカウントを接続できます。 Purview アカウントに接続されている Data Factory アカウントの一覧を表示するには、次のことを行います。

1. 左側のナビゲーション ウィンドウで **[管理]** を選択します。
2. **[Lineage connections]\(系列接続\)** で、 **[Data Factory]** を選択します。
3. Data Factory 接続の一覧が表示されます。

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="Data Factory 接続の一覧を示すスクリーンショット。" lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

4. 次の接続の **[状態]** のさまざまな値に注意してください。

    - **接続中**: データ ファクトリは Purview アカウントに接続されています。
    - **切断**: Data Factory はカタログにアクセスできますが、別のカタログに接続されています。 その結果、データ系列がこのカタログに自動的には報告されません。
    - **アクセス不可**: 現在のユーザーは Data Factory にアクセスできないため、接続の状態は不明です。

>[!Note]
>Data Factory の接続を表示するには、次のロールが割り当てられている必要があります。 管理グループからのロールの継承はサポートされていません。
>ルート コレクションに対する **コレクション管理者** ロール。

## <a name="create-new-data-factory-connection"></a>新しい Data Factory 接続を作成する

>[!Note]
>Data Factory の接続を追加または削除するには、次のロールが割り当てられている必要があります。 管理グループからのロールの継承はサポートされていません。
>ルート コレクションに対する **コレクション管理者** ロール。
>
> さらに、ユーザーはデータ ファクトリの "所有者" または "共同作成者" である必要があります。

既存のデータ ファクトリを Purview アカウントに接続するには、次の手順に従います。 [Data Factory を ADF から Purview アカウントに接続する](../data-factory/connect-data-factory-to-azure-purview.md)こともできます。

1. 左側のナビゲーション ウィンドウで **[管理]** を選択します。
2. **[Lineage connections]\(系列接続\)** で、 **[Data Factory]** を選択します。
3. **[Data Factory connection] (Data Factory 接続)** ページで **[新規]** を選択します。

4. 一覧から Data Factory アカウントを選択し、 **[OK]** を選択します。 この一覧を制限するために、サブスクリプション名でフィルター処理することもできます。

    :::image type="content" source="./media/how-to-link-azure-data-factory/connect-data-factory.png" alt-text="Azure Data Factory を接続する方法を示すスクリーンショット。" lightbox="./media/how-to-link-azure-data-factory/connect-data-factory.png":::

    Data Factory が現在の Purview アカウントに既に接続されているか、または Data Factory にマネージド ID がない場合は、一部の Data Factory インスタンスが無効になっている可能性があります。

    選択されている Data Factory のいずれかが他の Purview アカウントに既に接続されている場合は、警告メッセージが表示されます。 [OK] を選択すると、他の Purview アカウントとの Data Factory 接続は切断されます。 追加の確認は必要ありません。

    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="Azure Data Factory を切断するという警告を示すスクリーンショット。":::

>[!Note]
>一度に 10 個までのデータ ファクトリの追加がサポートされるようになりました。 一度に 10 個を超えるデータ ファクトリを追加する場合は、サポート チケットを提出してください。

### <a name="how-authentication-works"></a>認証のしくみ

データ ファクトリのマネージド ID は、データ ファクトリから Purview への系列のプッシュ操作を認証するために使用されます。 UI 上で Purview にデータ ファクトリを接続すると、ロールの割り当てが自動的に追加されます。

データ ファクトリのマネージド ID に、Purview **ルート コレクション** の **データ キュレーター** ロールを付与します。 詳細については、[Azure Purview でのアクセス制御](../purview/catalog-permissions.md)および[コレクションを使用したロールの追加とアクセスの制限](../purview/how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections)に関連するページを参照してください。

### <a name="remove-data-factory-connections"></a>Data Factory 接続を削除する

Data Factory 接続を削除するには、次の操作を行います。

1. **[Data Factory connection] (Data Factory 接続)** ページで、1 つ以上の Data Factory 接続の横にある **[削除]** ボタンを選択します。
2. ポップアップの **[確認]** を選択して、選択されている Data Factory 接続を削除します。

    :::image type="content" source="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png" alt-text="接続を削除する Data Factory を選択する方法を示すスクリーンショット。" lightbox="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png":::

## <a name="supported-azure-data-factory-activities"></a>サポートされる Azure Data Factory アクティビティ

Azure Purview は、次の Azure Data Factory アクティビティからランタイムの系列をキャプチャします。

- [[データのコピー]](../data-factory/copy-activity-overview.md)
- [データ フロー](../data-factory/concepts-data-flow-overview.md)
- [SSIS パッケージの実行](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)

> [!IMPORTANT]
> ソースまたは宛先でサポートされていないデータ ストレージ システムが使用されている場合、Azure Purview は系列を削除します。

以降のセクションで説明されているように、Data Factory と Purview の間の統合では、Data Factory がサポートするデータ システムのサブセットのみがサポートされています。

[!INCLUDE[data-factory-supported-lineage-capabilities](includes/data-factory-common-supported-capabilities.md)]

### <a name="execute-ssis-package-support"></a>SSIS パッケージの実行のサポート

[サポートされるデータ ストア](how-to-lineage-sql-server-integration-services.md#supported-data-stores)を参照してください。

## <a name="access-secured-azure-purview-account"></a>セキュリティで保護された Azure Purview アカウントにアクセスする
      
Purview アカウントがファイアウォールで保護されている場合、Purview プライベート エンドポイントを利用して、[セキュリティで保護されている Purview アカウントに Data Factory でアクセスする](../data-factory/how-to-access-secured-purview-account.md)方法について説明します。

## <a name="bring-data-factory-lineage-into-purview"></a>Data Factory の系列を Purview に取り込む

エンドツーエンドのチュートリアルについては、「[チュートリアル: Data Factory 系列データを Azure Purview にプッシュする](../data-factory/turorial-push-lineage-to-purview.md)」に従ってください。

## <a name="supported-lineage-patterns"></a>サポートされる系列パターン

Azure Purview でサポートされる系列のパターンはいくつかあります。 生成される系列データは、Data Factory アクティビティで使用されるソースとシンクの種類に基づいています。 Data Factory は 80 を超えるソースとシンクをサポートしていますが、Azure Purview では、「[サポートされる Azure Data Factory アクティビティ](#supported-azure-data-factory-activities)」に示されているサブセットのみがサポートされています。

系列情報を送信するように Data Factory を構成するには、「[系列の使用を開始する](catalog-lineage-user-guide.md#get-started-with-lineage)」を参照してください。

系列ビューの情報を検索するための他の方法には、次のようなものがあります。

- **[系列]** タブで、図形をマウスでポイントすると、そのアセットに関する追加情報がツールヒントにプレビューされます。
- ノードまたはエッジを選択すると、それが属するアセットの種類が表示されるか、またはアセットが切り替えられます。
- **[系列]** タブの左側には、データセットの列が表示されます。列レベルの系列の詳細については、「[データセット列の系列](catalog-lineage-user-guide.md#dataset-column-lineage)」を参照してください。

### <a name="data-lineage-for-11-operations"></a>1:1 の操作のデータ系列

データ系列をキャプチャするための最も一般的なパターンは、1 つの入力データセットから 1 つの出力データセットに、その間に 1 つのプロセスを使用してデータを移動することです。

このパターンの例には、次のものがあります。

- 1 つのソース/入力: *Customer* (SQL テーブル)
- 1 つのシンク/出力: *Customer1.csv* (Azure BLOB)
- 1 つのプロセス: *CopyCustomerInfo1\#Customer1.csv* (Data Factory のコピー アクティビティ)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage.png" alt-text="1:1 の Data Factory のコピー操作の系列を示すスクリーンショット。":::

### <a name="data-movement-with-11-lineage-and-wildcard-support"></a>1:1 の系列とワイルドカードのサポートを使用したデータ移動

系列をキャプチャするためのもう 1 つの一般的なシナリオは、ワイルドカードを使用して、1 つの入力データセットから 1 つの出力データセットにファイルをコピーすることです。 ワイルドカードにより、コピー アクティビティはファイル名の共通の部分を使用して、コピーする複数のファイルを一致させることができます。 Azure Purview は、対応するコピー アクティビティによってコピーされた個々のファイルのファイル レベルの系列をキャプチャします。

このパターンの例には、次のものがあります。

* ソース/入力: *CustomerCall\*.csv* (ADLS Gen2 パス)
* シンク/出力: *CustomerCall\*.csv* (Azure BLOB ファイル)
* 1 つのプロセス: *CopyGen2ToBlob\#CustomerCall.csv* (Data Factory のコピー アクティビティ)  

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png" alt-text="ワイルドカードのサポートを使用した 1:1 のコピー操作の系列を示すスクリーンショット。" lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png":::

### <a name="data-movement-with-n1-lineage"></a>n:1 の系列を使用したデータ移動

Data Flow アクティビティを使用してデータ操作 (merge や join など) を実行できます。 複数のソース データセットを使用してターゲット データセットを生成できます。 この例では、Azure Purview は、個々の入力ファイルのファイル レベルの系列を Data Flow アクティビティの一部である SQL テーブルにキャプチャします。

このパターンの例には、次のものがあります。

* 2 つのソース/入力: *Customer.csv*、*Sales.parquet* (ADLS Gen2 パス)
* 1 つのシンク/出力: *Company data* (Azure SQL テーブル)
* 1 つのプロセス: *DataFlowBlobsToSQL* (Data Factory Data Flow アクティビティ)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png" alt-text="n:1 の A D F Data Flow 操作の系列を示すスクリーンショット。" lightbox="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png":::

### <a name="lineage-for-resource-sets"></a>リソース セットの系列

リソース セットは、基になるストレージ内の多数のパーティション ファイルを表す、カタログ内の論理オブジェクトです。 詳細については、「[リソース セットについて](concept-resource-sets.md)」を参照してください。 Azure Purview は、Azure Data Factory から系列をキャプチャするときに、個々のパーティション ファイルを正規化して 1 つの論理オブジェクトを作成するためのルールを適用します。

次の例では、Azure BLOB から Azure Data Lake Gen2 リソース セットが生成されます。

* 1 つのソース/入力: *Employee\_management.csv* (Azure BLOB)
* 1 つのシンク/出力: *Employee\_management.csv* (Azure Data Lake Gen 2)
* 1 つのプロセス: *CopyBlobToAdlsGen2\_RS* (Data Factory のコピー アクティビティ)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png" alt-text="リソース セットの系列を示すスクリーンショット。" lightbox="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png":::

## <a name="next-steps"></a>次のステップ

[チュートリアル: Data Factory 系列データを Azure Purview にプッシュする](../data-factory/turorial-push-lineage-to-purview.md)

[カタログ系列ユーザー ガイド](catalog-lineage-user-guide.md)

[系列のための Azure Data Share へのリンク](how-to-link-azure-data-share.md)
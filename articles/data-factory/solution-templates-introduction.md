---
title: テンプレートの概要
description: 定義済みのテンプレートを使用して Azure Data Factory の使用をすぐに開始する方法について説明します。
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.author: susabat
author: ssabat
ms.custom: seo-lt-2019
ms.date: 08/24/2021
ms.openlocfilehash: 5d07ecedbc8fe4ff0670c9e8aa96fdf2a92359d3
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123098951"
---
# <a name="templates"></a>テンプレート

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

テンプレートは、Azure Data Factory の使用をすぐに開始できる定義済みの Data Factory パイプラインです。 テンプレートは、Data Factory を初めて使用するときに、すぐに作業を開始したい場合に役立ちます。 これらのテンプレートはデータ統合プロジェクトを構築するための開発時間を短縮するため、開発者の生産性が向上します。

## <a name="create-data-factory-pipelines-from-templates"></a>テンプレートから Data Factory パイプラインを作成する

次の 2 つの方法で、テンプレートから Data Factory パイプラインの作成を開始できます。

1.  Data Factory のホーム ページの **[その他を参照]** セクションで、 **[パイプラインのテンプレート]** を選択してテンプレート ギャラリーを開きます。

    ![Data Factory ホームページからテンプレート ギャラリーを開くスクリーンショット。](media/doc-common-process/home-page-pipeline-templates-tile.png)

1.  リソース エクスプローラーの [作成者] タブで、 **+** を選択し、**テンプレートからのパイプライン** を選択してテンプレート ギャラリーを開きます。

    ![[作成者] タブからテンプレート ギャラリーを開くスクリーンショット。](media/solution-templates-introduction/templates-introduction-image-2.png)

## <a name="template-gallery"></a>テンプレート ギャラリー

![テンプレート ギャラリー ページのスクリーンショット。](media/solution-templates-introduction/templates-introduction-image-3.png)

### <a name="out-of-the-box-data-factory-templates"></a>すぐに利用できる Data Factory テンプレート

Data Factory では、データ ファクトリ パイプライン テンプレートを保存するために Azure Resource Manager テンプレートが使用されています。 [公式の Azure Data Factory GitHub リポジトリ](https://github.com/Azure/Azure-DataFactory/tree/master/templates)で、すべての Resource Manager テンプレートと、すぐに利用できる Data Factory テンプレートで使用されるマニフェスト ファイルを確認できます。 Microsoft が提供している定義済みのテンプレートには、以下の項目が含まれていますが、これらに限定されているわけではありません。

-   コピー テンプレート:

    -   [データベースからの一括コピー](solution-template-bulk-copy-with-control-table.md)
    
    -   [LastModifiedDate を基準にした新しいファイルのコピー](solution-template-copy-new-files-lastmodifieddate.md)

    -   [ファイルベースのストア間での複数のファイル コンテナーのコピー](solution-template-copy-files-multiple-containers.md)

    -   [ファイルの移動](solution-template-move-files.md)

    -   [データベースからの差分コピー](solution-template-delta-copy-with-control-table.md)

    -   \<source\> から \<destination\> へのコピー

        -   [Amazon S3 から Azure Data Lake Store Gen 2 へ](solution-template-migration-s3-azure.md)

        -   Google Big Query から Azure Data Lake Store Gen 2 へ

        -   HDF から Azure Data Lake Store Gen 2 へ

        -   Netezza から Azure Data Lake Store Gen 1 へ

        -   オンプレミスの SQL Server から Azure SQL Database へ

        -   オンプレミスの SQL Server から Azure Synapse Analytics へ

        -   オンプレミスの Oracle から Azure Synapse Analytics へ

-   SSIS テンプレート

    -   SSIS パッケージを実行する Azure-SSIS Integration Runtime のスケジュールの設定

-   変換テンプレート

    -   [Azure Databricks を使用した ETL](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>マイ テンプレート

[パイプライン] タブで **[テンプレートとして保存]** を選択することで、パイプラインをテンプレートとして保存できます。

![パイプラインをテンプレートとして保存する方法を示すスクリーンショット。](media/solution-templates-introduction/templates-introduction-image-4.png)

**テンプレート ギャラリー** ページで **[マイ テンプレート]** にチェックを入れると、このページの右ペインにテンプレートとして保存されたパイプラインが表示されるようになります。 

![[マイ テンプレート] ウィンドウのスクリーンショット。](media/solution-templates-introduction/templates-introduction-image-5.png)

> [!NOTE]
> マイ テンプレート機能を使用するには、Git 統合を有効にする必要があります。 Azure DevOps Git と GitHub の両方がサポートされています。

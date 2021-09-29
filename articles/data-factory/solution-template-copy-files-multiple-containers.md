---
title: 複数のコンテナーからのファイルのコピー
description: Azure Data Factory を使用して複数のコンテナーからファイルをコピーするための、ソリューション テンプレート の使用方法について説明します。
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: d1d1ffaecdf57817e5ef7be48791d0cb50ca9b74
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743541"
---
# <a name="copy-multiple-folders-with-azure-data-factory"></a>Azure Data Factory で複数のフォルダーをコピーする

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、複数のコピー アクティビティを使用して、ファイルベースのストア間でコンテナーまたはフォルダーをコピーするソリューション テンプレートについて説明します。ここでは、各コピー アクティビティが 1 つのコンテナーまたはフォルダーをコピーすることが想定されています。 

> [!NOTE]
> 1 つのコンテナーからファイルをコピーする場合は、[データのコピー ツール](copy-data-tool.md)を使用して、単一のコピー アクティビティを含んだパイプラインを作成する方が効率的です。 この記事に示すテンプレートは、そのシンプルなシナリオで必要とするものより大きくなっています。

## <a name="about-this-solution-template"></a>このソリューション テンプレートについて

このテンプレートは、ソース ストレージ ストア上の指定された親フォルダーからフォルダーを列挙します。 次に、各フォルダーをコピー先のストアにコピーします。

このテンプレートには、3 つのアクティビティが含まれています。
- **GetMetadata** はソース ストレージ ストアをスキャンし、指定された親フォルダーからサブフォルダーの一覧を取得します。
- **ForEach** は、**GetMetadata** アクティビティからサブフォルダーの一覧を取得し、その一覧を反復処理して、各フォルダーを Copy アクティビティに渡します。
- **Copy** は、各フォルダーをソース ストレージ ストアからコピー先ストアにコピーします。

このテンプレートでは、次のパラメーターを定義します。
- *SourceFileFolder* は、データ ソース ストア *SourceFileFolder/SourceFileDirectory* の親フォルダー パスの一部で、ここでサブフォルダーの一覧を取得できます。 
- *SourceFileDirectory* は、データ ソース ストア *SourceFileFolder/SourceFileDirectory* の親フォルダー パスの一部で、ここでサブフォルダーの一覧を取得できます。 
- *DestinationFileFolder* は、親フォルダー パス *DestinationFileFolder/DestinationFileDirectory* の一部で、宛先ストアにファイルがコピーされる場所です。 
- *DestinationFileDirectory* は、親フォルダー パス *DestinationFileFolder/DestinationFileDirectory* の一部で、宛先ストアにファイルがコピーされる場所です。 

ルート フォルダーにある複数のコンテナーをストレージ ストア間でコピーする場合は、4 つのすべてのパラメーターを */* として入力できます。 これにより、ストレージ ストア間ですべてがレプリケートされます。

## <a name="how-to-use-this-solution-template"></a>このソリューション テンプレートの使用方法

1. **Copy multiple files containers between File Stores** テンプレートに移動します。 ソース ストレージ ストアへの **[新規]** 接続を作成します。 ソース ストレージ ストアは、複数のコンテナーからファイルをコピーする場所 (コピー元) です。

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png" alt-text="ソースへの新しい接続を作成する":::

2. コピー先ストレージ ストアへの **[新規]** 接続を作成します。

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png" alt-text="コピー先への新しい接続を作成する":::

3. **[このテンプレートを使用]** を選択します。

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png" alt-text="このテンプレートを使用":::
    
4. 次の例に示すように、パイプラインが表示されます。

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png" alt-text="パイプラインを表示する":::

5. **[デバッグ]** を選択し、 **[パラメーター]** で入力し、 **[完了]** を選択します。

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png" alt-text="パイプラインを実行する":::

6. 結果を確認します。

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png" alt-text="結果を確認する":::

## <a name="next-steps"></a>次のステップ

- [Azure Data Factory で制御テーブルを使用してデータベースから一括コピーを行う](solution-template-bulk-copy-with-control-table.md)

- [Azure Data Factory を使用して複数のコンテナーからファイルをコピーする](solution-template-copy-files-multiple-containers.md)

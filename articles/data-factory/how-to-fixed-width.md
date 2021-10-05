---
title: Azure Data Factory のマッピング データ フローを使用して固定長テキスト ファイルを処理する
description: Azure Data Factory のマッピング データ フローを使用して固定長テキスト ファイルを処理する方法について説明します。
author: kromerm
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: 58bd77eb815a510328c49ca7c614b0bc680a4b94
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128571430"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Data Factory のマッピング データ フローを使用して固定長テキスト ファイルを処理する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Microsoft Azure Data Factory のマッピング データ フローを使用すると、固定幅のテキスト ファイルからデータを変換できます。 次のタスクでは、区切り記号を使用せずにテキスト ファイルのデータセットを定義し、次に序数の位置に基づいて部分文字列の分割を設定します。

## <a name="create-a-pipeline"></a>パイプラインを作成する

1. **[+ 新しいパイプライン]** を選択して新しいパイプラインを作成します。

2. 固定幅ファイルの処理に使用されるデータ フロー アクティビティを追加します。

    :::image type="content" source="media/data-flow/fwpipe.png" alt-text="固定幅パイプライン":::

3. データ フロー アクティビティで、 **[New mapping data flow]\(新しいマッピング データ フロー\)** を選択します。

4. ソース、派生列、選択、およびシンクの変換を追加します。

    :::image type="content" source="media/data-flow/fw2.png" alt-text="固定幅データ フロー":::

5. 新しいデータ セットを使用するようにソース変換を構成します。これは区切りテキスト型になります。

6. 列区切り記号またはヘッダーは設定しないでください。

   ここで、このファイルの内容のフィールドの開始点と長さを設定します。

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. ソース変換の **[プロジェクション]** タブには、*Column_1* という名前の文字列型の列が表示されます。

8. 派生列で新しい列を作成します。

9. 列には、*col1* のように簡単な名前を付けます。

10. 式ビルダーで、次のように入力します。

    `substring(Column_1,1,4)`

    :::image type="content" source="media/data-flow/fwderivedcol1.png" alt-text="派生列":::

11. 解析する必要があるすべての列について、手順 10 を繰り返します。

12. **[Inspect]\(検査\)** タブを選択すると、生成される新しい列が表示されます。

    :::image type="content" source="media/data-flow/fwinspect.png" alt-text="検査":::

13. SELECT 変換を使用して、変換に必要のない列をすべて削除します。

    :::image type="content" source="media/data-flow/fwselect.png" alt-text="SELECT 変換":::

14. シンクを使用して、データをフォルダーに出力します。

    :::image type="content" source="media/data-flow/fwsink.png" alt-text="固定幅シンク":::

    出力は次のようになります。

    :::image type="content" source="media/data-flow/fxdoutput.png" alt-text="固定幅出力":::

  固定幅データがそれぞれ 4 文字で分割され、Col1、Col2、Col3、Col4 などに割り当てられます。 前の例に基づいて、データは 4 つの列に分割されます。

## <a name="next-steps"></a>次のステップ

* マッピング データ フローの[変換](concepts-data-flow-overview.md)を使用して、残りのデータ フロー ロジックを構築します。

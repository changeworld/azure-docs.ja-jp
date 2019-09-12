---
title: Azure Data Factory の Mapping Data Flow を使用して固定長テキスト ファイルを処理する
description: Azure Data Factory の Mapping Data Flow を使用して固定長テキスト ファイルを処理する方法について説明します。
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: e1ba09f459152616941071c23f7a6545ec2a8b73
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210509"
---
# <a name="process-fixed-length-text-files-using-data-factory-mapping-data-flows"></a>Data Factory の Mapping Data Flow を使用して固定長テキスト ファイルを処理する

Data Factory の Mapping Data Flow では、固定幅テキスト ファイルからのデータの変換がサポートされます。 区切り記号を使用せずにテキスト ファイルのデータセットを定義し、次に序数の位置に基づいて部分文字列の分割を設定します。

## <a name="create-a-pipeline"></a>パイプラインを作成する。

1. **[+New Pipeline]\(+新しいパイプライン\)** に移動して、新しいパイプラインを開始します

2. 固定幅ファイルの処理に使用されるデータ フロー アクティビティを追加します

  ![固定幅パイプライン](media/data-flow/fwpipe.png)

3. データ フロー アクティビティで、[New Mapping Data Flow]\(新しい Mapping Data Flow\) を選択します

4. ソース変換、派生列、SELECT、およびシンク変換を追加します

  ![固定幅データ フロー](media/data-flow/fw2.png)

5. 区切りテキスト型となる新しいデータ セットを使用するようにソース変換を構成します

6. 列区切り記号とヘッダーは設定しません

単にこのファイル コンテンツのフィールドの開始点と長さを設定します。

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

7. ソース変換の [Projection]\(プロジェクション\) タブに、"Column_1" という文字列が 1 つ表示されます

8. 次に派生列で新しい列を作成します

9. 列には、col1 のように単純な名前を付けます

10. 次に、式ビルダーで、次のように入力します。

  ```substring(Column_1,1,4)```

  ![派生列](media/data-flow/fwderivedcol1.png)

10. 解析する必要があるすべての列について、これを繰り返します

12. [Inspect]\(検査\) タブをクリックすると、生成される新しい列が表示されます

  ![検査](media/data-flow/fwinspect.png)

13. SELECT 変換を使用して、変換に必要のない列をすべて削除します

  ![SELECT 変換](media/data-flow/fwselect.png)

14. 最後に、シンクを使用して、データをフォルダーに出力します。

  ![固定幅シンク](media/data-flow/fwsink.png)

  出力は次のようになります。

  ![固定幅出力](media/data-flow/fxdoutput.png)

  固定幅データがそれぞれ 4 文字で分割され、Col1、Col2、Col3、Col4、... に割り当てられます。上記の例に基づいて、データを 4 列に分割します。

## <a name="next-steps"></a>次の手順

* Mapping Data Flow の[変換](concepts-data-flow-overview.md)を使用して、残りのデータ フロー ロジックを構築します

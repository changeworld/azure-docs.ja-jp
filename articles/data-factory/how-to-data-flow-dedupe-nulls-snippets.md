---
title: データ フロー スニペットを使用して行の重複を除去し、null を見つける
description: データ フロー内でコード スニペットを使用して、簡単に行の重複を除去し、null を見つける方法について説明します
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: 841484a647d2737d621c75ebe63f65f2de829a26
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666502"
---
# <a name="dedupe-rows-and-find-nulls-using-data-flow-snippets"></a>データ フロー スニペットを使用して行の重複を除去し、null を見つける

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

マッピング データ フロー内でコード スニペットを使用すると、データの重複除去や null のフィルター処理などの一般的なタスクを、非常に簡単に実行できます。 この攻略ガイドでは、データ フローのスクリプト スニペットを使用して非常に簡単に、それらの関数をパイプラインに追加する方法について説明します。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>パイプラインを作成する

1. **[+ 新しいパイプライン]** を選択して新しいパイプラインを作成します。

2. データ フロー アクティビティを追加します。

3. ソース変換を追加し、それをデータセットの 1 つに接続します

    ![ソース スニペット 2](media/data-flow/snippet-adf-2.png)

4. 重複除去と NULL チェックのスニペットでは、データ フロー スキーマの誤差を活用する汎用のパターンを使用するので、データセットのどのスキーマを使用しても、定義済みのスキーマがまったくないデータセットを使用しても機能します。

5. [データ フロー スクリプトに関するドキュメントのページに移動し、個別の行のためのコード スニペットをコピーします。](https://docs.microsoft.com/azure/data-factory/data-flow-script#distinct-row-using-all-columns)

6. データ フロー デザイナーの UI で、右上にある [スクリプト] ボタンをクリックして、データ フロー グラフの背後にあるスクリプト エディターを開きます。

    ![ソース スニペット 3](media/data-flow/snippet-adf-3.png)

7. スクリプトの ```source1``` の定義の後で、Enter キーを押してからコード スニペットを貼り付けます。

8. 貼り付けたコードの前に「source1」と入力することで、貼り付けたこのコード スニペットを、グラフ内に作成した以前のソース変換に接続します。

9. または、グラフ内の新しい変換のノードから受信ストリームを選択しても、デザイナー内で新しい変換を接続できます。

    ![ソース スニペット 4](media/data-flow/snippet-adf-4.png)

10. これで、データ フローでは、すべての列値にわたって汎用のハッシュを適用してすべての行をグループ化する集計変換を使用して、ソースからの重複行の削除が行われます。
    
11. 次に、NULL がある行を含むストリームと、NULL がないストリームにデータを分割するためのコード スニペットを追加します。

12. [スニペットのライブラリに戻り、今回は、NULL チェック用のコードをコピーします。](https://docs.microsoft.com/azure/data-factory/data-flow-script#check-for-nulls-in-all-columns)

13. データ フロー デザイナーで、もう一度 [スクリプト] をクリックし、この新しい変換コードを最下部に貼り付けます。貼り付けたスニペットは、その前にその変換の名前を入力して、以前の変換に接続します。

14. データ フロー グラフはこれで次のようになるはずです。

    ![ソース スニペット 1](media/data-flow/snippet-adf-1.png)

  これで、データ フロー スクリプト ライブラリから既存のコード スニペットを取得し、それらを既存の設計に追加することで、汎用の重複除去と NULL チェックを備えた機能するデータ フローができました。

## <a name="next-steps"></a>次のステップ

* マッピング データ フローの[変換](concepts-data-flow-overview.md)を使用して、残りのデータ フロー ロジックを構築します。

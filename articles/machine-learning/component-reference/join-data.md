---
title: 'Join Data (データの結合): コンポーネント リファレンス'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーの Join Data (データの結合) コンポーネントを使用して、2 つのデータセットをマージする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: a19ae7de4355e5b4d2d8ce4379ef3359edd55f07
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565807"
---
# <a name="join-data"></a>データの結合

この記事では、Azure Machine Learning デザイナーの **[Join Data]\(データの結合\)** コンポーネントを使用して、データベーススタイルの結合操作を用いて 2 つのデータセットをマージする方法について説明します。  

## <a name="how-to-configure-join-data"></a>[Join Data]\(データの結合\) を構成する方法

2 つのデータセットに対して結合を実行するには、それらをキー列で関連付ける必要があります。 複数の列を使用した複合キーもサポートされています。 

1. 結合するデータセットを追加してから、 **[Join Data]\(データの結合\)** コンポーネントをパイプラインにドラッグします。 

    このコンポーネントは、 **[Data Transformation]\(データの変換\)** カテゴリの **[Manipulation]\(操作\)** 内にあります。

1. データセットを **[Join Data]\(データの結合\)** コンポーネントに接続します。 
 
1. **[Launch column selector]\(列セレクターの起動\)** を選択してキー列を選択します。 必ず左右両方の入力に列を選択してください。

    単一キーの場合:

    両方の入力に対して単一のキー列を選択します。
    
    複合キーの場合:

    左の入力と右の入力のすべてのキー列を同じ順序で選択します。 すべてのキー列が一致すると、 **[Join Data]\(データの結合\)** コンポーネントによってテーブルが結合されます。 列の順序が元のテーブルと同じでない場合は、オプション **[Allow duplicates and preserve column order in selection]\(重複を許可し、選択内容の列の順序を維持する\)** をオンにします。 

    ![列セレクター](media/module/join-data-column-selector.png)


1. テキスト列の結合で大文字と小文字の区別を維持する場合は、 **[大文字と小文字を区別する]** オプションをオンにします。 
   
1. **[結合の種類]** ドロップ ダウン リストを使用してデータセットの結合方法を指定します。  
  
    * **内部結合**:"*内部結合*" は最も一般的な結合操作です。 キー列の値が一致する場合にのみ、結合された行が返されます。  
  
    * **左外部結合**:"*左外部結合*" では、左側のテーブルのすべての行について結合された行が返されます。 左側のテーブルの行に右側のテーブルの一致する行がない場合、返される行には、右側のテーブルのすべての列の欠損値が含まれます。 欠損値の代わりの値を指定することもできます。  
  
    * **完全外部結合**:"*完全外部結合*" では、左側のテーブル (**table1**) と右側のテーブル (**table2**) のすべての行が返されます。  
  
         いずれかのテーブルの各行に、もう一方のテーブルと一致する行がない場合、結果には欠損値を含む行が含まれます。  
  
    * **左半結合**:"*左半結合*" では、キー列の値が一致すると、左側のテーブルの値のみが返されます。  

1. オプション **[Keep right key columns in joined table]\(結合テーブルの右側のキー列を維持する\)** の場合:

    * 両方の入力テーブルのキーを表示するには、このオプションを選択します。
    * 左側の入力のキー列のみを返すには、オフにします。

1. パイプラインを送信します。

1. 結果を表示するには、 **[Join Data]\(データの結合\)** を右クリックし、 **[可視化]** を選択します。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のコンポーネント](component-reference.md)を参照してください。 
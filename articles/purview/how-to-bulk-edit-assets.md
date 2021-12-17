---
title: 資産を一括編集して、分類、用語集の用語にタグを付け、連絡先を変更する方法
description: Azure Purview で資産を一括編集する方法について説明します。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 511c104080acf27e233fecfa3fae7efa814b9bba
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133464"
---
# <a name="how-to-bulk-edit-assets-to-annotate-classifications-glossary-terms-and-modify-contacts"></a>資産を一括編集して、分類、用語集の用語に注釈を付け、連絡先を変更する方法

この記事では、1 回の操作で、選択された資産の一覧の複数の用語集の用語、分類、所有者、およびエキスパートにタグ付けする方法について説明します。

## <a name="add-assets-to-view-selected-list-using-search"></a>検索を使用して資産を [選択対象の表示] リストに追加する

1. 一括編集用の一覧に追加するデータ資産を検索します。

1. 検索結果ページで、一括編集の **[選択対象の表示]** 一覧に追加する資産の上にマウス ポインターを移動すると、チェックボックスが表示されます。

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-checkbox.png" alt-text="チェックボックスのスクリーンショット。":::

1. チェックボックスをオンにして、一括編集の **[選択対象の表示]** 一覧にそれを追加します。 追加されると、ページの下部に選択された項目アイコンが表示されます。

   :::image type="content" source="media/how-to-bulk-edit-assets/selected-list.png" alt-text="一覧のスクリーンショット。":::

1. 上記の手順を繰り返して、すべてのデータ資産を一覧に追加します。

## <a name="add-assets-to-view-selected-list-from-asset-detail-page"></a>資産の詳細ページから [選択対象の表示] リストに資産を追加する

また、資産の詳細ページにある一括編集リストで資産を追加することもできます。 右上隅にあるチェックボックスをオンにして、一括編集リストに資産を追加します。

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-list.png" alt-text="資産のスクリーンショット。":::

## <a name="bulk-edit-assets-in-the-view-selected-list-to-add-replace-or-remove-glossary-terms"></a>[選択対象の表示] 一覧で資産を一括編集して、用語集の用語を追加、置換、または削除します。

1. 一括編集が必要なすべてのデータ資産の特定が完了したら、検索結果ページまたは資産の詳細ページから **[選択対象の表示]** 一覧を選択します。

    :::image type="content" source="media/how-to-bulk-edit-assets/view-list.png" alt-text="表示のスクリーンショット。":::

1. 一覧から用語を削除する場合は、リストを確認してから、**[選択解除]** を選択します。

    :::image type="content" source="media/how-to-bulk-edit-assets/remove-list.png" alt-text="選択解除ボタンが強調表示されているスクリーンショット。":::

1. 選択したすべての資産で用語集の用語を追加、削除、または置換するには、**[一括編集]** を選択します。

    :::image type="content" source="media/how-to-bulk-edit-assets/bulk-edit.png" alt-text="一括編集ボタンが強調表示されているスクリーンショット。":::

1. 用語集の用語を追加するには、操作として **[追加]** を選択します。 [新しい値] で、追加するすべての用語集の用語を選択します。 完了したら、[適用] を選択します。
    - [追加] 操作により、既にデータ資産にタグ付けされている用語集の用語の一覧に新しい値が追加されます。  
   
    :::image type="content" source="media/how-to-bulk-edit-assets/add-list.png" alt-text="追加のスクリーンショット。":::

1. 用語集の用語を置き換えるには、操作として **[Replace with]\(書き換え\)** を選択します。 [新しい値] で、置き換えるすべての用語集の用語を選択します。 完了したら、[適用] を選択します。
    - 置き換え操作によって、選択したデータ資産で、すべての用語集の用語が、[新しい値] で選択された用語に置き換えられます。
   
1. 用語集の用語を削除するには、操作として **[削除]** を選択します。 完了したら、[適用] を選択します。
    - 削除操作によって、選択したデータ資産で、すべての用語集の用語が削除されます。
   
    :::image type="content" source="media/how-to-bulk-edit-assets/replace-list.png" alt-text="用語の削除のスクリーンショット。":::

1. classifications、owners、experts に対して上の手順を繰り返します。

    :::image type="content" source="media/how-to-bulk-edit-assets/all-list.png" alt-text="分類と連絡先のスクリーンショット。":::

1. 完了したら、 **[閉じる]** または **[Remove all and close]\(すべて削除して閉じる\)** を選択して一括編集ブレードを閉じます。 [閉じる] では、選択された資産は削除されません。一方、[Remove all and close]\(すべて削除して閉じる\) では、すべての選択された資産が削除されます。
    :::image type="content" source="media/how-to-bulk-edit-assets/close-list.png" alt-text="閉じるのスクリーンショット。":::

   > [!Important]
   > 一括編集で推奨される資産の数は 25 です。 25 を超える数を選択すると、パフォーマンスの問題が発生する可能性があります。
   > **[選択対象の表示]** ボックスは、少なくとも 1 つの資産が選択されている場合にのみ表示されます。

## <a name="next-steps"></a>次のステップ

次のチュートリアルを修了してください。[チュートリアル:用語集の用語の作成とインポート](how-to-create-import-export-glossary.md)

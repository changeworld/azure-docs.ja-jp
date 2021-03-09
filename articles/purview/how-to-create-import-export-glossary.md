---
title: 用語集の用語を作成、インポート、エクスポートする方法
description: Azure Purview で用語集の用語を作成、インポート、エクスポートする方法について説明します。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 7466e143f345ea305c7e9ef118d09fb6f685ac16
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694490"
---
# <a name="how-to-create-import-and-export-glossary-terms"></a>用語集の用語を作成、インポート、エクスポートする方法

この記事では、Azure Purview Data Catalog でビジネス用語集の用語を作成し、.csv ファイルを使用して用語集の用語をインポートおよびエクスポートする方法について説明します。

## <a name="create-a-new-term"></a>新しい用語の作成

用語集の新しい用語を作成するには、次の手順を実行します。

1. ホーム ページの左側のナビゲーションにある用語集アイコンを選択して、用語一覧のページに移動します。

2. **[用語集の用語]** ページで、 **[+ 新しい用語]** を選択します。 **[システムの既定値]** テンプレートが選択された状態でページが開きます。 用語集の用語の作成に使用するテンプレートを選択し、 **[続行]** を選択します。

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-with-default-template.png" alt-text="新規用語作成のスクリーンショット。" border="true":::

3. 新しい用語に名前を付けます。この名前は、カタログ内で一意の名前である必要があります。 用語名は大文字と小文字が区別されます。つまり、**Sample** という用語と **sample** という用語がカタログ内に存在することができます。

4. **[定義]** を追加します。

5. 用語の **[状態]** を設定します。 新しい用語は既定で **[ドラフト]** の状態になります。

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-options.png" alt-text="状態の選択のスクリーンショット。" border="true":::

   これらの状態マーカーは、用語に関連付けられているメタデータです。 現時点では、各用語に対して次の状態を設定できます。

   - **ドラフト**: この用語はまだ正式には実装されていません。
   - **承認済み**: この用語は、正式/標準/承認済みの用語です。
   - **期限切れ**:この用語はもう使用できません。
   - **アラート**: この用語には注意が必要です。

6. **[リソース]** と **[頭字語]** を追加します。 用語が階層の一部である場合は、[概要] タブの **[親]** に親の用語を追加できます。

7. [関連] タブで、 **[同意語]** と **[関連用語]** を追加します。

   :::image type="content" source="media/how-to-create-import-export-glossary/related-tab.png" alt-text="新しい用語 > 関連タブのスクリーンショット。" border="true":::

8. 必要に応じて、 **[連絡先]** タブを選択して、用語にエキスパートとスチュワードを追加します。

9. **[作成]** を選択して用語を作成します。

## <a name="import-terms-into-the-glossary"></a>用語集への用語のインポート

Azure Purview Data Catalog には、用語を用語集にインポートするためのテンプレート .csv ファイルが用意されています。

カタログ内の用語をインポートできます。 ファイル内の重複する用語は上書きされます。

用語名は大文字と小文字が区別されることに注意してください。 たとえば、`Sample` と `saMple` の両方が同じ用語集に存在する可能性があります。

### <a name="to-import-terms-follow-these-steps"></a>用語をインポートするには、次の手順に従います。

1. **[用語集の用語]** ページで、 **[Import terms]\(用語のインポート\)** を選択します。

2. 用語テンプレートのページが開きます。 用語テンプレートを、インポートする .CSV の種類と一致させます。

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-import.png" alt-text="[用語集の用語] ページの、用語をインポートするボタンのスクリーンショット。":::

3. csv テンプレートをダウンロードし、それを使用して追加する用語を入力します。

   > [!Important]
   > システムでは、テンプレートで使用可能な列のインポートのみがポートされます。 [システムの既定値] テンプレートでは、すべての既定の属性が設定されます。
   > ただし、カスタム用語テンプレートでは、そのまま使用できる属性と、テンプレートに定義されている追加のカスタム属性が設定されます。 そのため、選択した用語テンプレートによって、.CSV ファイルの総列数と列名が異なります。 アップロード後にファイルの問題を確認することもできます。

   :::image type="content" source="media/how-to-create-import-export-glossary/select-file-for-import.png" alt-text="[用語集の用語] ページでの、インポートするファイルの選択のスクリーンショット。":::

4. .csv ファイルへの入力が完了したら、インポートするファイルを選択し、 **[OK]** を選択します。

5. システムによってファイルがアップロードされ、すべての用語がカタログに追加されます。

## <a name="export-terms-from-glossary-with-custom-attributes"></a>カスタム属性を使用した、用語集からの用語のエクスポート

選択した用語が同じ用語テンプレートに属している限り、用語集から用語をエクスポートできます。

1. 用語集では、既定で **[エクスポート]** ボタンが無効になっています。 選択した用語が同じテンプレートに属している場合は、エクスポートする用語を選択すると、 **[エクスポート]** ボタンが有効になります。

2. **[エクスポート]** を選択して、選択した用語をダウンロードします。

 > [!Important]
   > 階層内の用語が複数の異なる用語テンプレートに属している場合は、インポートのためにそれらを別々の .CSV ファイルに分割する必要があります。 また、インポート処理を使用して、用語の親を更新することは現在サポートされていません。


## <a name="next-steps"></a>次のステップ

次のチュートリアルを修了してください。[チュートリアル:用語集の用語の作成とインポート](tutorial-import-create-glossary-terms.md)

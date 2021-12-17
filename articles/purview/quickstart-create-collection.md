---
title: 'クイックスタート: コレクションを作成する'
description: コレクションは、Azure Purview でアクセス制御と資産の編成に使用されます。 この記事では、コレクションを作成してアクセス許可を追加し、ソースを登録し、資産をコレクションに登録する方法について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: quickstart
ms.date: 11/04/2021
ms.custom: template-quickstart
ms.openlocfilehash: 5dff616c6fa7ee8e72a932da281935f77db19010
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853702"
---
# <a name="quickstart-create-a-collection-and-assign-permissions-in-purview"></a>クイック スタート: Purview でコレクションを作成してアクセス許可を割り当てる

コレクションは、資産、ソース、および情報全体の所有権とアクセス制御を管理するための Azure Purview のツールです。 これにより、ソースと資産は、管理エクスペリエンスとデータを調和させるためにカスタマイズされたカテゴリに整理されます。 このガイドでは、組織の Purview 環境を準備するために最初のコレクションとコレクション管理者を設定する方法について説明します。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 所有する [Azure Active Directory テナント](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

## <a name="check-permissions"></a>アクセス許可を確認してください

Purview でコレクションを作成および管理するには、Purview 内で **コレクション管理者** である必要があります。 これらのアクセス許可は [Purview Studio](use-purview-studio.md) で確認できます。 Studio を見つけるには、[Azure portal](https://portal.azure.com) で Purview リソースに移動し、概要ページで **[Purview Studio を開く]** タイルを選択します。

1. 左ペインから [データ マップ] > [コレクション] を選択し、コレクション管理ページを開きます。

    :::image type="content" source="./media/quickstart-create-collection/find-collections.png" alt-text="Purview Studio のスクリーンショット。データ マップが開かれ、[コレクション] タブが選択されています。" border="true":::

1. ルート コレクションを選択します。 これはコレクション リストの一番上のコレクションであり、Purview リソースと同じ名前になります。 次の例では、Contoso Purview という名前になっています。

    :::image type="content" source="./media/quickstart-create-collection/select-root-collection.png" alt-text="Purview Studio ウィンドウのスクリーンショット。データ マップが開かれ、ルート コレクションが強調表示されています。" border="true":::

1. コレクション ウィンドウでロールの割り当てを選択します。

    :::image type="content" source="./media/quickstart-create-collection/role-assignments.png" alt-text="Purview Studio ウィンドウのスクリーンショット。データ マップが開かれ、[ロールの割り当て] タブが強調表示されています。" border="true":::

1. コレクションを作成するには、ロールの割り当ての下のコレクション管理者リストに含まれている必要があります。 Purview リソースを作成した場合は、ルート コレクションの下にコレクション管理者として既に一覧表示されている必要があります。 それ以外の場合は、コレクション管理者に連絡してアクセス許可を付与してもらう必要があります。

    :::image type="content" source="./media/quickstart-create-collection/collection-admins.png" alt-text="Purview Studio ウィンドウのスクリーンショット。データ マップが開かれ、コレクション管理者のセクションが強調表示されています。" border="true":::

## <a name="create-a-collection-in-the-portal"></a>ポータルでコレクションを作成する

コレクションを作成するには、[Purview Studio](use-purview-studio.md) から開始します。 Studio を見つけるには、Azure portal で Purview リソースに移動し、概要ページで **[Purview Studio を開く]** タイルを選択します。

1. 左ペインから [データ マップ] > [コレクション] を選択し、コレクション管理ページを開きます。

    :::image type="content" source="./media/quickstart-create-collection/find-collections-2.png" alt-text="Purview Studio ウィンドウのスクリーンショット。データ マップが開かれ、[コレクション] タブが選択されています。" border="true":::

1. **[+ コレクションの追加]** を選択します。

    :::image type="content" source="./media/quickstart-create-collection/select-add-collection.png" alt-text="Purview Studio ウィンドウのスクリーンショット。データ マップが開かれ、[コレクション] タブが選択され、[Add a Collection]\(コレクションの追加\) が強調表示されています。" border="true":::

1. 右側のパネルで、コレクション名、説明を入力し、ユーザーを検索してコレクション管理者として追加します。

    :::image type="content" source="./media/quickstart-create-collection/create-collection.png" alt-text="Purview Studio ウィンドウのスクリーンショット。新しいコレクション ウィンドウが表示され、表示名とコレクション管理者が選択され、[作成] ボタンが強調表示されています。" border="true":::

1. **［作成］** を選択します コレクション情報がページに反映されます。

    :::image type="content" source="./media/quickstart-create-collection/created-collection.png" alt-text="新しく作成されたコレクション ウィンドウが表示されている Purview Studio ウィンドウのスクリーンショット。" border="true":::

## <a name="assign-permissions-to-collection"></a>コレクションにアクセス許可を割り当てる

コレクションが作成されたので、このコレクションにアクセス許可を割り当て、Purview へのユーザー アクセスを管理できます。

### <a name="roles"></a>ロール

割り当てられているすべてのロールは、ロールが適用されるコレクション内のソース、資産、その他のオブジェクトに適用されます。

* **コレクション管理者** - コレクションとその詳細を編集し、コレクション内のアクセスを管理し、サブコレクションを追加できます。
* **データ ソース管理者** - データ ソースとデータ スキャンを管理できます。
* **データ キュレーター** - カタログ データ オブジェクトに対するアクションを作成、読み取り、変更、および削除できます。
* **データ リーダー** - カタログ データ オブジェクトにアクセスできますが、変更はできません。

### <a name="assign-permissions"></a>アクセス許可の割り当て

1. コレクション内のすべてのロールを表示するには、 **[ロールの割り当て]** タブを選択します。

    :::image type="content" source="./media/quickstart-create-collection/select-role-assignments.png" alt-text="[ロールの割り当て] タブが強調表示されている Purview Studio コレクション ウィンドウのスクリーンショット。" border="true":::

1. **[ロール割り当ての編集]** または人アイコンを選択して、各ロールメンバーを編集します。

    :::image type="content" source="./media/quickstart-create-collection/edit-role-assignments.png" alt-text="[ロールの割り当ての編集] ドロップダウン リストが選択されている Purview Studio コレクション ウィンドウのスクリーンショット。" border="true":::

1. テキスト ボックスに入力し、ロール メンバーに追加するユーザーを検索します。 **[OK]** を選択して変更を保存します。

## <a name="next-steps"></a>次の手順

コレクションが作成されたので、以下のガイドに従って、リソースの追加、スキャン、コレクションの管理を行うことができます。

* [ソースをコレクションに登録する](how-to-create-and-manage-collections.md#register-source-to-a-collection)
* [コレクションを使用したアクセス管理](how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections)

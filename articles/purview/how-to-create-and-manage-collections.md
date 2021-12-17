---
title: コレクションを作成および管理する方法
description: この記事では、Azure Purview 内でコレクションを作成および管理する方法について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: template-how-to
ms.openlocfilehash: 0c288971fefc95bfed7d5c4b7496dda628bb1f90
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845268"
---
# <a name="create-and-manage-collections-in-azure-purview"></a>Azure Purview でコレクションを作成および管理する

Azure Purview のコレクションは、ビジネスのフローによる資産とソースの整理に使用できますが、Purview 間のアクセスを管理するために使用されるツールでもあります。 このガイドでは、これらのコレクションの作成と管理に加えて、ソースを登録して資産をコレクションに追加する方法の手順について説明します。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 所有する [Azure Active Directory テナント](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

### <a name="check-permissions"></a>アクセス許可を確認してください

Purview でコレクションを作成および管理するには、Purview 内で **コレクション管理者** である必要があります。 これらのアクセス許可は [Purview Studio](https://web.purview.azure.com/resource/) で確認できます。 Studio を見つけるには、[Azure portal](https://portal.azure.com) で Purview リソースに移動し、概要ページで [Purview Studio を開く] タイルを選択します。

1. 左ペインから [データ マップ] > [コレクション] を選択し、コレクション管理ページを開きます。

    :::image type="content" source="./media/how-to-create-and-manage-collections/find-collections.png" alt-text="Purview Studio ウィンドウのスクリーンショット。データ マップが開かれ、[コレクション] タブが選択されています。" border="true":::

1. ルート コレクションを選択します。 これはコレクション リストの一番上のコレクションであり、Purview リソースと同じ名前になります。 次の例では、Contoso Purview という名前になっています。 また、コレクションが既に存在する場合は、サブコレクションを作成する任意のコレクションを選択できます。

    :::image type="content" source="./media/how-to-create-and-manage-collections/select-root-collection.png" alt-text="Purview Studio ウィンドウのスクリーンショット。データ マップが開かれ、ルート コレクションが強調表示されています。" border="true":::

1. コレクション ウィンドウで **ロールの割り当て** を選択します。

    :::image type="content" source="./media/how-to-create-and-manage-collections/role-assignments.png" alt-text="Purview Studio ウィンドウのスクリーンショット。データ マップが開かれ、[ロールの割り当て] タブが強調表示されています。" border="true":::

1. コレクションを作成するには、ロールの割り当ての下のコレクション管理者リストに含まれている必要があります。 Purview リソースを作成した場合は、ルート コレクションの下にコレクション管理者として既に一覧表示されている必要があります。 それ以外の場合は、コレクション管理者に連絡してアクセス許可を付与してもらう必要があります。

    :::image type="content" source="./media/how-to-create-and-manage-collections/collection-admins.png" alt-text="Purview Studio ウィンドウのスクリーンショット。データ マップが開かれ、コレクション管理者のセクションが強調表示されています。" border="true":::

## <a name="collection-management"></a>コレクションの管理

### <a name="create-a-collection"></a>コレクションの作成

コレクションを作成するには、コレクション管理者である必要があります。 わからない場合は、[前述のガイド](#check-permissions)に従ってアクセス許可を確認してください。

1. 左ペインから [データ マップ] > [コレクション] を選択し、コレクション管理ページを開きます。

    :::image type="content" source="./media/how-to-create-and-manage-collections/find-collections.png" alt-text="Purview Studio ウィンドウのスクリーンショット。データ マップが開かれ、[コレクション] タブが選択され、開かれています。" border="true":::

1. **[+ Add a collection (+ コレクションの追加)]** を選択します。 この場合も、[コレクション管理者](#check-permissions)のみがコレクションを管理できます。

    :::image type="content" source="./media/how-to-create-and-manage-collections/select-add-a-collection.png" alt-text="[add a collection (コレクションの追加)] ボタンが強調表示された新しいコレクション ウィンドウを示す Purview Studio ウィンドウのスクリーンショット。" border="true":::

1. 右側のパネルで、コレクションの名前と説明を入力します。 必要に応じて、新しいコレクションにコレクション管理者としてユーザーまたはグループを追加できます。
1. **［作成］** を選択します

    :::image type="content" source="./media/how-to-create-and-manage-collections/create-collection.png" alt-text="Purview Studio ウィンドウのスクリーンショット。新しいコレクション ウィンドウが表示され、表示名とコレクション管理者が選択され、[作成] ボタンが強調表示されています。" border="true":::

1. 新しいコレクションの情報がページに反映されます。

    :::image type="content" source="./media/how-to-create-and-manage-collections/created-collection.png" alt-text="新しく作成されたコレクション ウィンドウが表示されている Purview Studio ウィンドウのスクリーンショット。" border="true":::

### <a name="edit-a-collection"></a>コレクションの編集

1. コレクションの詳細ページ、またはコレクションのドロップダウン メニューから、 **[編集]** を選択します。

    :::image type="content" source="./media/how-to-create-and-manage-collections/edit-collection.png" alt-text="Purview Studio ウィンドウのスクリーンショット。データ マップが開かれ、選択したコレクション ウィンドウと、コレクションの名前の横にある省略記号ボタンの両方で、[編集] ボタンが強調表示されています。" border="true":::

1. 現時点では、コレクションの説明とコレクションの管理者を編集できます。 変更を加え、 **[Save (保存)]** を選択して変更を保存します。

    :::image type="content" source="./media/how-to-create-and-manage-collections/edit-description.png" alt-text="Purview Studio ウィンドウのスクリーンショット。[コレクションの編集] ウィンドウが開いており、説明がコレクションに追加され、[保存] ボタンが強調表示されています。" border="true":::

### <a name="view-collections"></a>コレクションの表示

1. コレクションの名前の横にある三角形のアイコンを選択して、コレクション階層を展開または折りたたみます。 移動するコレクション名を選択します。

    :::image type="content" source="./media/how-to-create-and-manage-collections/subcollection-menu.png" alt-text="Purview studio のコレクション ウィンドウのスクリーンショット (コレクション名の横のボタンが強調表示されています)。" border="true":::

1. 一覧の上部にあるフィルター ボックスに入力し、コレクションをフィルター処理します。

    :::image type="content" source="./media/how-to-create-and-manage-collections/filter-collections.png" alt-text="Purview studio のコレクション ウィンドウのスクリーンショット (コレクションの上のフィルターが強調表示されています)。" border="true":::

1. ルート コレクションのコンテキスト メニューで **[最新の情報に更新]** を選択して、コレクションの一覧を再度読み込みます。

    :::image type="content" source="./media/how-to-create-and-manage-collections/refresh-collections.png" alt-text="Purview Studio のコレクション ウィンドウのスクリーンショット。選択したリソース名の横にボタンが表示され、[更新] ボタンが強調表示されています。" border="true":::

1. コレクションの詳細ページで **[最新の情報に更新]** を選択して、単一のコレクションを再度読み込みます。

    :::image type="content" source="./media/how-to-create-and-manage-collections/refresh-single-collection.png" alt-text="Purview Studio のコレクション ウィンドウのスクリーンショット。コレクション ウィンドウの下の [更新] ボタンが強調表示されています。" border="true":::

### <a name="delete-a-collection"></a>コレクションの削除

コレクションを削除するには、コレクション管理者である必要があります。 わからない場合は、前述のガイドに従ってアクセス許可を確認してください。 コレクションを削除できるのは、子のコレクション、資産、データ ソース、またはスキャンが関連付けられていない場合のみです。 

1. コレクションの詳細ページから **[削除]** を選択します。
   
   :::image type="content" source="./media/how-to-create-and-manage-collections/delete-collections.png" alt-text="コレクションを削除する Purview スタジオ ウィンドウのスクリーンショット" border="true":::

2. **[このコレクションを削除しますか?]** というプロンプトが表示されたら、 **[確定]** を選択します。

   :::image type="content" source="./media/how-to-create-and-manage-collections/delete-collection-confirmation.png" alt-text="コレクションを削除するための確認メッセージが表示されている Purview スタジオ ウィンドウのスクリーンショット" border="true":::

3. Purview Data Map からのコレクションの削除を確認します。

## <a name="add-roles-and-restrict-access-through-collections"></a>ロールを追加して、コレクションからアクセスを制限する

アクセス許可は Purview のコレクションから管理されるため、ロールとユーザーに付与するアクセス許可を理解することが重要です。 コレクションに対するアクセス許可を付与されたユーザーは、そのコレクションに関連付けられているソースと資産にアクセスできるだけでなく、サブコレクションに対するアクセス許可を継承します。 継承は[制限できます](#restrict-inheritance)が、既定では許可されています。

次のガイドでは、ロール、それらを管理する方法、アクセス許可の継承について説明します。

### <a name="roles"></a>ロール

割り当てられているすべてのロールは、ロールが適用されるコレクション内のソース、資産、その他のオブジェクトに適用されます。

* **コレクション管理者** - コレクションとその詳細を編集し、サブコレクションを追加できます。 また、データ キュレーター、データ リーダー、その他の Purview ロールをコレクション スコープに追加することもできます。 親コレクションから自動的に継承されたコレクション管理者を削除することはできません。
* **データ ソース管理者** - データ ソースとデータ スキャンを管理できます。
* **データ キュレーター** - カタログ データ オブジェクトの作成、読み取り、変更、削除のアクションを実行し、オブジェクト間のリレーションシップを確立できます。
* **データ リーダー** - カタログ データ オブジェクトにアクセスできますが、変更はできません。

### <a name="add-role-assignments"></a>ロールの割り当てを追加する

1. コレクション内のすべてのロールを表示するには、 **[ロールの割り当て]** タブを選択します。 ロールの割り当てを管理できるのは、コレクション管理者のみです。

    :::image type="content" source="./media/how-to-create-and-manage-collections/select-role-assignments.png" alt-text="[ロールの割り当て] タブが強調表示されている Purview Studio コレクション ウィンドウのスクリーンショット。" border="true":::

1. **[ロール割り当ての編集]** または人アイコンを選択して、各ロールメンバーを編集します。

    :::image type="content" source="./media/how-to-create-and-manage-collections/edit-role-assignments.png" alt-text="[ロールの割り当ての編集] ドロップダウン リストが選択されている Purview Studio コレクション ウィンドウのスクリーンショット。" border="true":::

1. テキスト ボックスに入力し、ロール メンバーに追加するユーザーを検索します。 追加しないメンバーを削除するには、**X** を選択します。

    :::image type="content" source="./media/how-to-create-and-manage-collections/search-user-permissions.png" alt-text="検索バーが強調表示されている Purview Studio コレクション コレクション管理ウィンドウのスクリーンショット。" border="true":::

1. **[OK]** を選択して変更を保存すると、新しいユーザーが [ロールの割り当て] 一覧に反映されます。

### <a name="remove-role-assignments"></a>ロールの割り当てを削除する

1. ロールの割り当てを削除するには、ユーザー名の横にある **[X]** ボタンを選択します。

    :::image type="content" source="./media/how-to-create-and-manage-collections/remove-role-assignment.png" alt-text="Purview Studio のコレクション ウィンドウのスクリーンショット。[ロールの割り当て] タブが選択され、いずれかの名前の横に [X] ボタンが強調表示されています。" border="true":::

1. ユーザーを削除する場合は、 **[確定]** を選択します。

    :::image type="content" source="./media/how-to-create-and-manage-collections/confirm-remove.png" alt-text="確認のポップアップのスクリーンショット。[確認] ボタンが強調表示されています。" border="true":::

### <a name="restrict-inheritance"></a>継承を制限する

コレクションのアクセス許可は、親コレクションから自動的に継承されます。 たとえば、ルート コレクション (Purview リソースと同じ名前の一覧の上部にあるコレクション) に対するアクセス許可は、その下のすべてのコレクションに継承されます。 [継承されたアクセス許可を制限する] オプションを使用すると、いつでも親コレクションから継承を制限できます。

継承を制限した場合は、制限されたコレクションにユーザーを直接追加して、アクセス権を付与する必要があります。

1. 継承を制限するコレクションに移動し、 **[ロールの割り当て]** タブを選択します。
1. **[Restrict inherited permissions (継承されたアクセス許可を制限する)]** を選択し、ポップアップ ダイアログで **[アクセスの制限]** をクリックして、このコレクションとサブコレクションから継承されたアクセス許可を削除します。 コレクション管理者のアクセス許可は影響を受けません。

    :::image type="content" source="./media/how-to-create-and-manage-collections/restrict-access-inheritance.png" alt-text="Purview Studio コレクション ウィンドウのスクリーンショット。[ロールの割り当て] タブが選択され、[継承されたアクセス許可を制限する] スライド ボタンが強調表示されています。" border="true":::

1. 制限後、継承されたメンバーは、ロールから削除されます (コレクション管理者を除く)。
1. 元に戻すには、 **[Restrict inherited permissions (継承されたアクセス許可を制限する)]** トグル ボタンをもう一度選択します。

    :::image type="content" source="./media/how-to-create-and-manage-collections/remove-restriction.png" alt-text="Purview Studio コレクション ウィンドウのスクリーンショット。[ロールの割り当て] タブが選択され、[継承されたアクセス許可を制限しない] スライド ボタンが強調表示されています。" border="true":::
    
## <a name="register-source-to-a-collection"></a>ソースをコレクションに登録する

1. データ ソースを登録するには、コレクション ノードで **[レジスタ]** を選択するか、[レジスタ] アイコンを選択します。 ソースを登録できるのはデータ ソース管理者のみです。

    :::image type="content" source="./media/how-to-create-and-manage-collections/register-by-collection.png" alt-text="ページの上部とコレクションの下の両方で [登録] ボタンが強調表示されている Purview Studio データ マップ ウィンドウのスクリーンショット。"border="true":::

1. データ ソース名と他のソース情報を入力します。  スキャン アクセス許可のあるすべてのコレクションがフォームの下部に一覧表示されます。 選択できるコレクションは 1 つです。 このソースのすべての資産は、選択したコレクションに属します。

    :::image type="content" source="./media/how-to-create-and-manage-collections/register-source.png" alt-text="ソース登録ウィンドウのスクリーンショット。"border="true":::

1. 作成されたデータ ソースは、選択したコレクションの下に配置されます。 **[View details (詳細の表示)]** を選択して、データ ソースを表示します。

    :::image type="content" source="./media/how-to-create-and-manage-collections/see-registered-source.png" alt-text="新しく追加されたソース カードが強調表示されている Purview Studio データ マップ ウィンドウのスクリーンショット。"border="true":::

1. **[New scan (新しいスキャン)]** を選択して、データ ソースの下にスキャンを作成します。

    :::image type="content" source="./media/how-to-create-and-manage-collections/new-scan.png" alt-text="新しいスキャン ボタンが強調表示されている Purview Studio ソース ウィンドウのスクリーンショット。"border="true":::

1. 同様に、フォームの下部でコレクションを選択できます。スキャンされた資産はすべてコレクションに含まれます。
ここに示すコレクションは、データ ソース コレクションのサブコレクションに制限されます。

    :::image type="content" source="./media/how-to-create-and-manage-collections/scan-under-collection.png" alt-text="コレクション ドロップダウンが強調表示されている新しいスキャン ウィンドウのスクリーンショット。"border="true":::

1. コレクション ウィンドウに戻ると、コレクションにリンクされたデータ ソースがソース カードに表示されます。

    :::image type="content" source="./media/how-to-create-and-manage-collections/source-under-collection.png" alt-text="新しく追加されたソース カードがマップで強調表示されている Purview Studio データ マップ ウィンドウのスクリーンショット。"border="true":::

## <a name="add-assets-to-collections"></a>資産をコレクションに追加する

資産とソースもコレクションに関連付けられています。 スキャンがコレクションに関連付けられている場合にスキャンすると、資産は自動的にそのコレクションに追加されますが、手動でサブコレクションに追加することもできます。

1. 資産の詳細でコレクション情報を確認します。 コレクション情報は、[asset details (資産の詳細)] ページの右上隅にある **[Collection path (コレクション パス)]** セクションで確認できます。

    :::image type="content" source="./media/how-to-create-and-manage-collections/collection-path.png" alt-text="コレクション パスが強調表示されている Purview Studio の資産ウィンドウのスクリーンショット。" border="true":::

1. [資産の詳細] ページのアクセス許可:
    1. [上記のコレクションにロールを追加してアクセスを制限するガイド](#add-roles-and-restrict-access-through-collections)に従って、コレクション ベースのアクセス許可モデルを確認してください。
    1. コレクションに対する読み取りアクセス許可がない場合、そのコレクションの下の資産は検索結果に表示されません。 1 つの資産の直接 URL を取得して開くと、[アクセス権なし] ページが表示されます。 この場合は、Purview 管理者に連絡して、アクセス権の付与を依頼してください。 **[最新の情報に更新]** ボタンを選択すると、再度アクセス許可を確認することができます。

        :::image type="content" source="./media/how-to-create-and-manage-collections/no-access.png" alt-text="Purview Studio の資産ウィンドウのスクリーンショット。ユーザーにアクセス許可がなく、情報やオプションのアクセス権もありません。" border="true":::

    1. 1 つのコレクションに対する読み取りアクセス許可があるが、書き込みアクセス許可がない場合は、[資産の詳細] ページを参照できますが、次の操作は無効になっています。
        * 資産を編集する。 **[編集]** ボタンは無効になります。
        * 資産を削除する。 **[削除する]** ボタンは無効になります。
        * 資産を別のコレクションに移動する。 [Collection path (コレクション パス)] セクションの右上隅にある [ellipsis (楕円)] ボタンは非表示になります。
    1. **[階層]** セクションの資産もアクセス許可の影響を受けます。 読み取りアクセス許可のない資産はグレー表示されます。

        :::image type="content" source="./media/how-to-create-and-manage-collections/hierarchy-permissions.png" alt-text="Purview Studio の資産ウィンドウのスクリーンショット。ユーザーに読み取りアクセス許可のみがあり、オプションのアクセス権はありません。" border="true":::

### <a name="move-asset-to-another-collection"></a>資産を別のコレクションに移動する

1. [Collection path (コレクション パス)] セクションの右上隅にある [ellipsis (楕円)] ボタンを選択します。

    :::image type="content" source="./media/how-to-create-and-manage-collections/move-asset.png" alt-text="Purview Studio の資産ウィンドウのスクリーンショット。コレクション パスが強調表示され、コレクション パスの横にある省略記号ボタンが選択されています。" border="true":::

1. **[Move to another collection (別のコレクションに移動)]** ボタンを選択します。
1. 右側のパネルで、移動先のターゲット コレクションを選択します。 表示できるのは、書き込みアクセス許可があるコレクションのみです。 資産は、データ ソース コレクションのサブコレクションにのみ追加できます。

    :::image type="content" source="./media/how-to-create-and-manage-collections/move-select-collection.png" alt-text="[コレクションの選択] ドロップダウン メニューが強調表示されている Purview Studio ポップアップ ウィンドウのスクリーンショット。" border="true":::

1. ウィンドウの下部にある **[Move (移動)]** ボタンを選択して、資産を移動します。

## <a name="search-and-browse-by-collections"></a>コレクションで検索して参照する

### <a name="search-by-collection"></a>コレクションで検索する

1. Azure Purview では、検索バーは Purview Studio UX の上部にあります。

   :::image type="content" source="./media/how-to-create-and-manage-collections/purview-search-bar.png" alt-text="Azure Purview の検索バーの場所を示すスクリーンショット" border="true":::

1. 検索バーを選択すると、最近の検索履歴と最近アクセスした資産が表示されます。 最近表示したすべての資産を表示するには、 **[View all (すべて表示)]** を選択します。

   :::image type="content" source="./media/how-to-create-and-manage-collections/search-no-keywords.png" alt-text="キーワードが入力される前の検索バーを示すスクリーンショット" border="true":::

1. 資産を識別するのに役立つキーワードを入力します (その名前、データ型、分類、用語集の用語など)。 目的の資産に関連するキーワードを入力すると、Azure Purview には、検索対象の候補と、一致する可能性がある資産の候補が表示されます。 検索を完了するには、 **[View search results (検索結果の表示)]** を選択するか、**Enter** キーを押します。

   :::image type="content" source="./media/how-to-create-and-manage-collections/search-keywords.png" alt-text="ユーザーがキーワードを入力した検索バーのスクリーンショット" border="true":::

1. 検索結果ページには、入力されたキーワードに一致する資産の一覧が関連性の順に表示されます。 資産の関連性スコアに影響を与える可能性があるさまざまな要因があります。 検索対象の資産に適用される特定のコレクション、データ ストア、分類、連絡先、ラベル、用語集の用語を選択して、一覧をさらに絞り込むことができます。

   :::image type="content" source="./media/how-to-create-and-manage-collections/search-results.png" alt-text="検索結果を示すスクリーンショット" border="true":::

1. 目的の資産を選択すると、資産の詳細ページが表示されます。ここでは、スキーマ、系列、資産の所有者などのプロパティが確認できます。

   :::image type="content" source="./media/how-to-create-and-manage-collections/search-by-collection.png" alt-text="コレクションを含む検索結果を示すスクリーンショット。" border="true":::

### <a name="browse-by-collection"></a>コレクションで参照する

1. データ資産を参照するには、ホームページで **[Browse asset (資産の参照)]** を選択します。

    :::image type="content" source="./media/how-to-create-and-manage-collections/browse-by-collection.png" alt-text="[資産の参照] ボタンが強調表示されている Purview Studio カタログ ウィンドウのスクリーンショット。" border="true":::

1. [資産の参照] ページで、 **[By collection (コレクション別)]** ピボットを選択します。 コレクションと階層テーブル ビューが一覧表示されます。 各コレクション内の資産をさらに検索するには、対応するコレクション名をクリックします。

    :::image type="content" source="./media/how-to-create-and-manage-collections/by-collection-view.png" alt-text="[コレクション別] タブが選択されている Purview Studio 資産ウィンドウのスクリーンショット。"border="true":::

1. 次のページで、選択したコレクションの下に資産の検索結果が表示されます。 ファセット フィルターを選択すると、結果を絞り込むことができます。 また、サブ/関連コレクション名を選択すると、他のコレクションの下の資産を表示できます。

    :::image type="content" source="./media/how-to-create-and-manage-collections/search-results-by-collection.png" alt-text="[by collection (コレクション別)] タブが選択されたカタログ Purview Studio ウィンドウのスクリーンショット。"border="true":::

1. 資産の詳細を表示するには、検索結果で資産名をクリックします。 または、資産を確認して、一括編集できます。

    :::image type="content" source="./media/how-to-create-and-manage-collections/view-asset-details.png" alt-text="Purview Studio カタログ ウィンドウのスクリーンショット。[コレクション別] タブが選択され、[資産] チェック ボックスが強調表示されています。"border="true":::

## <a name="next-steps"></a>次の手順

コレクションが作成されたので、以下のガイドに従ってリソースの追加やスキャンを行います。

* [データ ソースを管理する](manage-data-sources.md)

* [サポートされるデータ ソース](purview-connector-overview.md)

* [スキャンとインジェスト](concept-scans-and-ingestion.md)

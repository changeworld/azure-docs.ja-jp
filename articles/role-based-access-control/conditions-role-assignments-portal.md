---
title: Azure portal で Azure ロール割り当ての条件を追加または編集する (プレビュー) - Azure RBAC
description: Azure ロール割り当ての、属性ベースのアクセス制御 (ABAC) の条件を、Azure portal と Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、追加、編集、閲覧する方法について説明します。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: conceptual
ms.workload: identity
ms.date: 09/24/2021
ms.author: rolyon
ms.custom: subject-rbac-steps
ms.openlocfilehash: 7401b7369d766df5513100b30b7b65c324517fbe
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091991"
---
# <a name="add-or-edit-azure-role-assignment-conditions-using-the-azure-portal-preview"></a>Azure portal で Azure ロール割り当ての条件を追加または編集する (プレビュー)

> [!IMPORTANT]
> Azure ABAC と Azure ロール割り当ての条件は、現在プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Azure ロールの割り当て条件](conditions-overview.md)は、必要に応じてロールの割り当てに追加して、よりきめ細かなアクセス制御を提供できるようにする追加のチェックです。 たとえば、特定のタグがあるオブジェクトしか読み込めない、という条件を設定できます。 この記事では、Azure portal でロール割り当ての条件を追加、編集、閲覧、削除する方法を説明します。

## <a name="prerequisites"></a>[前提条件]

ロール割り当ての条件を追加、編集するための要件は、[条件の要件](conditions-prerequisites.md)に関する記事をご覧ください。


## <a name="step-1-determine-the-condition-you-need"></a>ステップ 1: 必要な条件を判断する

必要な条件を判断するには、[Azure ロール割り当ての条件の例](../storage/common/storage-auth-abac-examples.md)に関する記事をご確認ください。

現在条件を追加できるのは、[ストレージ BLOB データ アクション](../storage/common/storage-auth-abac-attributes.md)がある、組み込みまたはカスタムのロール割り当てです。 これらには、次の組み込みロールが含まれます。

- [ストレージ BLOB データ共同作成者](built-in-roles.md#storage-blob-data-contributor)
- [ストレージ BLOB データ所有者](built-in-roles.md#storage-blob-data-owner)
- [ストレージ BLOB データ閲覧者](built-in-roles.md#storage-blob-data-reader)

## <a name="step-2-choose-how-to-add-condition"></a>ステップ 2: 条件の追加方法を選ぶ

条件を追加する方法は 2 つあります。 新たにロールを割り当てるときに条件を追加する方法と、割り当て済みのロールに条件を追加する方法があります。

### <a name="new-role-assignment"></a>New role assignment

1. 「[Azure portal を使用して Azure ロールを割り当てる](role-assignments-portal.md)」の手順を実行します。

1. **[条件 (省略可能)]** タブで、 **[条件の追加]** をクリックします。

    [条件 (省略可能)] タブが表示されない場合は、これらの条件をサポートするロールを選択したことを確認してください。

   ![ロール割り当て追加ページのスクリーンショット。プレビュー機能である [条件の追加] タブが表示されています。](./media/shared/condition.png)

    ロール割り当ての条件追加ページが表示されます。

### <a name="existing-role-assignment"></a>既存のロール割り当て

1. Azure portal で、条件を追加したいスコープで **[アクセス制御 (IAM)]** を開きます。 たとえば、サブスクリプション、リソース グループまたはリソースを開きます。

    現在 Azure portal では、管理グループのスコープで条件を追加、閲覧、編集、削除することはできません。

1. **[ロールの割り当て]** タブをクリックして、このスコープのすべてのロールの割り当てを表示します。

1. 条件を追加したいストレージ データ アクションがあるロール割り当てを見つけます。

1. **[条件]** 列で **[追加]** をクリックします。

    [追加] リンクが表示されない場合は、ロール割り当てと同じスコープを見ていることを確認してください。

    ![[条件] 列のあるロール割り当てリスト。](./media/conditions-role-assignments-portal/condition-role-assignments-list.png)

    ロール割り当ての条件追加ページが表示されます。

## <a name="step-3-review-basics"></a>ステップ 3: 基本項目を確認する

ロール割り当ての条件追加ページでは、条件の基本項目を確認できます。 **[ロール]** は条件を追加するロールを表します。

1. **[エディターの種類]** オプションでは、既定の **[ビジュアル]** を選択したままにします。

    条件を追加すると、[ビジュアル] と [コード] を切り替えられるようになります。

1. (オプション) **[説明]** ボックスが表示される場合は、説明を入力します。

    条件の追加方法により、[説明] ボックスが表示されない場合もあります。 説明は、条件を理解し思い出すのに役立ちます。

    ![ロール割り当ての条件追加ページ。エディターの種類と説明が表示されています。](./media/conditions-role-assignments-portal/condition-basics.png)

## <a name="step-4-add-actions"></a>ステップ 4: アクションを追加する

1. **[アクションの追加]** セクションで **[アクションの追加]** をクリックします。

    [アクションの選択] ペインが表示されます。 条件を設定するロール割り当てに合わせて絞り込まれたデータ アクションのリストが、このペインに表示されます。 詳しくは、[Azure ロール割り当ての条件の形式と構文](conditions-format.md#actions)に関する記事をご覧ください。

    ![条件の [アクションの選択] ペイン。アクションが選択されています。](./media/conditions-role-assignments-portal/condition-actions-select.png)

1. 条件を満たす場合に許可するアクションを選択します。

    1 つの条件に対して複数のアクションを選択した場合、その条件で選択できる属性の数が少なくなる場合があります。これは、選択したすべてのアクションに対応する属性だけが表示されるためです。

1. **[選択]** をクリックします。

    選択したアクションがアクションのリストに表示されます。

## <a name="step-5-build-expressions"></a>手順 5: 式を作成する

1. **[式の作成]** セクションで **[式の追加]** をクリックします。

    [式] セクションが展開されます。

1. [Attribute source]\(属性ソース\) リストで、何に対する属性を式に使用するかを選択します。

    - **[リソース]** を選択すると、コンテナー名などのリソースに対する属性を使用します。
    - **[要求]** を選択すると、BLOB のインデックス タグの設定などの、アクション要求に対する属性を使用します。

1. [属性] リストで、式の左側に使用する属性を選択します。 詳しくは、[Azure ロール割り当ての条件の形式と構文](conditions-format.md#attributes)に関する記事をご覧ください。

    選択する属性によっては、属性の詳細を指定する追加のボックスが表示されます。

1. [演算子] リストでは、演算子を選択します。

1. [値] ボックスでは、式の右側に使用する値を入力します。

    ![[式の作成] セクション。BLOB インデックス タグの値が表示されています。](./media/shared/condition-expressions.png)

## <a name="step-6-review-and-add-condition"></a>ステップ 6: 条件を確認、追加する

1. **[エディターの種類]** まで上方にスクロールし、 **[コード]** をクリックします。

    条件がコードとして表示されます。 このコード エディターでは、条件を変更できます。 ビジュアル エディターに戻るには、 **[ビジュアル]** をクリックします。

    ![アクションを選択し式を追加した状態で、コード エディターに表示されている条件。](./media/conditions-role-assignments-portal/condition-code.png)

1. **[保存]** をクリックしてロール割り当てに条件を追加します。

## <a name="view-edit-or-delete-a-condition"></a>条件を閲覧、編集または削除する

1. Azure portal で、閲覧、編集または削除したい条件のあるロール割り当ての **[アクセス制御 (IAM)]** を開きます。

1. **[ロールの割り当て]** タブをクリックして目的のロール割り当てを見つけます。

1. **[条件]** 列で、 **[表示または編集]** をクリックします。

    [表示または編集] リンクが表示されない場合は、ロール割り当てと同じスコープを見ていることを確認してください。

    ![条件の [表示または編集] リンクを含むロール割り当てのリスト。](./media/conditions-role-assignments-portal/condition-role-assignments-list-edit.png)

    ロール割り当ての条件追加ページが表示されます。

1. エディターで条件を閲覧または編集します。

    ![[表示または編集] リンクをクリックしてエディターに表示された条件。](./media/conditions-role-assignments-portal/condition-edit.png)

1. 完了したら、 **[保存]** をクリックします。 条件全体を削除するには、 **[条件の削除]** をクリックします。 条件を削除してもロール割り当ては削除されません。

## <a name="next-steps"></a>次のステップ

- [Azure ロール割り当て条件の例 (プレビュー)](../storage/common/storage-auth-abac-examples.md)
- [チュートリアル: Azure portal でロール割り当ての条件を追加して blob へのアクセスを制限する (プレビュー)](../storage/common/storage-auth-abac-portal.md)
- [Azure ロール割り当ての条件のトラブルシューティング (プレビュー)](conditions-troubleshoot.md)

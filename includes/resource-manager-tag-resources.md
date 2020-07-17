---
title: インクルード ファイル
description: インクルード ファイル
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/19/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a00291182059506aeab9cde965fa4cbd5177ecf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132121"
---
タグを適用するために必要なアクセス権がユーザーに与えられていない場合は、 **[タグ共同作成者]** ロールをユーザーに割り当てることができます。 詳細については、「[チュートリアル:RBAC と Azure portal を使用して Azure リソースへのアクセス権をユーザーに付与する](../articles/role-based-access-control/quickstart-assign-role-user-portal.md)」を参照してください。

1. リソースまたはリソース グループのタグを表示するには、概要で既存のタグを探します。 これまでにタグを適用していない場合、リストは空です。

   ![リソースまたはリソース グループのタグの表示](./media/resource-manager-tag-resources/view-tags.png)

1. タグを追加するには、 **[タグを追加するにはここをクリックします]** を選択します。

1. 名前と値を指定します。

   ![タグの追加](./media/resource-manager-tag-resources/add-tag.png)

1. 必要に応じてタグの追加を続行します。 操作が完了したら、 **[保存]** をクリックします。

   ![タグの保存](./media/resource-manager-tag-resources/save-tags.png)

1. タグは、概要に表示されます。

   ![タグの表示](./media/resource-manager-tag-resources/view-new-tags.png)

1. タグを追加または削除するには、 **[変更]** を選択します。

1. タグを削除するには、ごみ箱アイコンを選択します。 次に、 **[保存]** を選択します。

   ![タグの削除](./media/resource-manager-tag-resources/delete-tag.png)

複数のリソースにタグを一括で割り当てるには:

1. リソースのリストで、タグを割り当てるリソースのチェックボックスをオンにします。 次に、 **[タグの割り当て]** を選択します。

   ![複数のリソースの選択](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. 名前と値を追加します。 操作が完了したら、 **[保存]** をクリックします。

   ![割り当ての選択](./media/resource-manager-tag-resources/select-assign.png)

タグが付いているすべてのリソースを表示するには:

1. Azure portal のメニューで**タグ**を検索します。 使用可能なオプションから、それを選択します。

   ![タグで検索する](./media/resource-manager-tag-resources/find-tags-general.png)

1. リソースを表示するタグを選択します。

   ![タグの選択](./media/resource-manager-tag-resources/select-tag.png)

1. そのタグの付いたすべてのリソースが表示されます。

   ![タグでリソースを表示する](./media/resource-manager-tag-resources/view-resources-by-tag.png)

---
title: ロールベースのアクセスをユーザーに許可する
titleSuffix: Azure Maps
description: ロールベースのアクセス制御を使用して、ユーザーに Azure Maps への承認を与えます
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 19806fe24d0ff3b87ebe61b45ac302947c734fa0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895570"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Azure Maps に対するロールベースのアクセスをユーザーに許可する

" *Azure ロールベースのアクセス制御* " (Azure RBAC) を付与するには、Azure AD グループまたはセキュリティ プリンシパルを、1 つまたは複数の Azure Maps ロールの定義に割り当てます。 Azure Maps に使用できる Azure ロールの定義を表示するには、 **[アクセス制御 (IAM)]** に移動します。 **[ロール]** を選択してから、「 *Azure Maps* 」で始まるロールを検索します。

* Azure Maps への大量のユーザーのアクセスを効率的に管理するには、[Azure AD グループ](../../active-directory/fundamentals/active-directory-manage-groups.md)に関する記事を参照してください。
* ユーザーがアプリケーションに対する認証を実行できるようにするには、ユーザーを Azure AD で作成する必要があります。 「[Azure Active Directory を使用してユーザーを追加または削除する](../../active-directory/fundamentals/add-users-azure-active-directory.md)」を参照してください。

ユーザーのディレクトリを効果的に管理する方法の詳細については、[Azure AD](../../active-directory/fundamentals/index.yml) に関する記事を参照してください。

1. **[Azure Maps アカウント]** にアクセスします。 **[アクセス制御 (IAM)]**  >  **[ロールの割り当て]** の順に選択します。

    ![Azure RBAC を使用してアクセス権を付与する](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. **[ロールの割り当て]** タブの **[ロール]** で、 **[Azure Maps データ閲覧者]** や **[Azure Maps データ共同作成者]** などの組み込みの Azure Maps ロールの定義を選択します。 **[アクセスの割り当て先]** で **[Azure AD user, group, or service principal]\(Azure AD のユーザー、グループ、またはサービス プリンシパル\)** を選択します。 プリンシパルを名前で選択します。 次に、 **[保存]** を選択します。

   * 詳細については、[ロールの割り当ての追加または削除](../../role-based-access-control/role-assignments-portal.md)に関する記事を参照してください。

> [!WARNING]
> Azure Maps の組み込みロール定義では、多くの Azure Maps REST API に対する非常に大きな承認アクセスが提供されます。 ユーザーに対する API を最小限に制限するには、[カスタム ロール定義を作成してそれにユーザーを割り当てる](../../role-based-access-control/custom-roles.md)方法に関する記事を参照してください。 これにより、ユーザーがアプリケーションに対して持つことのできる特権は必要最低限になります。
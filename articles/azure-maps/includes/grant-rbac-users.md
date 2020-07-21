---
title: ロールベースのアクセスをユーザーに許可する
titleSuffix: Azure Maps
description: ロールベースのアクセス制御を使用して、ユーザーに Azure Maps への承認を与えます
author: philmea
ms.author: philmea
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 86f89397f3685443071788580253ee11ce4b70be
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84988447"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Azure Maps に対するロールベースのアクセスをユーザーに許可する

"*ロールベースのアクセス制御*" (RBAC) を付与するには、Azure AD グループまたはセキュリティ プリンシパルを、1 つまたは複数の Azure Maps アクセス制御ロールの定義に割り当てます。 Azure Maps に使用できる RBAC ロールの定義を表示するには、 **[アクセス制御 (IAM)]** に移動します。 **[ロール]** を選択してから、「*Azure Maps*」で始まるロールを検索します。

* Azure Maps への大量のユーザーのアクセスを効率的に管理するには、[Azure AD グループ](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)に関する記事を参照してください。
* ユーザーがアプリケーションに対する認証を実行できるようにするには、ユーザーを Azure AD で作成する必要があります。 「[Azure Active Directory を使用してユーザーを追加または削除する](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory)」を参照してください。

ユーザーのディレクトリを効果的に管理する方法の詳細については、[Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/) に関する記事を参照してください。

1. **[Azure Maps アカウント]** にアクセスします。 **[アクセス制御 (IAM)]**  >  **[ロールの割り当て]** の順に選択します。

    ![RBAC の付与](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. **[ロールの割り当て]** タブの **[ロール]** で、 **[Azure Maps データ閲覧者]** や **[Azure Maps データ共同作成者]** などの組み込みの Azure Maps ロールの定義を選択します。 **[アクセスの割り当て先]** で **[Azure AD user, group, or service principal]\(Azure AD のユーザー、グループ、またはサービス プリンシパル\)** を選択します。 プリンシパルを名前で選択します。 次に、 **[保存]** を選択します。

   * 詳細については、[ロールの割り当ての追加または削除](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)に関する記事を参照してください。

> [!WARNING]
> Azure Maps の組み込みロール定義では、多くの Azure Maps REST API に対する非常に大きな承認アクセスが提供されます。 ユーザーに対する API を最小限に制限するには、[カスタム ロール定義を作成してそれにユーザーを割り当てる](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)方法に関する記事を参照してください。 これにより、ユーザーがアプリケーションに対して持つことのできる特権は必要最低限になります。
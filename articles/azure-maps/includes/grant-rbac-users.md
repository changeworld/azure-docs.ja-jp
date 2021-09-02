---
title: Microsoft Azure Maps に対するロールベースのアクセスをユーザーに許可する
titleSuffix: Azure Maps
description: ロールベースのアクセス制御を使用して、ユーザーに Azure Maps への承認を与えます
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/21/2021
ms.topic: include
ms.service: azure-maps
services: azure-maps
custom.ms: subject-rbac-steps
ms.openlocfilehash: 22b3eaaff0989718a781b29549b4fab102673273
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802568"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Azure Maps に対するロールベースのアクセスをユーザーに許可する

"*Azure ロールベースのアクセス制御*" (Azure RBAC) を付与するには、Azure AD グループまたはセキュリティ プリンシパルを、1 つまたは複数の Azure Maps ロールの定義に割り当てます。

Azure Maps で使用できる Azure ロールの定義については、「[組み込みの Azure Maps ロールの定義を表示する](../how-to-manage-authentication.md#view-built-in-azure-maps-role-definitions)」のセクションを参照してください。

作成されたマネージド ID またはサービス プリンシパルに、使用可能な Azure Maps ロールを割り当てる方法の詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください

大量のユーザーの Azure Maps アプリとリソース アクセスを効率的に管理するには、[Azure AD グループ](../../active-directory/fundamentals/active-directory-manage-groups.md)に関するページを参照してください。

>[!IMPORTANT]
>ユーザーがアプリケーションへの認証を許可されるようにするには、まず、ユーザーを Azure AD で作成する必要があります。 詳細については、[Azure AD を使用してユーザーを追加または削除する](../../active-directory/fundamentals/add-users-azure-active-directory.md)方法に関するページを参照してください。

ユーザーのための大規模なディレクトリを効果的に管理する方法については、[Azure AD](../../active-directory/fundamentals/index.yml) に関するページを参照してください。

> [!WARNING]
> Azure Maps の組み込みロールの定義は、多くの Azure Maps REST API に対する非常に大きな承認アクセスを提供します。 API のアクセスを最小限に制限するには、[カスタム ロールの定義を作成して、システム割り当て ID をカスタム ロールの定義に割り当てる](../../role-based-access-control/custom-roles.md)方法に関する記事を参照してください。 これにより、アプリケーションが Azure Maps にアクセスするために必要な最小限の権限が有効になります。

---
title: Microsoft Defender for Cloud でテナント全体のアクセス許可を付与と要求する
description: Microsoft Defender for Cloud でテナント全体のアクセス許可を管理する方法について説明します
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6f263de696ea23d0502a98b0ca85f4413a7020cd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131004446"
---
# <a name="grant-and-request-tenant-wide-visibility"></a>テナント全体の可視性を付与および要求する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

**グローバル管理者** の Azure Active Directory (AD) ロールを持つユーザーは、テナント全体の責任を持つことができますが、Microsoft Defender for Cloud で組織全体の情報を表示するための Azure アクセス許可はありません。 Azure AD ロールの割り当てでは Azure リソースへのアクセス権が付与されないため、アクセス許可の昇格が必要です。 

## <a name="grant-tenant-wide-permissions-to-yourself"></a>テナント全体のアクセス許可を自分に付与する

テナント レベルのアクセス許可を自分に割り当てるには、次の手順を実行します。

1. 組織が [Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md) またはその他の PIM ツールを使用してリソースへのアクセスを管理している場合は、下の手順に従い、ユーザーに対してグローバル管理者ロールが有効になっている必要があります。

1. テナントのルート管理グループに割り当てられていない、グローバル管理者ユーザーとして、Defender for  Cloud's **Overview** pageを開き、バナーにある  **tenant-wide visibility** リンクを選択します。 

    :::image type="content" source="media/management-groups-roles/enable-tenant-level-permissions-banner.png" alt-text="Microsoft Defender for Cloud でテナントレベルのアクセス許可を有効にします。":::

1. 割り当てる新しい Azure ロールを選択します。 

    :::image type="content" source="media/management-groups-roles/enable-tenant-level-permissions-form.png" alt-text="ユーザーに割り当てるテナントレベルのアクセス許可を定義するフォーム。":::

    > [!TIP]
    > 一般に、セキュリティ管理者ロールはルート レベルでポリシーを適用するために必要であり、テナント レベルの可視性を提供するのであればセキュリティ閲覧者で十分です。 これらのロールによって付与されるアクセス許可について詳しくは、[セキュリティ管理者組み込みロールの説明](../role-based-access-control/built-in-roles.md#security-admin)または[セキュリティ閲覧者組み込みロールの説明](../role-based-access-control/built-in-roles.md#security-reader)に関するページをご覧ください。
    >
    > Defender for Cloud 固有の役割の違いについては、「 [ロールと許可される操作](permissions.md#roles-and-allowed-actions)」の表を参照してください。

    組織全体のビューは、テナントのルート管理グループ レベルにロールを付与することで実現されます。  

1. Azure portal からログアウトした後に、もう一度ログインします。

1. 昇格したアクセス権を取得したら、Microsoft Defender for Cloud を開いて更新するか、Azure AD テナントのすべてのサブスクリプションが表示されていることを確認します。 

上記の簡単なプロセスでは、さまざまな操作が自動的に実行されます。

1. ユーザーのアクセス許可は一時的に昇格されます。
1. 新しいアクセス許可を使用して、ユーザーはルート管理グループの目的の Azure RBAC ロールに割り当てられます。
1. 昇格したアクセス許可は削除されます。

Azure AD の昇格プロセスの詳細については、「[Azure のすべてのサブスクリプションと管理グループを管理する目的でアクセス権限を昇格させる](../role-based-access-control/elevate-access-global-admin.md)」を参照してください。


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>テナント全体のアクセス許可が不十分な場合に要求する

Defender for Cloud にログインし、ビューが制限されていることを示すバナーが表示された場合は、[詳細] をクリックして、組織のグローバル管理者に要求を送信できます。 この要求には、自分に割り当ててもらいたいロールを含めることができます。グローバル管理者は付与するロールを決定します。 

これらの要求を受け入れるか拒否するかは、グローバル管理者が決定します。 

> [!IMPORTANT]
> 要求は 7 日ごとに 1 回だけ送信できます。

昇格されたアクセス許可をグローバル管理者に要求するには、次のようにします。

1. Azure portal から、Microsoft Defender for Cloud を開きます。

1. "情報が制限されています。" という内容のバナーが表示される場合は、 それを選択します。

    :::image type="content" source="media/management-groups-roles/request-tenant-permissions.png" alt-text="テナント全体のアクセス許可を要求できることをユーザーに通知するバナー。":::

1. 詳細な要求フォームで、目的のロールを選択し、これらのアクセス許可が必要である正当な理由を選択します。

    :::image type="content" source="media/management-groups-roles/request-tenant-permissions-details.png" alt-text="Azure グローバル管理者にテナント全体のアクセス許可を要求するための詳細ページ。":::

1. **[アクセス権の要求]** を選択します。

    グローバル管理者に電子メールが送信されます。 このメールには、要求を承認または拒否できる Defender for Cloudへのリンクが含まれています。

    :::image type="content" source="media/management-groups-roles/request-tenant-permissions-email.png" alt-text="新しいアクセス許可についてグローバル管理者に電子メールで送信する。":::

    グローバル管理者が **[要求の確認]** を選択してプロセスを完了したら、決定内容が要求元のユーザーに電子メールで送信されます。 

## <a name="next-steps"></a>次のステップ

アクセス許可に関する Defender for Cloud の詳細については、次の関連ページを参照してください：

- [Microsoft Defender for Cloud のアクセス許可](permissions.md)

---
title: Privileged Identity Management (PIM) で管理するグループを識別する - Azure AD | Microsoft Docs
description: Privileged Identity Management (PIM) で特権アクセス グループとして管理するために、ロール割り当て可能なグループをオンボードする方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/09/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72f848dae3eda447edee40b0da18f09fed50462c
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157048"
---
# <a name="bring-privileged-access-groups-preview-into-privileged-identity-management"></a>Privileged Identity Management に特権アクセス グループ (プレビュー) を持ち込む

Azure Active Directory (Azure AD) では、Azure AD 組み込みロールをクラウド グループに割り当てて、ロールの割り当てを管理する方法を簡素化することができます。 Azure AD のロールを保護し、アクセスをセキュリティで保護するために、Privileged Identity Management (PIM) を使用して、これらのグループのメンバーまたは所有者の Just-In-Time アクセスを管理できるようになりました。 Azure AD のロールを割り当て可能なグループを特権アクセス グループとして Privileged Identity Management で管理するには、これを PIM で管理下に置く必要があります。

## <a name="identify-groups-to-manage"></a>管理するグループを識別する

「[Azure Active Directory でロールを割り当て可能なグループを作成する](../roles/groups-create-eligible.md)」の説明に従って、Azure AD でロールを割り当て可能なグループを作成できます。 グループを Privileged Identity Management で管理下に置くには、そのグループの所有者である必要があります。

1. 特権ロール管理者ロールのアクセス許可で [Azure AD にサインインします](https://aad.portal.azure.com)。

1. **[グループ]** を選択し、PIM で管理する、ロールを割り当て可能なグループを選択します。 リストを検索およびフィルター処理することができます。

    ![PIM で管理する、ロールを割り当て可能なグループを見つける](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. グループを開き、 **[特権アクセス (プレビュー)]** を選択します。

    ![Privileged Identity Management エクスペリエンスを開く](./media/groups-discover-groups/groups-discover-groups.png)

1. PIM での割り当ての管理を開始します。

    ![Privileged Identity Management で割り当てを管理する](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> 特権アクセス グループは、いったん管理されると、これを管理から除外することはできません。 これにより、別のリソース管理者が Privileged Identity Management 設定を削除できなくなります。

> [!IMPORTANT]
> Azure Active Directory から特権アクセス グループを削除した場合、[特権アクセス グループ (プレビュー)] ブレードからそのグループが削除されるまでに最大 24 時間かかることがあります。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で特権アクセス グループの割り当てを構成する](pim-resource-roles-configure-role-settings.md)
- [Privileged Identity Management で特権アクセス グループを割り当てる](pim-resource-roles-assign-roles.md)

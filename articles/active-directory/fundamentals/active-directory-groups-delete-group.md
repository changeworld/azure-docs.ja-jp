---
title: Azure Active Directory を使用してグループを削除する方法 | Microsoft Docs
description: Azure Active Directory を使用してグループを削除する方法を説明します。
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 014fe487d23a6c75e94ca2708ed15044bd6cf53b
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574333"
---
# <a name="how-to-delete-a-group-using-azure-active-directory"></a>方法: Azure Active Directory を使用してグループを削除する
グループはさまざまな理由で削除されますが、通常は次のために行います。

- 不正なオプションで**グループの種類**を設定してしまった

- 誤って不正なグループを作成したか、重複して作成してしまった 

- グループが不要になった

## <a name="to-delete-a-group"></a>グループを削除するには
1. ディレクトリの全体管理者アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。

2. **[Azure Active Directory]** を選択し、**[グループ]** を選択します。

3. **[グループ - すべてのグループ]** ページで、削除するグループを検索して選択します。 この手順では、**[MDM policy - East]** \(MDM ポリシー - 東部\) を使用します。

    ![[グループ - すべてのグループ] ページ、グループ名が強調表示](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. **[MDM policy - East]\(MDM ポリシー - 東部\) の [概要]** ページで **[削除]** を選択します。

    このグループは、Azure Active Directory テナントから削除されます。

    ![[削除] オプションが強調表示された [MDM policy - East]\(MDM ポリシー - 東部\) の [概要] ページ](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>次の手順

- 間違ってグループを削除してしまった場合、それを再作成することができます。 詳細については、[基本グループを作成し、メンバーを追加する方法](active-directory-groups-create-azure-portal.md)に関するページを参照してください。

- Office 365 グループを誤って削除した場合は、それを復元できる場合があります。 詳細については、「[削除済みの Office 365 グループを復元する](../users-groups-roles/groups-restore-deleted.md)」を参照してください。

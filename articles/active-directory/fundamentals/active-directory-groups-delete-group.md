---
title: グループを削除する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory を使用したグループの削除方法に関する手順です。
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
ms.custom: it-pro, seodec18
ms.openlocfilehash: 6c08022b47fb1a118e95a54eedf9b5239496ebf7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53092232"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Azure Active Directory を使用してグループを削除する
Azure Active Directory (Azure AD) グループはさまざまな理由で削除されますが、通常は次のために行います。

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

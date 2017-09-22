---
title: "Azure Active Directory におけるグループ所有者の管理 | Microsoft Docs"
description: "グループ所有者の管理と、グループを使ってリソースに対するアクセスを管理する方法。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 44350a3c-8ea1-4da1-aaac-7fc53933dd21
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: curtand
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 70be82fdd673c4f245e306b1e1cfdcd94d4dac53
ms.contentlocale: ja-jp
ms.lasthandoff: 09/14/2017

---
# <a name="managing-owners-for-a-group"></a>グループの所有者の管理
リソースの所有者が Azure AD グループにリソースに対するアクセス権を割り当てると、その後はグループの所有者がそのグループのメンバーシップを管理することになります。 実質的には、リソースの所有者が、ユーザーをリソースに割り当てる権限をグループの所有者に委任することになります。

## <a name="add-an-owner-to-a-group"></a>グループに所有者を追加する

1. [Azure AD 管理センター](https://aad.portal.azure.com)で、**[ユーザーとグループ]** を選択します。
2. **[すべてのグループ]** を選択し、所有者を追加するグループを開きます。
3. **[所有者]**、**[所有者の追加]** の順に選択します。
4. **[所有者の追加]** ページで、このグループの所有者として追加するユーザーを選んで**[選択]** をクリックまたはタップします。 

## <a name="remove-an-owner-from-a-group"></a>グループから所有者を削除する

1. [Azure AD 管理センター](https://aad.portal.azure.com)で、**[ユーザーとグループ]** を選択します。
2. **[すべてのグループ]** を選択し、所有者を削除するグループを開きます。
3. **[所有者]** を選択し、このグループから削除する所有者を選んで **[選択]** をクリックまたはタップします。
4. 選択した所有者のウィンドウが開くので、そこで **[削除]** を選択します。

## <a name="additional-information"></a>追加情報
次の記事は、Azure Active Directory グループに関する追加情報を提供します。

* [既存のグループの表示](active-directory-groups-view-azure-portal.md)
* [新しいグループの作成とメンバーの追加](active-directory-groups-create-azure-portal.md)
* [グループの設定の管理](active-directory-groups-settings-azure-portal.md)
* [グループのメンバーシップの管理](active-directory-groups-membership-azure-portal.md)
* [グループ内のユーザーの動的ルールの管理](active-directory-groups-dynamic-membership-azure-portal.md)


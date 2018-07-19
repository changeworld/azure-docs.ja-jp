---
title: グループを使用したアクセス管理のための次のステップ - Azure AD | Microsoft Docs
description: セキュリティ グループの管理方法とそれらのグループを使用してリソースへのアクセスを管理する方法を上級者向けに説明しています。
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/12/2017
ms.author: lizross
ms.custom: it-pro
ms.openlocfilehash: bdc8754253ce2567d957b4d6240fe52242aea2ea
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448946"
---
# <a name="managing-owners-for-a-group"></a>グループの所有者の管理
リソースの所有者がリソースへのアクセスを Azure AD グループに割り当てたら、そのグループの所有者がグループのメンバーシップを管理します。 実質的には、リソースの所有者が、ユーザーをリソースに割り当てる権限をグループの所有者に委任することになります。

## <a name="add-an-owner-to-a-group"></a>グループに所有者を追加する

1. [Azure AD 管理センター](https://aad.portal.azure.com)で、**[ユーザーとグループ]** を選択します。
2. **[すべてのグループ]** を選択し、所有者を追加するグループを開きます。
3. **[所有者の追加]** を選択します。
4. **[所有者の追加]** ページで、このグループの所有者として追加するユーザーを選択し、その名前が **[選択済み]** ウィンドウに追加されていることを確認します。

## <a name="remove-an-owner-from-a-group"></a>グループから所有者を削除する

1. [Azure AD 管理センター](https://aad.portal.azure.com)で、**[ユーザーとグループ]** を選択します。
2. **[すべてのグループ]** を選択し、所有者を削除するグループを開きます。
3. **[所有者]** タブをクリックします。
4. このグループから削除する所有者を選択し、 **[削除]** をクリックします。

## <a name="additional-information"></a>追加情報
次の記事は、Azure Active Directory に関する追加情報を示します。

* [Azure Active Directory グループによるリソースへのアクセス管理](active-directory-manage-groups.md)
* [グループの設定を構成するための Azure Active Directory コマンドレット](../users-groups-roles/groups-settings-cmdlets.md)
* [Azure Active Directory のアプリケーション構成の管理に関する記事の索引](../active-directory-apps-index.md)
* [Azure Active Directory とは](active-directory-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](../connect/active-directory-aadconnect.md)

---
title: グループのメンバーと所有者を検索してフィルター処理する (プレビュー) - Azure Active Directory |Microsoft Docs
description: Azure portal でグループのメンバーと所有者を検索し、フィルター処理します。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 10/22/2021
ms.author: curtand
ms.reviewer: jodah
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8774bef1dd939daea4384a25bc35d22f3475f57a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049615"
---
# <a name="search-groups-and-members-in-azure-active-directory"></a>Azure Active Directory でグループとメンバーを検索する

この記事では、グループのメンバーと所有者を検索する方法と、Azure Active Directory (Azure AD) ポータルで検索フィルターを使用する方法について説明します。 グループの検索機能には、次のようなものがあります。

- グループ名の部分文字列検索などのグループ検索機能
- メンバーと所有者のリストに対するフィルター処理および並べ替えオプション
- メンバーと所有者のリストの検索機能

## <a name="group-search-and-sort"></a>グループの検索と並べ替え

**[すべてのグループ]** ページで、検索文字列を入力すると、 **[すべてのグループ]** ページのみで "contains" と "search with" の検索を切り替えることができるようになりました。 部分文字列検索は単語全体でのみ行われ、特殊文字の検索は AND 検索でも行われます。 たとえば、-Name を検索すると、部分文字列 "Name" の検索と "-" の検索が開始されます。 Substring 検索では大文字と小文字が区別されます。 オブジェクト ID または mailNickname プロパティも検索されます。

![[すべてのグループ] ページでの新しい部分文字列検索](./media/groups-members-owners-search/members-list.png)

たとえば、"policy" を検索すると、"MDM policy – West" と "Policy group" の両方が返されます。 "New_policy" という名前のグループは返されません。 **[すべてのグループ]** リストは、昇順または降順で名前別に並べ替えることができます。

## <a name="group-member-search-and-filter"></a>グループ メンバーの検索とフィルター処理

### <a name="search-group-member-and-owner-lists"></a>グループ メンバーと所有者のリストを検索する

特定のグループのメンバーまたは所有者を名前で検索できます。検索文字列を入力すると、`contains` 検索が自動的に行われます。 たとえば、"Scott" を検索すると、Scott Wilkinson と Maya Scott の両方が返されます。

![グループ メンバーと所有者のリストでの新しい部分文字列の検索](./media/groups-members-owners-search/groups-search-preview.png)

### <a name="filter-member-and-owner-lists"></a>メンバーと所有者のリストをフィルター処理する

ユーザーの種類別にグループ メンバーと所有者のリストをフィルター処理することもできます。 この情報は、メンバーまたは所有者リストの **[ユーザーの種類]** 列に示されます。 リストをフィルター処理して、メンバーまたはゲストだけを表示することができます。

**[メンバー]** ページには、グループ メンバーシップを別のグループから継承したユーザーを含む、グループのすべての一意のメンバーが含まれます。

また、リストの検索とフィルター処理を個別に行うこともできます。 すべてのメンバー リストをフィルター処理しても、直接メンバー リストに適用されているフィルターには影響しません。

## <a name="group-memberships"></a>グループ メンバーシップ

また、 **[グループ メンバーシップ]** ページでグループのグループ メンバーシップを表示することもできます。 **[グループ メンバーシップ]** ページでは、他のグループ ページに似た検索、並べ替え、およびフィルター操作がサポートされます。

## <a name="group-member-counts"></a>グループ メンバー数

グループの **[概要]** ページには、グループのメンバー数が示されます。 **[概要]** ページでは、グループの直接メンバーの総数と、メンバーシップの合計数 (継承されたメンバーシップを含むグループのすべての一意のメンバー) を確認できます。

![グループ メンバーシップ数の精度の向上](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>次のステップ

これらの記事では、Azure AD でのグループの操作に関する追加情報が提供されます。

- [グループとメンバーを表示する](../fundamentals/active-directory-groups-view-azure-portal.md)
- [グループ メンバーシップを管理する](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [グループ内のユーザーの動的ルールの管理](groups-create-rule.md)
- [グループの設定を編集する](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [グループを使用したリソースへのアクセス管理](../fundamentals/active-directory-manage-groups.md)
- [グループを使用して SaaS アプリへのアクセスを管理する](groups-saasapps.md)
- [PowerShell コマンドを使用してグループを管理する](../enterprise-users/groups-settings-v2-cmdlets.md)
- [Azure サブスクリプションを Azure Active Directory に追加する](../fundamentals/active-directory-how-subscriptions-associated-directory.md)

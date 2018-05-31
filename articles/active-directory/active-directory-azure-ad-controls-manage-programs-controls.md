---
title: Azure AD アクセス レビューに使用するプログラムとコントロールの管理 | Microsoft Docs
description: 社内のコンプライアンスへの取り組みやリスク管理、ガバナンスごとに追加のプログラムを作成することで、Azure Active Directory のアクセス レビューをコントロールとしてまとめ、体系化することができます。
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: billmath
ms.openlocfilehash: a3fb812623b490e27907f63c1f7c6610ae754fb8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192882"
---
# <a name="manage-programs-and-their-controls"></a>プログラムとそのコントロールの管理 

Azure Active Directory (Azure AD) には、グループ メンバーのアクセス レビューとアプリケーション アクセスのアクセス レビューが備わっています。 これらのレビューは、組織のグループ メンバーシップとアプリケーションに対するアクセス権の所在管理を徹底するコントロールの例です。 こうしたコントロールによって、企業はそのガバナンス、リスク管理、コンプライアンス要件に効率よく対応することができます。

## <a name="create-and-manage-programs-and-their-controls"></a>プログラムとそのコントロールを作成、管理する
目的の異なるさまざまなアクセス レビューは、プログラムにまとめることにより、その追跡と収集を省力化することができます。 それぞれのアクセス レビューを特定のプログラムに関連付けることができるので、 それにより、監査担当者へのレポートを準備する場合は、特定の取り組みの範囲内のアクセス レビューに焦点を絞ることができます。  プログラムおよびアクセス レビューの結果は、グローバル管理者、セキュリティ管理者、またはセキュリティ閲覧者の各役割にあるユーザーに表示されます。

プログラムの一覧を表示するには、[アクセス レビュー ページ](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)に移動し、**[プログラム]** を選択します。

"**既定のプログラム**" というプログラムが常に存在します。 グローバル管理者ロールのユーザーであれば、新たにプログラムを作成することができます。 たとえば、コンプライアンスの取り組みやビジネス目標ごとにプログラムを 1 つ用意することができます。

不要になったためコントロールが関連付けられていないプログラムは削除することができます。

## <a name="next-steps"></a>次の手順

- [グループのメンバーまたはアプリケーションへのアクセスのアクセス レビューを作成する](active-directory-azure-ad-controls-create-access-review.md)
- [アクセス レビューの結果を取得する](active-directory-azure-ad-controls-retrieve-access-review.md)

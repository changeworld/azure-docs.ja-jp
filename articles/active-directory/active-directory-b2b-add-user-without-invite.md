---
title: 招待を使用せずに B2B コラボレーション ユーザーを Azure Active Directory に追加する | Microsoft Docs
description: Azure Active Directory B2B コラボレーションの招待を利用しないでゲスト ユーザーが他のゲスト ユーザーを Azure AD に追加することができます。
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 5a37a5a14dcb07db7e078558072f7edad7432d9b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075625"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>招待を使用せずに B2B コラボレーション ゲスト ユーザーを追加する

> [!NOTE]
> これで、特殊な場合を除き、ゲスト ユーザーは招待メールを必要としなくなります。 詳細については、「[B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)」を参照してください。  

パートナーの担当者などのユーザーに、招待を利用しなくてもパートナーのユーザーを組織に追加することを許可できます。 行う必要があるのは、パートナー組織に使用しているディレクトリにユーザー列挙権限を付与することだけです。 

これらの権限は次の場合に付与します。

1. ホスト組織のユーザー (たとえば、WoodGrove) が、パートナー組織の 1 人のユーザー (たとえば、Sam@litware.com) をゲストとして招待します。
2. ホスト組織の管理者が、取引先組織 (Litware) の他のユーザーを特定し追加することを Sam に許可する[ポリシーをセットアップ](active-directory-b2b-delegate-invitations.md)します。
3. これで、Sam は Litware の他のユーザーを WoodGrove のディレクトリ、グループ、またはアプリケーションに追加できます。招待に応じる操作は必要ありません。 Sam が Litware での適切な列挙権限を持っていれば、自動的に処理されます。

### <a name="next-steps"></a>次の手順

- [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
- [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-iw-add-users.md)
- [B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)
- [Azure Active Directory B2B コラボレーションの招待の委任](active-directory-b2b-delegate-invitations.md)


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
ms.openlocfilehash: 589d9a417dae5c40d24d25c4ef864ce903fbfbe3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259579"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>招待を使用せずに B2B コラボレーション ゲスト ユーザーを追加する

> [!NOTE]
> これで、特殊な場合を除き、ゲスト ユーザーは招待メールを必要としなくなります。 詳細については、「[B2B コラボレーションの招待の利用](redemption-experience.md)」を参照してください。  

パートナーの担当者などのユーザーに、招待を利用しなくてもパートナーのユーザーを組織に追加することを許可できます。 行う必要があるのは、パートナー組織に使用しているディレクトリにユーザー列挙権限を付与することだけです。 

これらの権限は次の場合に付与します。

1. ホスト組織のユーザー (たとえば、WoodGrove) が、パートナー組織の 1 人のユーザー (たとえば、Sam@litware.com) をゲストとして招待します。
2. ホスト組織の管理者が、取引先組織 (Litware) の他のユーザーを特定し追加することを Sam に許可する[ポリシーをセットアップ](delegate-invitations.md)します。
3. これで、Sam は Litware の他のユーザーを WoodGrove のディレクトリ、グループ、またはアプリケーションに追加できます。招待に応じる操作は必要ありません。 Sam が Litware での適切な列挙権限を持っていれば、自動的に処理されます。

### <a name="next-steps"></a>次の手順

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)
- [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](add-users-information-worker.md)
- [B2B コラボレーションの招待の利用](redemption-experience.md)
- [Azure Active Directory B2B コラボレーションの招待の委任](delegate-invitations.md)


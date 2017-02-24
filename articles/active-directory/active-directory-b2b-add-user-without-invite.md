---

title: "招待を使用せずに B2B コラボレーション ユーザーを Azure Active Directory に追加する | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーションを使用すると、インフォメーション ワーカーは組織のユーザーを Azure AD に追加して、会社のアプリケーションにアクセスできるようにすることができます"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/10/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0eef684115f9c21ea61502a10576f74cac0ace8e
ms.openlocfilehash: 8fa8ba169ca85c33e52eee4a7cd5b84aa4012673


---

# <a name="add-b2b-collaboration-users-without-an-invitation"></a>招待を使用せずに B2B コラボレーション ユーザーを追加する

招待元が、追加ユーザーのいるパートナー組織のディレクトリに対する列挙権限があるロールに属している場合、招待されるユーザーは招待なしで招待元組織に追加されます。

これが最も役に立つのは、次のシナリオです。

1. ホスト組織のユーザー (たとえば、WoodGrove) が、パートナー組織の&1; 人のユーザー (たとえば、Sam@litware.com) をゲストとして招待します。
2. ホスト組織の管理者が、パートナー組織 (Litware) の他のユーザーを特定し追加することを Sam に許可するポリシーをセットアップします。
3. これで、Sam は Litware の他のユーザーを WoodGrove のディレクトリ、グループ、またはアプリケーションに追加できます。招待に応じる操作は必要ありません。 Sam が Litware での適切な列挙権限を持っていれば、自動的に処理されます。

### <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-admin-add-users.md)
* [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-iw-add-users.md)
* [B2B コラボレーションの招待メールの要素](active-directory-b2b-invitation-email.md)
* [B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B コラボレーションのライセンス](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B コラボレーションのトラブルシューティング](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B コラボレーションの API とカスタマイズ](active-directory-b2b-api.md)
* [B2B コラボレーション ユーザーの多要素認証](active-directory-b2b-mfa-instructions.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->



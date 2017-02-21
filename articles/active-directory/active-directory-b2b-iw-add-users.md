---

title: "インフォメーション ワーカーが B2B コラボレーション ユーザーを Azure Active Directory に追加する方法 | Microsoft Docs"
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
ms.date: 02/02/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d9ffd4176e87b6b5ada882ff09f507665bda7b1d
ms.openlocfilehash: 7bad8269c3756979fe48d130a0cdfb02ceafad86


---

# <a name="how-do-information-workers-add-b2b-collaboration-users-to-azure-active-directory"></a>インフォメーション ワーカーが B2B コラボレーション ユーザーを Azure Active Directory に追加する方法

インフォメーション ワーカーは、[アプリケーション アクセス パネル](http://myapps.microsoft.com)を使用して、自分が管理しているグループとアプリケーションに B2B コラボレーション ユーザーを追加できます。

## <a name="information-workers-adding-b2b-collaboration-users-to-an-application"></a>B2B コラボレーション ユーザーをアプリケーションに追加するインフォメーション ワーカー
次のビデオで示すように、B2B コラボレーション ユーザーをパートナー組織のインフォメーション ワーカーとしてアプリに割り当てます。

  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/information-worker-assign-to-apps]

  このビデオが埋め込みで表示されない場合は、[こちら](https://channel9.msdn.com/Blogs/Azure/information-worker-assign-to-apps)からアクセスできます。

## <a name="information-workers-adding-b2b-collaboration-users-to-a-group"></a>B2B コラボレーション ユーザーをグループに追加するインフォメーション ワーカー

インフォメーション ワーカーは、セルフサービスのグループ管理が有効になっている割り当て済みグループに B2B コラボレーション ユーザーを同様に追加できます。
> [!NOTE]
B2B コラボレーション ユーザーを、動的グループ、またはオンプレミスの Active Directory と同期しているグループに追加することはできません。

## <a name="add-without-invitation"></a>招待なしの追加

招待元が、追加ユーザーのいるパートナー組織のディレクトリに対する列挙権限があるロールに属している場合、招待されるユーザーは招待なしで招待元組織に追加されます。

これが最も役に立つのは、次のシナリオです。
1. ホスト組織のユーザー (たとえば、WoodGrove) が、パートナー組織の&1; 人のユーザー (たとえば、ゲストとして Sam@litware.com) を招待します。
2. ホスト組織の管理者は、パートナー組織 (Litware) の他のユーザーを特定および追加することを Sam に許可するポリシーをセットアップします。
4. これで、Sam は Litware の他のユーザーを WoodGrove のディレクトリ、グループ、またはアプリケーションに追加できます。招待に応じる操作は必要ありません。 Sam が Litware での適切な列挙権限を持っていれば、自動的に処理されます。


* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-admin-add-users.md)
* [B2B コラボレーションの招待メールの要素](active-directory-b2b-invitation-email.md)
* [B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B コラボレーションのライセンス](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B コラボレーションのトラブルシューティング](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B コラボレーションの API とカスタマイズ](active-directory-b2b-api.md)
* [B2B コラボレーション ユーザーの多要素認証](active-directory-b2b-mfa-instructions.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO1-->



---
title: "Azure Active Directory B2B コラボレーション プレビューの外部ユーザー オブジェクト属性の変更 | Microsoft Docs"
description: "Azure Active Directory B2B は、会社のアプリケーションにビジネス パートナーが選択的にアクセスできるようにすることで、会社間のリレーションシップをサポートします"
services: active-directory
documentationcenter: 
author: viv-liu
manager: cliffdi
editor: 
tags: 
ms.assetid: 4ede9c7b-3830-42f2-b73d-cbecbcc7db64
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/09/2016
ms.author: viviali
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d57fd7d6dcd186d1e79528c716d8bf81ad868ae5


---
# <a name="azure-ad-b2b-collaboration-preview-external-user-object-attribute-changes"></a>Azure AD B2B コラボレーション プレビュー: 外部ユーザー オブジェクト属性の変更
Azure AD ディレクトリ内の各ユーザーは、ユーザー オブジェクトで表されます。 Azure AD のユーザー オブジェクトは、B2B コラボレーションの招待承諾フローのさまざまな段階で属性が変更されます。 ディレクトリ内のパートナー ユーザーを表すユーザー オブジェクトには、承諾時 (パートナー ユーザーが招待メール内のリンクをクリックしたとき) に変更される属性があります。 具体的には次の処理が行われます。

* **SignInName** 属性と **AltSecId** 属性が設定されます。
* **DisplayName** 属性が、ユーザー プリンシパル名 ((user_fabrikam.com#EXT#@contoso.com)) からサインイン名 ((user@fabrikam.com)) に変更されます。

Azure AD でこれらの属性を追跡すると、パートナー ユーザーが B2B コラボレーションへの招待を承諾したかどうかに関するトラブルシューティングに役立ちます。

## <a name="related-articles"></a>関連記事
Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [動作のしくみ](active-directory-b2b-how-it-works.md)
* [詳細なチュートリアル](active-directory-b2b-detailed-walkthrough.md)
* [CSV ファイル形式リファレンス](active-directory-b2b-references-csv-file-format.md)
* [外部ユーザー トークンの形式](active-directory-b2b-references-external-user-token-format.md)
* [現在のプレビューの制限事項](active-directory-b2b-current-preview-limitations.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)




<!--HONumber=Dec16_HO5-->



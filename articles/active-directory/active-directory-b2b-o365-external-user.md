---
title: "Office 365 の外部共有と Azure Active Directory B2B コラボレーション | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーションの要求マッピング リファレンス"
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
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: 716d19be09085db91c17d7ed76422e05d164d137


---

# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Office 365 の外部共有と Azure Active Directory B2B コラボレーション

Office&365; の外部共有 (OneDrive、SharePoint Online、統合グループなど) と Azure Active Directory (Azure AD) B2B コラボレーションを使用することは、技術的には同じことです。 **統合グループのゲスト**など、すべての外部共有 (OneDrive/SharePoint Online を除く) は、共有のために既に Azure AD B2B コラボレーションの招待 API を使用しています。

OneDrive/SharePoint Online (ODSP) は、独自の招待マネージャーを備えています。これは、ODSP の外部共有サポートが、Azure AD ファブリックの一部として外部共有がサポートされる前に開始されたためです。 時間の経過と共に、ODSP 外部共有にはいくつかの機能が追加され、製品の組み込み共有パターンを使用するユーザーが数百万人に増加しました。 Microsoft は、(このドキュメントで言及されている) Azure AD B2B の招待 API の提供を開始するために、ODSP での作業を進めています。これにより、すべてのエンド ツー エンドの利点が統合され、Azure AD によって得られたすべての革新的な技術がそれらに集約されます。 それまでの間は、ODSP の外部共有の動作と Azure AD B2B の動作の間に、いくつかの微妙な違いがあります。

1. ODSP では、ユーザーが招待に応じた後で、ユーザーがディレクトリに追加されます。 そのため、ユーザーが応じるまでは、Azure AD ポータルでユーザーが実際には表示されません。 その間にユーザーが別のサイトから招待された場合は、新しい招待が生成されます。 しかし、B2B コラボレーションを使用する場合は、招待時にユーザーがすぐに追加されるので、すべての場所でユーザーが表示されるようになります。

2. ODSP で招待に応じるためのエクスペリエンスは、B2B コラボレーションのものとは異なっています。

3. しかし、ユーザーが招待に応じた後は、同じような外観になります。

4. B2B コラボレーションで招待されたユーザーは、ODSP 共有のダイアログで選択することができます。 ODSP で招待されたユーザーも、招待に応じた後は、Azure AD に表示されます。

5. 管理者に制御された方法で、ODSP の外部共有を B2B コラボレーションと共に使用するには、ODSP の外部共有の設定を**既にディレクトリに存在する外部ユーザーのみに共有を許可する**ように指定します。 ユーザーは外部の共有サイトに移動し、管理者が追加した外部コラボレーターを選択できます。 管理者は、B2B コラボレーションの招待 API を通じて、外部コラボレーターを追加できます。

![ODSP の外部共有の設定](media/active-directory-b2b-o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B コラボレーション ユーザーのプロパティ](active-directory-b2b-user-properties.md)
* [B2B コラボレーション ユーザーのロールへの追加](active-directory-b2b-add-guest-to-role.md)
* [B2B コラボレーションの招待の委任](active-directory-b2b-delegate-invitations.md)
* [動的グループと B2B コラボレーション](active-directory-b2b-dynamic-groups.md)
* [B2B コラボレーション コードと PowerShell サンプル](active-directory-b2b-code-samples.md)
* [B2B コラボレーション用の SaaS アプリの構成](active-directory-b2b-configure-saas-apps.md)
* [B2B コラボレーション ユーザーのトークン](active-directory-b2b-user-token.md)
* [B2B コラボレーション ユーザーの要求マッピング](active-directory-b2b-claims-mapping.md)
* [B2B コラボレーションの現在の制限](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->



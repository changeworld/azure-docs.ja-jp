---
title: "Azure Active Directory B2B コラボレーション ユーザーのロールへの追加 | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーション ユーザーのプロパティは構成できます"
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
ms.date: 02/03/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: 9ba46fe5ce6f06a69b057ebcb4af072689ac8ce9


---

# <a name="adding-an-azure-active-directory-b2b-collaboration-user-to-a-role"></a>Azure Active Directory B2B コラボレーション ユーザーのロールへの追加

Azure Active Directory (Azure AD) B2B コラボレーション ユーザーはゲスト ユーザーとしてディレクトリに追加され、ディレクトリのゲスト アクセス許可は既定で制限されていますが、一部のゲスト ユーザーを組織内の特権ロールに入れる必要がある場合があります。 このような場合をサポートするために、ゲスト ユーザーを組織のニーズに合わせて任意のロールに追加することができます。

## <a name="default-role"></a>既定のロール

![既定のロール](media/active-directory-b2b-add-guest-to-role/default-role.png)

## <a name="global-administrator-role"></a>全体管理者ロール

![全体管理者ロール](media/active-directory-b2b-add-guest-to-role/global-admin-role.png)

## <a name="limited-administrator-role"></a>制限付き管理者ロール

![制限付き管理者ロール](media/active-directory-b2b-add-guest-to-role/limited-admin-role.png)

## <a name="auditing-and-reporting"></a>監査とレポート
ゲスト ユーザーに対しても、メンバー ユーザーに対する場合と同様に、監査機能を使用できるようになりました。 以下に、招待されただけの Sam Oogle の招待と使用の履歴の例を示します。

![監査ログ](media/active-directory-b2b-add-guest-to-role/audit-log.png)

各イベントに立ち入って、詳細を取得することができます。 たとえば、承認の詳細を見てみましょう。

![活動の詳細](media/active-directory-b2b-add-guest-to-role/activity-details.png)

これらのログを Azure AD からエクスポートし、任意のレポート ツールを使用してレポートをカスタマイズすることもできます。

## <a name="resending-invitations"></a>招待の再送信
B2B コラボレーション ユーザーのプロファイル ページに移動し、なんらかの理由でまだ使用していないゲスト ユーザーに招待を再送信することができるようになりました。

![招待の再送信](media/active-directory-b2b-add-guest-to-role/resend-invitation.png)

> ![注] サインインしたユーザーからの招待が再送信され、元の招待が特定のアプリまたはグループ宛てであった場合でも、ユーザーがディレクトリに招待されます。

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B コラボレーション ユーザーのプロパティ](active-directory-b2b-user-properties.md)
* [B2B コラボレーションの招待の委任](active-directory-b2b-delegate-invitations.md)
* [動的グループと B2B コラボレーション](active-directory-b2b-dynamic-groups.md)
* [B2B コラボレーション コードと PowerShell サンプル](active-directory-b2b-code-samples.md)
* [B2B コラボレーション用の SaaS アプリの構成](active-directory-b2b-configure-saas-apps.md)
* [B2B コラボレーション ユーザーのトークン](active-directory-b2b-user-token.md)
* [B2B コラボレーション ユーザーの要求マッピング](active-directory-b2b-claims-mapping.md)
* [Office 365 の外部共有](active-directory-b2b-o365-external-user.md)
* [B2B コラボレーションの現在の制限](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->



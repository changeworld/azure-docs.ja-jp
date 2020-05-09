---
title: Azure Active Directory の条件付きアクセスに関する FAQ | Microsoft Docs
description: Azure Active Directory の条件付きアクセスに関してよく寄せられる質問への回答を示します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6842338bd27e4bea3436f0b249380ab773d60de6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "77368077"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Azure Active Directory の条件付きアクセスに関する FAQ

## <a name="which-applications-work-with-conditional-access-policies"></a>条件付きアクセス ポリシーを使用するアプリケーションにはどのようなものがありますか?

条件付きアクセス ポリシーを使用するアプリケーションについては、「[Azure Active Directory の条件付きアクセス規則を使用するアプリケーションとブラウザー](concept-conditional-access-cloud-apps.md)」を参照してください。

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>条件付きアクセス ポリシーは、B2B コラボレーション ユーザーおよびゲスト ユーザーには適用されますか?

企業間 (B2B) コラボレーション ユーザーにはポリシーが適用されます。 ただし、ユーザーはポリシー要件を満たすことができない場合があります。 たとえば、ゲスト ユーザーの組織では多要素認証がサポートされていないことがあります。 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>SharePoint Online ポリシーは、OneDrive for Business にも適用されますか?

はい。 SharePoint Online ポリシーは OneDrive for Business にも適用されます。

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>Word や Outlook などのクライアント アプリに直接ポリシーを設定できないのはなぜですか?

条件付きアクセス ポリシーでは、サービスにアクセスするための要件を設定します。 このポリシーは、そのサービスに対する認証が行われる際に適用されます。 ポリシーは、クライアント アプリケーションでは直接には設定されません。 代わりに、クライアントがサービスを呼び出す際に適用されます。 たとえば、SharePoint で設定したポリシーは SharePoint を呼び出すクライアントに適用されます。 Exchange で設定したポリシーは Outlook に適用されます。

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>条件付きアクセス ポリシーはサービス アカウントに適用されますか?

条件付きアクセス ポリシーは、すべてのユーザー アカウントに適用されます。 これには、サービス アカウントとして使用されるユーザー アカウントも含まれます。 多くの場合、無人で実行されるサービス アカウントは、条件付きアクセス ポリシーの要件を満たすことができません。 たとえば、多要素認証が必要になる場合があります。 サービス アカウントは、条件付きアクセス ポリシーの管理設定を使用してポリシーから除外できます。 

## <a name="are-microsoft-graph-apis-available-for-configuring-conditional-access-policies"></a>条件付きアクセス ポリシーの構成に Microsoft Graph API は利用できますか?

現時点では利用できません。 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>サポートされていないデバイス プラットフォームに対する既定の除外ポリシーとは何ですか?

現在のところ、条件付きアクセス ポリシーは、iOS デバイスおよび Android デバイスのユーザーに選択的に適用されます。 その他のデバイス プラットフォームのアプリケーションは、既定では、iOS デバイスおよび Android デバイスの条件付きアクセス ポリシーの影響を受けません。 テナント管理者は、サポートされていないプラットフォームのユーザーへのアクセスを許可しないように、グローバル ポリシーをオーバーライドすることを選択できます。

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>条件付きアクセス ポリシーは Microsoft Teams にどのように作用しますか?

Microsoft Teams は、会議、カレンダー、ファイル共有などの主要な生産性シナリオに関して、Exchange Online と SharePoint Online に大きく依存しています。 これらのクラウド アプリに設定されている条件付きアクセス ポリシーは、ユーザーが Microsoft Teams に直接サインインした場合に、Microsoft Teams に適用されます。

Microsoft Teams はまた、Azure Active Directory の条件付きアクセス ポリシーでクラウド アプリとして個別にサポートされています。 クラウド アプリに設定されている条件付きアクセス ポリシーは、ユーザーのサインイン時に Microsoft Teams に適用されます。 ただし、Exchange Online や SharePoint Online などの他のアプリには適正なポリシーがなく、ユーザーは引き続きそれらのリソースに直接アクセスできる場合があります。

Windows 版および Mac 版の Microsoft Teams デスクトップ クライアントでは、先進認証がサポートされています。 先進認証では、Azure Active Directory Authentication Library (ADAL) に基づいて、プラットフォームでの Microsoft Office クライアント アプリケーションへのサインインが行われます。

## <a name="next-steps"></a>次のステップ

- 環境に適用する条件付きアクセス ポリシーを構成するには、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](best-practices.md)」を参照してください。 

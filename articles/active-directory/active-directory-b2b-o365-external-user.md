---
title: Office 365 の外部共有と Azure Active Directory B2B コラボレーション | Microsoft Docs
description: O365 および Azure Active Directory B2B コラボレーションを使用する外部パートナーとのリソースの共有について説明します。
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/24/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 4421e6ca2bdce721c360f2701b3693024a694eb6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33931964"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Office 365 の外部共有と Azure Active Directory B2B コラボレーション

Office 365 の外部共有 (OneDrive、SharePoint Online、統合グループなど) と Azure Active Directory (Azure AD) B2B コラボレーションは、技術的には同じことです。 Office 365 グループのゲストなど、すべての外部共有 (OneDrive/SharePoint Online を除く) は、共有のために既に Azure AD B2B コラボレーションの招待 API を使用しています。

OneDrive/SharePoint Online には、独自の招待マネージャーがあります。 OneDrive/SharePoint Online での外部共有のサポートは、Azure AD でのサポートより前に開始されました。 時間の経過と共に、OneDrive/SharePoint Online の外部共有にはいくつかの機能が追加され、製品の組み込み共有パターンを使用するユーザーが数百万人に増加しました。 ただし、OneDrive/SharePoint Online の外部共有の動作と Azure AD B2B コラボレーションの動作の間に、次のようないくつかの微妙な違いがあります。

- OneDrive/SharePoint Online は、ユーザーが招待に応じた後で、ユーザーをディレクトリに追加します。 そのため、応じる前は Azure AD ポータルにユーザーが表示されません。 その間に別のサイトがユーザーを招待すると、新しい招待が生成されます。 一方、Azure AD B2B コラボレーションを使用している場合は、ユーザーがすべての場所に表示されるように、招待後すぐにユーザーが追加されます。

- OneDrive/SharePoint Online で招待に応じるためのエクスペリエンスは、Azure AD B2B コラボレーションでのエクスペリエンスとは外観が異なります。 ユーザーが招待に応じた後のエクスペリエンスは似ています。

- Azure AD B2B コラボレーションで招待されたユーザーは、OneDrive/SharePoint Online 共有の各ダイアログ ボックスで選択できます。 OneDrive/SharePoint Online で招待されたユーザーも、招待に応じた後は、Azure AD に表示されます。

- OneDrive/SharePoint Online と Azure AD B2B コラボレーションの外部共有を管理するには、OneDrive/SharePoint Online の外部共有の設定を **[Only allow sharing with external users already in the directory]\(既にディレクトリに存在する外部ユーザーのみに共有を許可する\)** に設定します。 ユーザーは外部の共有サイトに移動し、管理者が追加した外部コラボレーターを選択できます。 管理者は、B2B コラボレーションの招待 API を通じて、外部コラボレーターを追加できます。

![OneDrive/SharePoint Online の外部共有の設定](media/active-directory-b2b-o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>次の手順

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B コラボレーション ユーザーのロールへの追加](active-directory-b2b-add-guest-to-role.md)
* [B2B コラボレーションの招待の委任](active-directory-b2b-delegate-invitations.md)
* [動的グループと B2B コラボレーション](active-directory-b2b-dynamic-groups.md)
---
title: 多要素認証を要求する方法 | Microsoft Docs
description: Azure Active Directory Privileged Identity Management 拡張機能で特権 ID の多要素認証 (MFA) を要求する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 8e1c0fa212b31c05fcc4559f9f8d42b627f0da0e
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622849"
---
# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management で MFA を要求する方法
すべての管理者に多要素認証 (MFA) を要求することをお勧めします。 これにより、パスワードの漏洩による攻撃のリスクが軽減されます。

MFA チャレンジは、ユーザーがサインインしたときに完了するよう要求できます。 ブログ記事「 [MFA for Office 365 and MFA for Azure (Office 365 の MFA と Azure の MFA)](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) 」で、Office と Azure のサブスクリプションに含まれているものと、Microsoft Azure Multi-Factor Authentication サービスに含まれている機能が比較されています。

ユーザーが Azure AD PIM でロールをアクティブ化するときに MFA チャレンジを完了するよう要求することもできます。 この場合は、サインイン時に MFA チャレンジを完了していないユーザーには、完了するよう求めるメッセージが PIM に表示されます。

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management での MFA の要求
特権ロール管理者として PIM で ID を管理していると、特権アカウントの MFA を推奨するアラートが表示されることがあります。 PIM ダッシュボードでそのセキュリティ アラートをクリックすると、MFA を必要とする管理者アカウントの一覧が新しいブレードに表示されます。  MFA を要求するには、複数のロールを選択して **[修正]** ボタンをクリックするか、個別のロールの横にある省略記号をクリックして **[修正]** ボタンをクリックします。

> [!IMPORTANT]
> 現時点では、Azure MFA は職場または学校のアカウントでのみ機能します。Microsoft アカウント (通常は、Skype、Xbox、Outlook.com などの Microsoft サービスにサインインするために使用される個人アカウント) では機能しません。 このため、Microsoft アカウントを使用するユーザーは対象管理者にすることはできません。ロールをアクティブ化するために MFA を使用することができないからです。 Microsoft アカウントを使用してワークロードの管理を続行する必要がある場合は、ここで永続的な管理者に昇格させてください。
> 
> 

また、特定のロールの MFA 要件を変更するには、PIM ダッシュボードの [ロール] セクションでそのロールをクリックします。 ロール ブレードで **[設定]** をクリックし、[Multi-Factor Authentication] の **[有効にする]** をクリックします。

## <a name="how-azure-ad-pim-validates-mfa"></a>Azure AD PIM で MFA を検証する方法
ユーザーがロールをアクティブ化するときに MFA を検証するためのオプションは 2 つあります。

最も簡単な方法は、特権ロールをアクティブ化するユーザーに対して Azure MFA を使用することです。 これを行うには、先に、そのユーザーにライセンスが付与されていることと (必要な場合)、Azure MFA に登録されていることを確認する必要があります。 この方法の詳細については、「 [クラウドでの Azure Multi-Factor Authentication Server の概要](../authentication/howto-mfa-getstarted.md)」を参照してください。 ユーザーのサインイン時に MFA を適用するように Azure AD を構成することは、必須ではありませんが、お勧めします。 MFA は Azure AD PIM 自体で確認されるためです。

また、ユーザーをオンプレミスで認証する場合は、ID プロバイダーで MFA を行うようにすることができます。 たとえば、Azure AD にアクセスする前にスマートカード ベースの認証を要求するように AD フェデレーション サービスを構成した場合は、「 [Azure Multi-Factor Authentication および AD FS を使用したクラウド リソースのセキュリティ保護](../authentication/howto-mfa-adfs.md) 」の手順に従って、Azure AD に要求を送信するように AD FS を構成します。 ユーザーがロールをアクティブ化しようとしたとき、Azure AD PIM は、適切な要求を受信した後で、ユーザーに対して MFA が検証済みであることを受け入れます。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>次の手順
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]


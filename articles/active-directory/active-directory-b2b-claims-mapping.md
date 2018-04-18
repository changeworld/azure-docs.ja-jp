---
title: Azure Active Directory での B2B コラボレーション ユーザーの要求マッピング | Microsoft Docs
description: Azure Active Directory (Azure AD) B2B ユーザーの SAML トークンで発行されたユーザー要求をカスタマイズします。
services: active-directory
documentationcenter: ''
author: twooley
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/06/2018
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 8f5e471d4e7102300cd5581976b45c9fa8cc57bc
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Azure Active Directory での B2B コラボレーション ユーザーの要求マッピング

Azure Active Directory (Azure AD) では、B2B コラボレーション ユーザーの SAML トークンで発行される要求のカスタマイズがサポートされています。 アプリケーションに対するユーザーの認証時に、Azure AD は、ユーザーを一意に識別する情報 (要求) を含む SAML トークンをアプリに発行します。 既定では、ユーザーのユーザー名、電子メール アドレス、名、および姓が含まれます。

[Azure Portal](https://portal.azure.com) では、アプリケーションに SAML トークンで送信された要求を表示または編集できます。 設定にアクセスするには、**[Azure Active Directory]** > **[エンタープライズ アプリケーション]** > シングル サインオン用に構成されたアプリケーション > **[シングル サインオン]** の順に選択します。 **[ユーザー属性]** セクションの SAML トークン設定を参照してください。

![UI の SAML トークン属性を表示する](media/active-directory-b2b-claims-mapping/view-claims-in-saml-token.png)

SAML トークンで発行された要求を編集する必要がある理由は、2 つ考えられます。

1. アプリケーションで、別の要求 URI または要求値のセットが必要である。

2. アプリケーションで NameIdentifier 要求が Azure AD に格納されているユーザー プリンシパル名 (UPN) 以外のものである必要がある。

要求の追加と編集については、「[Azure Active Directory のエンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](develop/active-directory-saml-claims-customization.md)」を参照してください。

B2B コラボレーション ユーザーの場合は、セキュリティ上の理由で、NameID と UPN クロステナントとのマッピングは許可されていません。

## <a name="next-steps"></a>次の手順

- B2B コラボレーション ユーザーのプロパティについては、「[Azure Active Directory B2B コラボレーション ユーザーのプロパティ](active-directory-b2b-user-properties.md)」を参照してください。
- B2B コラボレーション ユーザーのユーザー トークンについては、「[Azure AD B2B コラボレーションのユーザー トークンについて](active-directory-b2b-user-token.md)」を参照してください。


---
title: "Azure Active Directory B2B コラボレーションの招待の委任 | Microsoft Docs"
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
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c05cd490ee9125f7e5182cb502db6f4e9390094
ms.openlocfilehash: 6c6d757a770613498bedca0f8f3a965241d692eb


---

# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B コラボレーションの招待の委任

Azure Active Directory (Azure AD) B2B コラボレーションのパブリック プレビューの更新では、グローバル管理者でなくてもユーザーを招待することができます。 ポリシーを通じてだれが招待できるかを制御し、招待を許可されているロールのユーザーに招待を委任できます。 ゲスト ユーザーの招待を委任するための重要な新しい方法は、ゲストの招待元ロールを通じた方法です。

## <a name="guest-inviter-role"></a>ゲストの招待元ロール
ゲストの招待元ロールにユーザーを割り当てて、招待を送信することができます。 全体管理者のメンバーでなくても、招待を送信することができます。 既定では、全体管理者が通常のユーザーによる招待を無効にしていなければ、通常のユーザーも招待 API を呼び出すことができます。 この操作は、Azure Portal と PowerShell を通じて行うことができます。

次の例は、PowerShell を使ってゲストの招待元ロールにユーザーを追加する方法を示しています。

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress >
```

## <a name="controlling-who-can-invite"></a>招待できるユーザーの制御

![招待する方法を制御する](media/active-directory-b2b-delegate-invitations/control-who-to-invite.png)

Azure AD B2B コラボレーションでは、テナント管理者が次の招待ポリシーを設定できます。

1. 招待を無効にする
2. 管理者と、ゲストの招待元ロールのユーザーのみが招待を行うことができる
3. 管理者、ゲストの招待元ロール、およびメンバーが招待できる
4. ゲストを含むすべてのユーザーが招待できる

既定では、テナントは #4 (ゲストを含むすべてのユーザーが B2B ユーザーを招待できる) に設定されます。

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B コラボレーション ユーザーのプロパティ](active-directory-b2b-user-properties.md)
* [B2B コラボレーション ユーザーのロールへの追加](active-directory-b2b-add-guest-to-role.md)
* [動的グループと B2B コラボレーション](active-directory-b2b-dynamic-groups.md)
* [B2B コラボレーション コードと PowerShell サンプル](active-directory-b2b-code-samples.md)
* [B2B コラボレーション用の SaaS アプリの構成](active-directory-b2b-configure-saas-apps.md)
* [B2B コラボレーション ユーザーのトークン](active-directory-b2b-user-token.md)
* [B2B コラボレーション ユーザーの要求マッピング](active-directory-b2b-claims-mapping.md)
* [Office 365 の外部共有](active-directory-b2b-o365-external-user.md)
* [B2B コラボレーションの現在の制限](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->



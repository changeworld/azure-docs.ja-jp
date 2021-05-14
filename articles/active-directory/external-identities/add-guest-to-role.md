---
title: B2B コラボレーション ユーザーをロールに追加する - Azure Active Directory
description: Azure Active Directory でゲスト ユーザーをロールに追加します
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 455d0ee4855645104dda662785794225f5a23d2d
ms.sourcegitcommit: 38d81c4afd3fec0c56cc9c032ae5169e500f345d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2021
ms.locfileid: "109520758"
---
# <a name="grant-permissions-to-users-from-partner-organizations-in-your-azure-active-directory-tenant"></a>Azure Active Directory テナントでパートナー組織からユーザーにアクセス許可を付与する

Azure Active Directory (Azure AD) B2B コラボレーション ユーザーはゲスト ユーザーとしてディレクトリに追加され、ディレクトリのゲスト アクセス許可は既定で制限されています。 一部のゲスト ユーザーを組織内の上位の特権ロールに入れる必要がある場合があります。 上位の特権ロールの定義をサポートするために、ゲスト ユーザーを組織のニーズに合わせて任意のロールに追加することができます。

ディレクトリ ロールがゲスト ユーザーに割り当てられている場合は、ロールに付属する追加アクセス許可がゲスト ユーザーに付与されます。これには、基本の読み取りアクセス許可が含まれます。 「[Azure AD の組み込みロール](../roles/permissions-reference.md)」を参照してください。

## <a name="default-role"></a>既定のロール

![既定のディレクトリ ロールを示すスクリーンショット](./media/add-guest-to-role/default-role.png)

## <a name="global-administrator-role"></a>全体管理者ロール

![全体管理者ロールを示すスクリーンショット](./media/add-guest-to-role/global-admin-role.png)

## <a name="limited-administrator-role"></a>制限付き管理者ロール

![制限付き管理者ロールを示すスクリーンショット](./media/add-guest-to-role/limited-admin-role.png)

## <a name="next-steps"></a>次のステップ

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)
- [B2B コラボレーション ユーザーのプロパティ](user-properties.md)
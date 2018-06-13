---
title: Azure Active Directory B2B コラボレーションの制限 | Microsoft Docs
description: Azure Active Directory B2B コラボレーションの現在の制限事項
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: d4efa6ad1d2768a41a670fae4c490942b5a814ad
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33927765"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Azure AD B2B コラボレーションの制限
現在、Azure Active Directory (Azure AD) B2B コラボレーションには、この記事に記載されている制限が適用されます。

## <a name="possible-double-multi-factor-authentication"></a>多要素認証重複の可能性
Azure AD B2B を使用すると、多要素認証をリソース組織 (招待する側の組織) で実施することができます。 このアプローチの理由については、[Conditional access for B2B collaboration users](active-directory-b2b-mfa-instructions.md)を参照してください。 パートナー側で既に多要素認証が設定され、実施されている場合、パートナー側のユーザーは、ホーム組織と招待する側の組織の両方で認証が必要になる可能性があります。

## <a name="instant-on"></a>インスタント オン
B2B コラボレーションのフローでは、ユーザーをディレクトリに追加し、招待の使用、アプリ割り当てなどの際に動的に更新します。 更新と書き込みは、通常、1 つのディレクトリ インスタンスで行い、すべてのインスタンス間でレプリケートする必要があります。 すべてのインスタンスが更新されると、レプリケーションが完了します。 いずれかのインスタンスでオブジェクトの書き込みまたは更新が行われ、そのオブジェクトを取得する呼び出しが別のインスタンスに対して行われた場合は、レプリケーションの遅延が発生する可能性があります。 その場合は、更新または再試行が役立つことがあります。 API を使用してアプリを記述する場合は、バックオフの再試行がこの問題を軽減するための推奨される防御的な方法です。

## <a name="next-steps"></a>次の手順

Azure AD B2B コラボレーションに関する以下の記事を参照してください。

- [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
- [B2B コラボレーションの招待の委任](active-directory-b2b-delegate-invitations.md)


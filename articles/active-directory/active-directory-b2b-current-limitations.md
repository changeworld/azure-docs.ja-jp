---
title: "Azure Active Directory B2B コラボレーションの制限 | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーション プレビューの現在の制限"
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
ms.date: 04/12/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: cdc951d4e16e7f0df425dba7c33d86255276f526
ms.lasthandoff: 04/13/2017


---

# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Azure AD B2B コラボレーションの制限
現在、Azure Active Directory (Azure AD) B2B コラボレーションには、この記事に記載されている制限が適用されます。

## <a name="possible-double-multi-factor-authentication"></a>多要素認証重複の可能性
Azure AD B2B を使用すると、多要素認証をリソース組織 (招待する側の組織) で実施することができます。 このアプローチの理由については、[Conditional access for B2B collaboration users](active-directory-b2b-mfa-instructions.md)を参照してください。 このため、パートナー側で既に多要素認証ポリシーが設定され、実施されていると、パートナー側のユーザーは、自分の組織と招待する側の組織の両方で認証が必要になる可能性があります。

今後のリリースでは、パートナーの多要素認証を信頼して、認証の重複を回避できるポリシーを導入する予定です。


## <a name="instant-on"></a>インスタント オン
B2B コラボレーションのフローでは、ユーザーをディレクトリに追加し、招待の使用、アプリ割り当てなどの際に動的に更新します。 更新と書き込みは、通常、1 つのディレクトリ インスタンスで行い、すべてのインスタンス間でレプリケートする必要があります。 レプリケーションの完了までには、いくらか時間がかかることがあります。 ディレクトリのいずれかのインスタンスでオブジェクトの書き込みまたは更新が行われ、そのオブジェクトを取得する呼び出しが別のインスタンスに負荷分散された場合、そのことが認証の問題の原因になってきました。 このレプリケーションの待機時間をなくしたり短縮したりするために多くの作業を行ってきましたが、依然としてまれに待機時間が発生することがあります。 その場合は、更新または再試行が役立つことがあります。 API を使用してアプリを記述する場合は、バックオフの再試行がこの問題を軽減するための推奨される防御的な方法です。

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
* [Office 365 の外部共有](active-directory-b2b-o365-external-user.md)


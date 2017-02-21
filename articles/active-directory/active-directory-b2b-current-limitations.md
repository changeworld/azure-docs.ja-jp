---
title: "Azure Active Directory B2B コラボレーションの現在の制限 | Microsoft Docs"
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
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: 1c840fe5eaed88215b011d9a80823f5c8399ebfa


---

# <a name="azure-active-directory-b2b-collaboration-current-limitations"></a>Azure Active Directory B2B コラボレーションの現在の制限

1. Azure Active Directory (Azure AD) B2B コラボレーションの招待 API は、ベータ段階です。ただし、すべてのベータ版同様に、この API サーフェスは進行方向であり、ベータ名前空間契約の対象です。 この API は、GA リリースで番号付きのバージョンに移される予定です。
2. 二重の多要素認証 (MFA) の可能性。パートナーが既に MFA ポリシーを実施している場合、招待側の組織で B2B コラボレーション MFA が実行および管理されます。 すべての ID が処理対象になり、B2B コラボレーションで招待されるユーザーの認証の強度を制御できるため、これが望ましい方法です。 ただし、既に MFA をセットアップして強制しているパートナーがいる場合、そのパートナーのユーザーは MFA を自分の組織で&1; 回、招待側の組織でもう&1; 回実行する必要がある場合があります。 将来のリリースでは、特定のパートナーの MFA を信頼することを選択し、二重の MFA の問題を回避できるポリシーを導入する予定です。
3. インスタント オン。B2B コラボレーションのフローでは、ユーザーをディレクトリに追加し、招待の使用、アプリ割り当てなどの際に動的に更新します。 更新と書き込みは、通常、1 つのディレクトリ インスタンスで行われ、すべてのインスタンス間でレプリケートされる必要があります。 一部のケースでは、レプリケーションの完了までにかかる時間が限定されていると、承認問題として認識される問題が生じることがありました。 この一連の問題を、一般公開前に改善および解決できるように努力しています。 それまでの間に、万が一、この問題が発生した場合は、通常は更新や再試行を行うと効果があります。

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



<!--HONumber=Feb17_HO1-->



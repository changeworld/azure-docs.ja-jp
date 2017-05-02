---
title: "Azure Active Directory B2B コラボレーションのためのセルフサービス サインアップ ポータル | Microsoft ドキュメント"
description: "Azure Active Directory B2B コラボレーションは、会社のアプリケーションにビジネス パートナーが選択的にアクセスできるようにすることで会社間のリレーションシップをサポートします"
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
ms.date: 04/11/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: f629eeb6f12c8785cab2585190f70e98b02fa5b4
ms.lasthandoff: 04/12/2017


---


# <a name="self-service-sign-up-portal-for-azure-ad-b2b-collaboration"></a>Azure AD B2B コラボレーションのためのセルフ サービス サインアップ ポータル

顧客は、[Azure Portal](https://portal.azure.com) や、当社の非管理者向け[アプリケーション アクセス パネル](https://myapps.microsoft.com)の当社の IT エクスペリエンスによって公開されている、組み込み製品の多様な機能を使用できます。 しかし、当社では、企業が B2B ユーザーのために配布準備ワークフローをカスタマイズして、ユーザーの組織のニーズを満たす必要があることについても認識しています。 これは[当社の API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) を使用することで実現します。

このことについて当社の多くのお客様と検討するうえで、共通する 1 つのニーズが他の全員に広がることを実感しました。 これは、企業のリソースへのアクセスを必要としているのが、どの社外の個人共同作業者なのか、招待側の組織が事前に知らない可能性がある (あるいは知りたい) というケースでした。 この組織は、招待側の組織による制御が可能なポリシー セットを使用して、パートナー企業のユーザーがセルフサインアップできるようにする方法が必要でした。 これは、当社の API を通じて実現可能です。このため、当社では[サンプル Github プロジェクト](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web)を Github に発行しました。

当社の Github プロジェクトでは、組織による当社の API の使用方法を説明しています。さらに、アクセス権を持つアプリがルールで規定されている、ポリシー ベースのセルフサービス サインアップ機能を信頼されたパートナーに提供しています。 これにより、パートナーのユーザーは、必要なときに適切なリソースを安全に利用することができます。招待側の組織が手動でパートナーに配布準備する必要はありません。 ボタンをクリックするだけで、Azure サブスクリプションに、プロジェクトを簡単にデプロイすることができます。 試しにやってみましょう。

## <a name="as-is-code"></a>現況コード

このコードは、Azure Active Directory B2B 招待 API の使用法を示すサンプルとして提供されているコードであることに注意してください。 開発チームまたはパートナーによってカスタマイズする必要があり、実稼働のシナリオにデプロイする前に確認する必要があります。

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。
* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-admin-add-users.md)
* [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-iw-add-users.md)
* [B2B コラボレーションの招待メールの要素](active-directory-b2b-invitation-email.md)
* [B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B コラボレーションのライセンス](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B コラボレーションのトラブルシューティング](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)](active-directory-b2b-faq.md)
* [B2B コラボレーション ユーザーの多要素認証](active-directory-b2b-mfa-instructions.md)
* [招待を使用せずに B2B コラボレーション ユーザーを追加する](active-directory-b2b-add-user-without-invite.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)

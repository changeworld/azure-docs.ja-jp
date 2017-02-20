---
title: "Azure Active Directory B2B コラボレーション プレビューとは | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーションは、会社のアプリケーションにビジネス パートナーが選択的にアクセスできるようにすることで会社間のリレーションシップをサポートします"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 1464387b-ee8b-4c7c-94b3-2c5567224c27
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/02/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 1c1da3c7b1d5e096e03ac3814275f29afeffcaeb
ms.openlocfilehash: ad03b71052be46bdb64432b007aa471d120db0fa


---

# <a name="what-is-azure-ad-b2b-collaboration-preview"></a>Azure AD B2B コラボレーション プレビューとは

Azure AD B2B コラボレーション機能により、IT プロフェッショナルとインフォメーション ワーカーは、世界中の他の組織のパートナーと密接に連携することができます。 内部データの完全な制御を維持しながら、ドキュメント、リソース、アプリケーションにアクセスできるようになります。 開発者は、Azure AD の企業間 API を使用して、インフォメーション ワーカーに対してシームレスであり、直感的なナビゲートが可能なセキュリティで保護された方法で&2; つの組織を結び付けるアプリケーションを作成できます。

Azure AD B2B コラボレーション機能により、あらゆる業界のあらゆる規模の組織が、コンプライアンスやガバナンスの要件に関係なく、世界中のコラボレーターと簡単かつ安全に連携することができます。 それが、この B2B コラボレーション パブリック プレビュー更新版の目的です。

## <a name="how-does-it-work"></a>それはどのように機能しますか?

現在のプレビュー リリースでは、IT プロフェッショナルとインフォメーション ワーカーは、組織との関係を築くために、ポータルまたは Invitation Manager API を使用して、別の組織のユーザーを一度に&1; 人または数人追加できます。 管理者は、B2B コラボレーションに対応する Azure Portal の新しいポータル エクスペリエンス (https://portal.azure.com) と PowerShell を使用できます。 また、インフォメーション ワーカーは、http://myapps.microsoft.com でアクセス パネル エクスペリエンスを使用できます。 開発者は、B2B コラボレーション ユーザーを追加し、招待および参加ワークフローをカスタマイズするために、Azure AD の B2B Invitation Manager API を使用してアプリケーションを作成できます。

通常、B2B コラボレーション ユーザーは、招待 + 利用プロセスを通じて参加します。 しくみは次のとおりです。

1. WoodGrove の John Doe は、gmail アドレス (gsamoogle@gmail.com) を使用して Sam Oogle を追加したいと考えています。

2. John は WoodGrove ポータル (portal.azure.com) またはアクセス パネル (myapps.microsoft.com) に移動してサインインし、WoodGrove ディレクトリ、またはグループあるいはアプリケーションにこのユーザーを追加します。

3. John は Sam に送信するカスタムの招待メールを指定します。

4. 作業が完了するとすぐに、WoodGrove AD に次のユーザーが作成されます (スクリーンショットは、portal.azure.com の管理 UX を示しています)。

  ![ユーザーが追加される](media/active-directory-b2b-what-is-azure-ad-b2b/user-is-added.png)

5. このユーザーの追加が完了するとすぐに、Azure AD によって Sam に招待メールが送信されます。

  ![Sam に送信された招待メール](media/active-directory-b2b-what-is-azure-ad-b2b/invitation-mail-sent-to-sam.png)

6. Sam が **[開始]** を選択し、サインインします。 この時点で、Azure AD によって、Sam のトークンから取得した情報でディレクトリ内のユーザー オブジェクトが更新されます (スクリーンショットは、[Azure Portal](https://portal.azure.com) の管理 UX を示しています)。

  ![ユーザー プロファイルが設定される](media/active-directory-b2b-what-is-azure-ad-b2b/user-profile-is-populated.png)

7. Sam の招待が利用されたので、Sam は WoodGrove のリソースにアクセスできるようになります。また、管理者は、ディレクトリ内の他のユーザーと同様に、Sam を管理できます (スクリーンショットは、[Azure Portal](https://portal.azure.com) の管理 UX を示しています)。

  ![Sam は Azure AD のユーザーになっている](media/active-directory-b2b-what-is-azure-ad-b2b/sam-now-user-in-azure-ad.png)

## <a name="public-preview-features"></a>パブリック プレビュー機能
パブリック プレビューに含まれた B2B コラボレーション機能を使用しているお客様から多数の優れたフィードバックが寄せられました。 Microsoft では、これらのフィードバックを基に このパブリック プレビュー更新版で行ったすべての機能強化のパッケージ化を進めています。 B2B コラボレーション パブリック プレビュー更新版の主な機能は次のとおりです。

1. 管理 UX における B2B エクスペリエンスの強化。
  - https://portal.azure.com への移行。
  - 管理者は、B2B ユーザーをディレクトリ、または任意のグループあるいはアプリケーションに招待できます。

2. インフォメーション ワーカーを対象とした、アクセス パネル (https://myapps.microsoft.com) の B2B コラボレーション セルフサービス招待機能。 インフォメーション ワーカーは、各自が管理するセルフサービス グループまたはアプリケーションに B2B コラボレーション ユーザーを招待できます。

3. 任意の電子メール アドレスを使用するユーザーを招待できるようになりました。 ユーザーは、Office365 またはオンプレミスの Microsoft Exchange の電子メール アドレス、outlook.com の電子メール アドレス、ソーシャル電子メール アドレス (Gmail、Yahoo など) のどれを使用しているかに関係なく、Azure AD アカウントまたは Microsoft アカウントをインラインで簡易作成することで、招待された組織にシームレスにアクセスできるようになりました。

4. テナントによってブランド化されたプロフェッショナルな外観の招待メールがもたらすメリットを享受できます。

5. 招待 API を使用して参加プロセスをカスタマイズする広範な機能。

6. 招待元の組織での B2B コラボレーション ユーザーの多要素認証。

7. 管理者以外のユーザーに招待を委任する機能。

8. PowerShell による B2B コラボレーションのサポート。

9. 監査およびレポート機能。

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-admin-add-users.md)
* [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-how-it-works.md)
* [B2B コラボレーションの招待メールの要素](active-directory-b2b-invitation-email.md)
* [B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B コラボレーションのライセンス](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B コラボレーションのトラブルシューティング](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B コラボレーションの API とカスタマイズ](active-directory-b2b-api.md)
* [B2B コラボレーション ユーザーの多要素認証](active-directory-b2b-mfa-instructions.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO1-->



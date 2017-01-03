---
title: "Azure Active Directory を使用して外部 ID を管理するための機能の比較 | Microsoft Docs"
description: "外部 ID の認証と承認のサポートに関して、Azure Active Directory B2B コラボレーション、B2C、マルチテナント アプリを比較します"
services: active-directory
documentationcenter: 
author: arvindsuthar
manager: cliffdi
editor: 
tags: 
ms.assetid: 749b2e3a-2b8e-45ba-9f2a-6ca56eb1053b
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/24/2016
ms.author: asuthar
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: a9a7c0a9adea7fe4fccaa4ec9df0b077cd1e7382


---
# <a name="comparing-capabilities-for-managing-external-identities-using-azure-active-directory"></a>Azure Active Directory を使用して外部 ID を管理するための機能の比較
SaaS アプリへのモバイル ユーザーのアクセスを管理するだけでなく、ビジネス パートナーとリソースを共有したり、企業や消費者にアプリケーションを提供したりする場合にも、Azure Active Directory (Azure AD) が役に立ちます。

## <a name="developing-applications-for-businesses"></a>企業向けのアプリケーションを開発する
給与支払いサービスなどのサービスやアプリケーションを企業に提供する場合、 Azure AD が備える ID プラットフォームを使用すると、Office 365 や他のエンタープライズ サービスのデプロイの一部として Azure AD を既に構成している多くの組織とシームレスに統合するアプリケーションを構築できます。

**例:** ある医薬品会社は、医療用品や情報システムを医療業界に提供しています。 この会社は医療機関に分析アプリケーションを提供する必要があり、顧客が自分で ID を管理できるようにしたいと考えました。 この会社は、業務管理アプリケーションの ID プラットフォームとして Azure AD を選択し、必要に応じてサインアップ時に顧客に Azure AD の ID を提供しています。 詳細については、「 [Azure Active Directory 開発者ガイド](active-directory-developers-guide.md)」をご覧ください。

## <a name="enabling-business-partner-access-to-your-corporate-resources"></a>ビジネス パートナーが会社のリソースにアクセスできるようにする
ビジネス パートナーや他の社外ユーザーに、SharePoint サイトや ERP システムなどの社内リソースにアクセスできるようにすることが必要な場合があります。 Azure AD を使用すると、外部ユーザー (Azure AD 内にいてもいなくても) に会社のアプリケーションへのシングル サインオン アクセスを許可できます。 ユーザーがパートナー組織を辞めたときはアクセスできなくなるのでセキュリティが向上し、組織内のアクセス ポリシーを制御できます。 また、外部パートナーのディレクトリを管理したり、パートナーのフェデレーション関係ごとに管理したりする必要がないため、管理が簡素化されます。

**例:** ある映像関連企業は、小売店に写真画像化サービスを提供し、印刷キオスクの最大規模の小売店ネットワークを運営しています。 この会社は、Azure AD を導入して、何千店ものリテール ビジネス パートナーが専用の資格情報を使用して最新のパートナー マーケティング資料をダウンロードしたり、会社のサプライヤー エクストラネットから写真処理用品を再注文したりできるようにしました。 詳細については、「 [Azure AD B2B コラボレーション](active-directory-b2b-what-is-azure-ad-b2b.md)」を参照してください。

## <a name="developing-applications-for-consumers"></a>消費者向けのアプリケーションを開発する
小売りなどのオンライン アプリケーションを何百万人もの消費者に対して安全かつ安価に公開したいことがあります。 Azure AD のプラットフォームを利用すると、ソーシャル ログインやユーザー名/パスワードのサインイン、ブランド化されたセルフサービスのサインアップ、セルフサービスのパスワード リセットなどを、アプリケーションの消費者に提供できます。 これにより、消費者の利便性を高めながら、開発者の負担を軽減できます。

**例:** \#世界最大のスポーツ フランチャイズ企業は、4 億 5,000 万人のファンと直接交流する必要がありました。 そのため、ユーザーの認証とプロファイルの保存に Azure AD を使用するモバイル アプリを開発しました。 ファンは、Facebook などのソーシャル アカウントを使用して簡単に登録してサインインすることも、従来のユーザー名とパスワードを使用して iOS、Android、Windows Phone の間でシームレスなエクスペリエンスを利用することもできます。 確立された Azure AD プラットフォーム上に構築することにより、カスタム コードは大幅に減り、ブランドをカスタマイズでき、セキュリティ、データ漏えい、拡張性に関する苦労が軽減されます。 詳細については、「 [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)」を参照してください。

## <a name="comparison-of-azure-ad-capabilities"></a>Azure AD の機能の比較
以上のような各シナリオには、Azure AD の機能で対応できます。 次の表を見ると、実際の状況に最適な機能がよくわかります。

| **検討すべき製品...** | [Azure AD マルチ テナント SaaS アプリ](active-directory-developers-guide.md) | [Azure AD B2B コラボレーション](active-directory-b2b-what-is-azure-ad-b2b.md) | [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/) |
| --- | --- | --- | --- |
| **提供する必要があるもの...** |企業向けサービス |パートナー向けアプリ |消費者向けサービス |
| **似た事例...** |医薬品会社 |映像関連企業 |スポーツ フランチャイズ |
| **アプリをデプロイする目的...** |業務管理 |サプライヤー エクストラネット |サッカー ファン |
| **対象...** |医師のオフィス |承認済みのビジネス パートナー |電子メールが使えるすべてのユーザー |
| **アクセス方法...** |顧客の管理者の同意 |自社の管理者が招待 |消費者自身がサインアップ |




<!--HONumber=Dec16_HO5-->



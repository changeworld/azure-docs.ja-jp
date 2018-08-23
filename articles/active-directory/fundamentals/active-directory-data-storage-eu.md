---
title: Azure AD でヨーロッパのお客様の識別データが保存されている場所 | Microsoft Docs
description: Microsoft Azure Active Directory でヨーロッパのお客様の ID 関連データが保存されている場所について説明します。
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/17/2018
ms.custom: it-pro
ms.openlocfilehash: 0baa499f56fa6c074ac1c0f604e74f9e7adb5502
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42023011"
---
# <a name="where-does-microsoft-azure-active-directory-azure-ad-store-identity-data-for-european-customers"></a>Microsoft Azure Active Directory (Azure AD) でヨーロッパのお客様の識別データが保存されている場所
Azure AD は組織のリソースの保護を目的としたソリューションであり、ユーザー ID の管理とインテリジェンスに基づくアクセス ポリシーの作成に役立ちます。 ID データは、組織がサービスにサブスクライブしたときに入力した住所に基づいた場所に保存されます。 たとえば、Office 365 や Azure にサブスクライブしたときです。 ID データの保存場所に関する詳細情報については、Microsoft セキュリティ センターの[データの保存場所](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)に関するセクションを参照してください。

ほとんどの Azure AD 関連のヨーロッパの ID データはヨーロッパのデータセンター内にとどまりますが、通常は米国のデータセンターに保存される 5 つのユーザー関連の属性があります。 具体的には、GivenName、Surname、userPrincipalName、Domain、および PasswordHash 属性です。 PasswordHash 属性は例外になることがあり、Azure AD との同期から PasswordHash 値を停止するオンプレミスのフェデレーション認証方法を使用している場合、米国には保存されません。 さらに、通常の Azure AD 操作に必要な、運用上のサービス固有のデータがいくつかあります。このデータは米国に保存され、個人データは含まれていません。

## <a name="data-stored-outside-of-european-datacenters-for-european-customers"></a>ヨーロッパのデータセンター以外に保存されるヨーロッパのお客様のデータ

ヨーロッパに拠点がある組織の Azure AD に関連するヨーロッパの ID データは、ヨーロッパのデータセンター内にとどまります。 ヨーロッパのデータセンターに保存されない Azure AD データには、以下のようなデータがあります。

- **ID 関連の属性**

    次の ID 関連の属性は、米国にレプリケートされます。

    - GivenName
    - Surname
    - userPrincipalName
    - ドメイン
    - PasswordHash
    - SourceAnchor
    - AccountEnabled
    - PasswordPolicies
    - StrongAuthenticationRequirement
    - ApplicationPassword
    - PUID

- **Microsoft Azure Multi-Factor Authentication (MFA) と Azure AD のセルフサービスによるパスワードのリセット (SSPR)**
    
    MFA は、ヨーロッパのデータセンターにすべてのユーザー データを保存しています。 ただし、一部の MFA サービス固有のデータは米国に保存されます。
    
    - MFA または SSPR を使用している場合、2 要素認証とそれに関連する個人データは米国に保存される場合があります。
        - 電話または SMS を使用したすべての 2 要素認証は、米国の通信事業者によって実行される可能性があります。
        - Microsoft Authenticator アプリを使用するプッシュ通知の場合、ヨーロッパ以外の国の製造元の通知サービス (Apple または Google) からの通知が必要です。
        - OATH コードは常に米国で検証されます。 
    - 一部の MFA および SSPR ログは、認証の種類に関係なく、米国に 30 日間保存されます。

- **Microsoft Azure Active Directory B2C (Azure AD B2C)**

    Azure AD B2C は、ヨーロッパのデータセンターにすべてのユーザー データを保存しています。 ただし、操作ログ (個人データを削除したもの) は、ユーザーがサービスにアクセスしている場所にとどまります。 たとえば、B2C ユーザーが米国内のサービスにアクセスすると、操作ログは米国内にとどまります。さらに、個人データを含まないすべてのポリシー構成データは米国内にのみ保存されます。ポリシー構成の詳細については、「[Azure Active Directory B2C: 組み込みのポリシー](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies)」を参照してください。

- **Microsoft Azure Active Directory B2B (Azure AD B2B)** 
    
    Azure AD B2B は、ヨーロッパのデータセンターにすべてのユーザー データを保存しています。 ただし、B2B は個人以外のメタデータを米国のデータセンター内のテーブルに保存しています。 このテーブルには、redeemUrl、invitationTicket、リソース テナント ID、InviteRedirectUrl、InviterAppId などのフィールドがあります。

- **Microsoft Azure Active Directory Domain Services (Azure AD DS)**

    Azure AD DS は、ユーザーが選択した Azure Virtual Network と同じ場所にユーザー データを保存します。 そのため、ネットワークがヨーロッパ以外にある場合、データはレプリケートされ、ヨーロッパ以外の場所に保存されます。

- **Azure AD と統合されているサービスとアプリ**

    Azure AD と統合されているサービスとアプリは ID データにアクセスできます。 各サービスとアプリを評価して、特定のサービスとアプリで ID データがどのように処理されるか、また企業のデータ ストレージ要件を満たしているかどうかを判断してください。

    Microsoft サービスのデータ保存場所の詳細については、Microsoft セキュリティ センターの[データの保存場所](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)に関するセクションを参照してください。

## <a name="next-steps"></a>次の手順
上記の機能の詳細については、以下の記事を参照してください。
- [Azure Active Directory の概要](get-started-azure-ad.md)
- [Multi-Factor Authentication とは](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
- [Azure AD のセルフ サービスによるパスワードのリセット](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)
- [Azure Active Directory B2C とは](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Azure AD B2B コラボレーションとは](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)

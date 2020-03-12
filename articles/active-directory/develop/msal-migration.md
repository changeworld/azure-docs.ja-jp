---
title: Microsoft Authentication Library (MSAL) への移行
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library (MSAL) と Azure AD Authentication Library (ADAL) の違いと、MSAL への移行方法について学習します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/27/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3af18eb09fd9906a0caaebda0b786795400467f3
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165397"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL) へのアプリケーションの移行

Azure AD エンティティを認証し、Azure AD からのトークンを要求する場合、Microsoft Authentication Library (MSAL) と Azure AD Authentication Library (ADAL) の両方が使用されます。 これまで、ほとんどの開発者は、開発者プラットフォーム用の Azure AD (v1.0) で、Azure AD Authentication Library (ADAL) を使用してトークンを要求することで、Azure AD ID (職場と学校のアカウント) を認証していました。 MSAL を使用すると、次のようになります。

- Microsoft ID プラットフォーム エンドポイントを使用するため、より広範な Microsoft ID (Azure AD の ID と Microsoft アカウント、および Azure AD B2C 経由のソーシャル アカウントとローカル アカウント) を認証できます。
- ユーザーは、最高のシングル サインオン エクスペリエンスを得られます。
- アプリケーションでは、増分同意を有効にできるほか、 Conditional Access のサポートがより簡単になります。
- イノベーションを活用できます。

**MSAL は、Microsoft ID プラットフォームと併せて使用する場合にお勧めの認証ライブラリです**。 ADAL には新しい機能は実装されません。 この取り組みは、MSAL の改良に重点を置いています。

次のアーティクルでは、MSAL ライブラリと ADAL ライブラリの違いについて説明し、MSAL への移行を支援します。
- [MSAL.NET への移行](msal-net-migration.md)
- [MSAL.js への移行](msal-compare-msal-js-and-adal-js.md)
- [MSAL.Android への移行](migrate-android-adal-msal.md)
- [MSAL.iOS / macOS への移行](migrate-objc-adal-msal.md)
- [MSAL Python への移行](migrate-python-adal-msal.md)
- [MSAL for Java への移行](migrate-adal-msal-java.md)
- [ブローカーを使用する Xamarin アプリの MSAL.NET への移行](msal-net-migration-ios-broker.md)

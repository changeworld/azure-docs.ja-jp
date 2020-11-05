---
title: Azure Active Directory におけるアプリに対する OIDC ベースのシングル サインオン (SSO) について理解する
description: Azure Active Directory におけるアプリに対する OIDC ベースのシングル サインオン (SSO) について理解する。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: kenwith
ms.reviewer: arajpathak7
ms.custom: contperfq2
ms.openlocfilehash: 5fc0156b9b832f44fd38292ef2b9a25e39401610
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348663"
---
# <a name="understand-oidc-based-single-sign-on"></a>OIDC ベースのシングル サインオンについて理解する
アプリケーション管理の[クイックスタート シリーズ](view-applications-portal.md)では、アプリケーションの ID プロバイダー (IdP) として Azure AD を使用する方法について学習しました。 この記事では、OpenID Connect 標準を使用してシングル サインオンを実装するアプリについて詳しく説明します。 

## <a name="before-you-begin"></a>開始する前に
Azure Active Directory テナントにアプリを追加するプロセスは、アプリケーションで実装されているシングル サインオンの種類によって異なります。 ID 管理に Azure AD を使用できるアプリで使用できるシングル サインオン オプションの詳細については、[シングル サインオン オプション](sso-options.md)に関するページを参照してください。 この記事では、OIDC ベースのアプリについて説明します。


## <a name="basic-oidc-configuration"></a>基本的な OIDC 構成
[クイックスタート シリーズ](add-application-portal-setup-oidc-sso.md)には、シングル サインオンの構成に関する記事があります。 そこでは、OIDC ベースのアプリを Azure テナントに追加する方法について説明しています。

OIDC 標準をシングル サインオンに使用するアプリを追加する利点は、構成が最小限になることです。 次の短いビデオは、OIDC ベースのアプリをテナントに追加する方法を示しています。

Azure Active Directory で OIDC ベースのアプリを追加する

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

## <a name="next-steps"></a>次のステップ

- [アプリケーション管理のクイックスタート シリーズ](add-application-portal-setup-oidc-sso.md)
- [シングル サインオンのオプション](sso-options.md)
- [方法: すべての Azure Active Directory ユーザーがマルチテナント アプリケーション パターンを使用してサインインする](../develop/howto-convert-app-to-be-multi-tenant.md)

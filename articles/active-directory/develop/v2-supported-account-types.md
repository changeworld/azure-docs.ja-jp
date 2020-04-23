---
title: サポートされているアカウントの種類 - Microsoft ID プラットフォーム | Azure
description: アプリケーションの対象ユーザーとサポートされているアカウントの種類に関する概念的なドキュメント
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: b3b0114bb5d545755fe59c49605d6def341d2275
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535776"
---
# <a name="supported-account-types"></a>サポートされているアカウントの種類

この記事では、アプリケーションでサポートされているアカウントの種類 (対象ユーザーと呼ばれることもある) について説明します。

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Microsoft ID プラットフォームのアプリケーションでサポートされているアカウントの種類

Microsoft Azure パブリック クラウドでは、ほとんどの種類のアプリで、どのようなオーディエンスのユーザーでもサインインさせることができます。

- 基幹業務 (LOB) アプリケーションを記述している場合は、お客様の組織内のユーザーをサインインさせられます。 このようなアプリケーションは、**シングル テナント**と呼ばれることもあります。
- お客様が ISV である場合は、次の場合にユーザーをサインインさせるアプリケーションを記述できます。

  - 任意の組織に。 このようなアプリケーションは、**マルチテナント** Web アプリケーションと呼ばれます。 職場または学校のアカウンでユーザーをサインインさせると解釈できる場合もあります。
  - 職場、学校または個人用の Microsoft アカウントを使用して。
  - 個人用の Microsoft アカウントのみを使用して。
    > [!NOTE]
    > 現在、Microsoft ID プラットフォームは、**職場、学校または Microsoft 個人アカウント**用にアプリを登録することによってのみ、個人用の Microsoft アカウントをサポートしており、`https://login.microsoftonline.com/consumers` などのアプリケーションを構築するときに Azure AD の権限を指定することによって、アプリケーションのコードでのサインインを制限しています。

- ビジネスから消費者へのアプリケーションを作成している場合は、Azure AD B2C を使用して、ソーシャル ID でユーザーをサインインさせることもできます。

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>特定の認証フローで、すべてのアカウントの種類がサポートされない

一部のアカウントの種類は、特定の認証フローでは使用できません。 たとえば、デスクトップでは、UWP アプリケーションやデーモン アプリケーションが該当します。

- デーモン アプリケーションは、Azure Active Directory の組織でのみ使用できます。 Microsoft 個人アカウントを操作するためにデーモン アプリケーションを使用しても意味がありません (管理者の同意は付与されません)。
- 統合 Windows 認証フローは、(お客様の組織または任意の組織の) 職場または学校のアカウントでのみ使用できます。 実際、統合 Windows 認証はドメイン アカウントで動作し、マシンをドメインに参加させるか Azure AD に参加させる必要があります。 このフローは、個人の Microsoft アカウントには意味がありません。
- [リソース所有者のパスワード付与](./v2-oauth-ropc.md) (ユーザー名/パスワード) は、個人の Microsoft アカウントでは使用できません。 実際、個人の Microsoft アカウントでは、ユーザーがサインイン セッションごとに個人のリソースへのアクセスに同意する必要があります。 このため、この動作は非対話型フローとは互換性がありません。
- デバイス コード フローは、個人の Microsoft アカウントではまだ機能しません。

## <a name="supported-account-types-in-national-clouds"></a>各国のクラウドでサポートされているアカウントの種類

 アプリは、[各国のクラウド](authentication-national-cloud.md)にユーザーをサインさせることもできます。 ただし、Microsoft の個人アカウントは、これらのクラウドで (これらのクラウドの定義によって) サポートされていません。 そのため、これらのクラウドでサポートされるアカウントの種類は、お客様の組織 (シングル テナント) または任意の組織 (マルチ テナント アプリケーション) に限定されます。

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory のテナント](./single-and-multi-tenant-apps.md)の詳細を確認する
- [各国のクラウド](./authentication-national-cloud.md)の詳細を確認する

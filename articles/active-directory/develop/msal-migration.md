---
title: Microsoft Authentication Library (MSAL) への移行
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library (MSAL) と Azure AD Authentication Library (ADAL) の違いと、MSAL への移行方法について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/22/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 06fd2f7fcbe5c43fa22daad7dbdd09105b2d9eca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741738"
---
# <a name="migrate-applications-to-the-microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL) へのアプリケーションの移行

お使いのアプリケーションで認証と承認の機能に Azure Active Directory 認証ライブラリ (ADAL) を使用している場合は、[Microsoft Authentication Library (MSAL)](msal-overview.md#languages-and-frameworks) に移行する時期です。

- セキュリティ修正プログラムを含め、ADAL についてのすべての Microsoft のサポートと開発は、2022 年 6 月 30 日に終了します。
- 2020 年 6 月 30 日以降、ADAL に新しい機能は追加されていません。

> [!WARNING]
> ADAL のサポートが終了する 2022 年 6 月 30 日までに MSAL に移行しないと、アプリのセキュリティが危険にさらされます。 ADAL を使用している既存のアプリはサポート終了日後も引き続き機能しますが、Microsoft は ADAL のセキュリティ修正プログラムをリリースしなくなります。

## <a name="why-switch-to-msal"></a>MSAL に切り替える理由

MSAL には、次の機能を含め、ADAL と比較して複数の利点があります。 

|機能|MSAL|ADAL|
|---------|---------|---------|
|**Security**|||
|2022 年 6 月 30 日以降のセキュリティ修正プログラム|![2022 年 6 月 30 日以降のセキュリティ修正プログラム: MSAL ではこの機能が提供されます][y]|![2022 年 6 月 30 日以降のセキュリティ修正プログラム: ADAL ではこの機能が提供されません][n]|
| [継続的アクセス評価 (CAE)](app-resilience-continuous-access-evaluation.md) をサポートする Microsoft Graph およびその他の API のポリシーまたは重要なイベントに基づいてトークンを事前に更新および取り消す。|![継続的アクセス評価 (CAE) をサポートする Microsoft Graph およびその他の API のポリシーまたは重要なイベントに基づいてトークンを事前に更新および取り消す: MSAL ではこの機能が提供されます][y]|![継続的アクセス評価 (CAE) をサポートする Microsoft Graph およびその他の API のポリシーまたは重要なイベントに基づいてトークンを事前に更新および取り消す: ADAL ではこの機能が提供されません][n]|
| OAuth v2.0 および OpenID Connect (OIDC) の標準への準拠 |![OAuth v2.0 および OpenID Connect (OIDC) の標準への準拠: MSAL ではこの機能が提供されます][y]|![OAuth v2.0 および OpenID Connect (OIDC) の標準への準拠: ADAL ではこの機能が提供されません][n]|
|**ユーザー アカウントとエクスペリエンス**|||
|Azure Active Directory (Azure AD) アカウント|![Azure Active Directory (Azure AD) アカウント: MSAL ではこの機能が提供されます][y]|![Azure Active Directory (Azure AD) アカウント: ADAL ではこの機能が提供されます][y]|
| Microsoft アカウント (MSA) |![Microsoft アカウント (MSA): MSAL ではこの機能が提供されます][y]|![Microsoft アカウント (MSA): ADAL ではこの機能が提供されません][n]|
| Azure AD B2C アカウント |![Azure AD B2Cアカウント: MSAL ではこの機能が提供されます][y]|![Azure AD B2Cアカウント: ADAL ではこの機能が提供されません][n]|
| 最適なシングル サインオン エクスペリエンス |![最適なシングル サインオン エクスペリエンス: MSAL ではこの機能が提供されます][y]|![最適なシングル サインオン エクスペリエンス: ADAL ではこの機能が提供されません][n]|
|**回復力**|||
| トークンの事前更新 |![トークンの事前更新: MSAL ではこの機能が提供されます][y]|![トークンの事前更新: ADAL ではこの機能が提供されません][n]|
| Throttling |![調整: MSAL ではこの機能が提供されます][y]|![調整: ADAL ではこの機能が提供されません][n]|

## <a name="ad-fs-support-in-msalnet"></a>MSAL.NET での AD FS のサポート

MSAL.NET、MSAL Java、MSAL Python を使用して、Active Directory フェデレーション サービス (AD FS) (AD FS) 2019 以降からトークンを取得できます。 AD FS 2016 を含め、以前のバージョンの AD FS は MSAL ではサポートされていません。

AD FS を引き続き使用する必要がある場合は、ADAL から MSAL にアプリケーションを更新する前に、AD FS 2019 以降にアップグレードする必要があります。

## <a name="how-to-migrate-to-msal"></a>MSAL への移行方法

移行を開始する前に、認証に ADAL を使用しているアプリを特定する必要があります。 こちらの記事の手順に従って、Azure portal を使用して一覧を取得してください。
- [方法: テナントで ADAL を使用しているアプリの一覧を取得する](howto-get-list-of-all-active-directory-auth-library-apps.md)

ADAL を使用しているアプリを特定した後、下に示すアプリケーションの種類に応じて MSAL に移行します。

[!INCLUDE [application type](includes/adal-msal-migration.md)]

## <a name="migration-help"></a>移行のヘルプ

ADAL から MSAL へのアプリの移行について質問がある場合、こちらにいくつかのオプションを示しています。

- [Microsoft Q&A](/answers/topics/azure-ad-adal-deprecation.html) で、タグ `[azure-ad-adal-deprecation]` を使用して質問を投稿する。
- ライブラリの GitHub リポジトリでイシューを開く。 各ライブラリのリポジトリへのリンクについては、MSAL の概要に関する記事の「[言語とフレームワーク](msal-overview.md#languages-and-frameworks)」セクションを参照してください。

アプリケーションの開発で独立系ソフトウェア ベンダー (ISV) と提携している場合は、MSAL への移行手順を把握するために、先方に直接連絡することをお勧めします。

## <a name="next-steps"></a>次のステップ

MSAL の詳細、たとえば使用情報や、さまざまなプログラミング言語とアプリケーションの種類で使用できるライブラリについては、こちらを参照してください。

- [MSAL でトークンを取得し、キャッシュする](msal-acquire-cache-tokens.md)
- [アプリケーション構成オプション](msal-client-application-configuration.md)
- [MSAL 認証ライブラリ](reference-v2-libraries.md)

<!--
 ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->
[y]: media/common/yes.png
[n]: media/common/no.png
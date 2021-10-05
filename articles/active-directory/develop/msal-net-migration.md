---
title: MSAL.NET と Microsoft.Identity.Web に移行する
titleSuffix: Microsoft identity platform
description: Azure AD Authentication Library for .NET (ADAL.NET) から Microsoft Authentication Library for .NET (MSAL.NET) または Microsoft.Identity.Web に移行する理由と方法を説明します
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/08/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 5a3dd6265c9fbefb85cf72b80473538983c682b7
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214705"
---
# <a name="migrating-applications-to-msalnet-or-microsoftidentityweb"></a>MSAL.NET または Microsoft.Identity.Web にアプリケーションを移行する

## <a name="why-migrate-to-msalnet-or-microsoftidentityweb"></a>MSAL.NET または Microsoft.Identity.Web に移行する理由

Azure AD エンティティを認証し、Azure AD からのトークンを要求する場合、Microsoft Authentication Library for .NET (MSAL.NET) と Azure AD Authentication Library for .NET (ADAL.NET) の両方が使用されます。 これまで、ほとんどの開発者は、Azure AD Authentication Library (ADAL) を使用して、Azure AD for developers (v1.0) プラットフォームにトークンを要求していました。 これらのトークンは、Azure AD ID (職場と学校のアカウント) を認証するのに使用します。 

MSAL には ADAL よりも有利な点がありません。 その利点の一部を下に挙げます。

- 広範な Microsoft ID を認証できます。職場または学校アカウント、私的な Microsoft アカウント、Azure AD B2C のソーシャル アカウントとローカルアカウントを認証可能です。
- ユーザーが、使い勝手の優れたシングルサインオン機能を利用できます。
- アプリケーションで、コンテンツの差分更新、条件付きアクセスを使用できます。
- セキュリティとレジリエンスに関する継続的なイノベーションの恩恵を受けられます。
- アプリケーションで、レジリエンスとセキュリティに関するベスト プラクティスを実装できます。

**MSAL.NET または Microsoft.Identity.Web は、Microsoft ID プラットフォームで使用する場合に推奨される認証ライブラリとなりました**。 ADAL.NET に新しい機能は実装されません。 MSAL.NET の改良に開発を集中しています。 詳しくは、次の発表をご覧ください: [Update your applications to use Microsoft Authentication Library and Microsoft Graph API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)

## <a name="should-you-migrate-to-msalnet-or-to-microsoftidentityweb"></a>MSAL.NET または Microsoft.Identity.Web に移行する場合

MSAL.NET と ADAL.NET を詳細に比較する前に、MSAL.NET を使用するか、または [Microsoft.Identity.Web](microsoft-identity-web.md) のような高レベルの抽象化を使用するかを確認することをお勧めします。

下図の決定の流れの詳細は、[MSAL.NET と Microsoft.Ideniy.Web のどちらを使用するか決める方法](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-or-Microsoft.Identity.Web)に関する記事をご覧ください。

![“ADAL.NET から移行するときに、MSAL.NET、Microsoft.Identity.Web またはその両方のどれを採用するべきかを決める方法を説明するブロック図”](media/msal-net-migration/decision-diagram.png)

<!-- 1P
## Examples of 1P Migrations

[See examples](https://identitydivision.visualstudio.com/DevEx/_wiki/wikis/DevEx.wiki/20413/1P-ADAL.NET-to-MSAL.NET-migration-examples) of other 1P teams who have already, or are currently, migrating from ADAL to one of the MSAL+ solutions above. See their code, and in some cases read about their migration story.
 -->
 
## <a name="next-steps"></a>次のステップ

- [パブリック クライアントと機密性を扱うクライアント アプリケーション](msal-client-applications.md)について学ぶ。
- [ASP.NET MVC または .NET classic を利用して開発した、機密性を扱うクライアント アプリケーションを、ADAL.NET から MSAL.NET に移行する方法](msal-net-migration-confidential-client.md)を学ぶ。
- [.NET または .NET classic を利用して開発したパブリック クライアント アプリケーションを、ADAL.NET から MSAL.NET に移行する方法](msal-net-migration-public-client.md)を学ぶ。
- [ADAL.NET と MSAL.NET アプリの違い](msal-net-differences-adal-net.md)について詳しく知る。
- ASP.NET Core を利用して開発した、機密性を扱うクライアント アプリケーションを、ADAL.NET から Microsoft.Identity.Web に移行する方法を学ぶ。
  -  [Web アプリ](https://github.com/AzureAD/microsoft-identity-web/wiki/web-apps#migrating-from-previous-versions--adding-authentication)
  -  [Web API](https://github.com/AzureAD/microsoft-identity-web/wiki/web-apis)

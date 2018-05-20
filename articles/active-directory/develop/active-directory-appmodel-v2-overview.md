---
title: Azure Active Directory v2.0 エンドポイント | Microsoft Docs
description: Microsoft アカウントと Azure Active Directory の両方でサインインできるアプリケーションの構築を紹介します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: celested
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: 0fde3cb77389994352b62f06d370c709fa115749
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
---
# <a name="sign-in-microsoft-account-and-azure-active-directory-users-in-a-single-application"></a>単一のアプリケーションで Microsoft アカウントと Azure Active Directory にサインインする
アプリケーションの開発者はこれまで、個人用の Microsoft アカウントと Azure Active Directory の職場アカウントの両方をサポートするには、2 つの異なるシステムを 1 つに統合する必要がありました。 Azure Active Directory (Azure AD) v2.0 のエンドポイントには、このプロセスを簡略化する新しい認証 API のバージョンが導入されています。 Azure AD v2.0 エンドポイントでは、単一の統合を使用してどちらの種類のアカウントからでもサインインできます。 Azure AD v2.0 エンドポイントを使うアプリケーションは、どちらの種類のアカウントを使用していても、[Microsoft Graph API](https://graph.microsoft.io) の REST API を使用できます。

## <a name="getting-started"></a>使用の開始
次の一覧からお好みのプラットフォームを選択し、Microsoft のオープン ソース ライブラリとフレームワークを利用してアプリケーションを構築します。 また、認証ライブラリを使用せずに、OAuth 2.0 と OpenID Connect プロトコルを使用して直接プロトコル メッセージを送受信することもできます。
<br />

[!INCLUDE [Azure AD v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-azure-ad-v20-endpoint"></a>Azure AD v2.0 エンドポイントの詳細
Azure AD v2.0 エンドポイントで実現できることについては以下をご覧ください。

* [Azure AD v2.0 エンドポイントで構築できるアプリケーションの種類](active-directory-v2-flows.md)を知る。
* Azure AD v2.0 エンドポイントの [制限事項と制約事項](active-directory-v2-limitations.md)を知る。
* 次のビデオで Azure AD v2.0 エンドポイントの概要を見る。

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>その他のリソース
Azure AD v2.0 エンドポイント プラットフォームの詳細については以下をご覧ください。

* [Azure AD v2.0 プロトコル リファレンス](active-directory-v2-protocols.md)
* [Azure AD v2.0 トークン リファレンス](active-directory-v2-tokens.md)
* [Azure AD v2.0 認証ライブラリ リファレンス](active-directory-v2-libraries.md)
* [Azure AD v2.0 エンドポイントのスコープと同意](active-directory-v2-scopes.md)
* [Microsoft Graph API](https://graph.microsoft.io)

> [!NOTE]
> Azure Active Directory から職場および学校アカウントにサインインするだけでよい場合は、最初に [Azure AD 開発者向けガイド](active-directory-developers-guide.md)をご覧ください。 Azure AD v2.0 エンドポイントは、Microsoft の個人用アカウントにサインインすることが明らかに必要な開発者が使用するためのものです。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

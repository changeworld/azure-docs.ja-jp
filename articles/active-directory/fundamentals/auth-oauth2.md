---
title: Azure Active Directory を使用した OAuth 2.0 認証
description: この認証パターンの実現に関するアーキテクチャのガイダンス
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea22c4e5b363eaa3ecc2a736dfef714666310062
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113971"
---
# <a name="oauth-20-authentication-with-azure-active-directory"></a>Azure Active Directory を使用した OAuth 2.0 認証

OAuth 2.0 は、認可用の業界プロトコルです。 ユーザーは保護されたリソースへの制限付きアクセスを許可できます。 OAuth は、特にハイパーテキスト転送プロトコル (HTTP) を使用するように設計されているため、クライアントの役割がリソース所有者から分離されます。 クライアントでは、リソース所有者によって制御され、リソース サーバーでホストされるリソースへのアクセスが要求されます。 リソース サーバーでは、リソース所有者の承認を使用してアクセス トークンが発行されます。 クライアントはアクセス トークンを使用して、リソース サーバーでホストされる保護されたリソースにアクセスします。 

OAuth 2.0 は、OpenID Connect (OIDC) に直接関連しています。 OIDC は OAuth 2.0 上に構築された認証と認可の層であるため、OAuth 1.0 との下位互換性がありません。 Azure Active Directory (Azure AD) では、すべての OAuth 2.0 フローがサポートされています。 

## <a name="use-when"></a>次の場合に使用します。

リッチ クライアントおよび最新アプリのシナリオと RESTful Web API アクセスの場合。

![アーキテクチャの図](./media/authentication-patterns/oauth.png)

## <a name="components-of-system"></a>システムのコンポーネント

* **[ユーザー]** :Web アプリケーション (アプリ) からサービスを要求します。 一般に、ユーザーはデータを所有し、そのデータまたはリソースへのアクセスをクライアントに許可する権限を持つリソース所有者です。 

* **Web ブラウザー** :ユーザーが操作する Web ブラウザーは、OAuth クライアントです。 

* **Web アプリ** :Web アプリ (またはリソース サーバー) は、リソースまたはデータが存在する場所です。 承認サーバーを信頼し、OAuth クライアントを安全に認証、承認します。 

* **Azure AD** :Azure AD は、ID プロバイダー (IdP) とも呼ばれる承認サーバーです。 ユーザーの情報、アクセス、および信頼関係に関するすべての処理が安全に行われます。 リソースへのアクセスの許可および取り消しを行うトークンを発行する責任があります。

## <a name="implement-oauth-20-with-azure-ad"></a>Azure AD を使用して OAuth 2.0 を実装する

* [Azure AD とアプリケーションの統合](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [Microsoft ID プラットフォームにおける OAuth 2.0 プロトコルと OpenID Connect プロトコル](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) 

* [アプリケーションの種類と OAuth2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) 

 

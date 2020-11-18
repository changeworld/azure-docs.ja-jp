---
title: Azure Active Directory REST API - 認証
description: REST API を通して Azure Active Directory を使用して Azure App Configuration に対して認証する
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: fb3d00fb79c55e29d578f5e068e4ae025414a935
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423728"
---
# <a name="azure-active-directory-authentication"></a>Azure Active Directory 認証

HTTP 要求は、Azure Active Directory (Azure AD) から取得したトークンが使われる **ベアラー** 認証スキームを使用して認証できます。 これらの要求は、TLS で送信する必要があります。

## <a name="prerequisites"></a>前提条件

Azure AD トークンを要求するために使用されるプリンシパルは、適切な [App Configuration ロール](./rest-api-authorization-azure-ad.md)の 1 つに割り当てられている必要があります。

それぞれの要求には、認証に必要なすべての HTTP ヘッダーを指定します。 最低限必要なのは以下のとおりです。

|  要求ヘッダー | 説明  |
| --------------- | ------------ |
| **承認** | **ベアラー** スキームに必要な認証情報。 形式と詳細については下で説明します。 |

**例:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-active-directory-token-acquisition"></a>Azure Active Directory トークンの取得

Azure AD トークンを取得する前に、ユーザーを何として認証するのか、トークンの対象ユーザー、どの Azure AD エンドポイント (機関) を使用する必要があるかを特定する必要があります。

### <a name="audience"></a>対象ユーザー

Azure AD トークンは、適切な対象ユーザーを指定して要求する必要があります。 Azure App Configuration では、トークンの要求時には以下の対象ユーザーのいずれかを指定する必要があります。 対象ユーザーは、トークンの要求対象になっている "リソース" と呼ばれることもあります。

- {configurationStoreName}.azconfig.io
- *.azconfig.io

> [!IMPORTANT]
> 要求された対象ユーザーが {configurationStoreName}.azconfig.io の場合、それは、要求の送信に使用される "Host" 要求ヘッダー (大文字と小文字が区別されます) と完全に一致している必要があります。

### <a name="azure-ad-authority"></a>Azure AD 機関

Azure AD 機関は、Azure AD トークンを取得するために使用されるエンドポイントです。 形式は `https://login.microsoftonline.com/{tenantId}` です。 `{tenantId}` セグメントは、認証しようとしているユーザーまたはアプリケーションが属している、Azure Active Directory テナント ID を指します。

### <a name="authentication-libraries"></a>認証ライブラリ

Azure AD トークンを取得するプロセスを簡素化するため、Azure には、Azure Active Directory 認証ライブラリ (ADAL) と呼ばれるライブラリのセットが用意されています。 これらのライブラリは、複数言語用に構築されています。 ドキュメントは[ここ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)にあります。

## <a name="errors"></a>**エラー**

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**理由:** ベアラー スキームでの Authorization 要求ヘッダーが指定されていません。
**解決方法:** 有効な ```Authorization``` HTTP 要求ヘッダーを指定してください

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**理由:** Azure AD トークンが有効ではありません。
**解決方法:** Azure AD 機関から Azure AD トークンを取得し、適切な対象ユーザーが使用されていることを確認してください。

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**理由:** Azure AD トークンが有効ではありません。
**解決方法:** Azure AD 機関から Azure AD トークンを取得し、Azure AD テナントが、構成ストアが属しているサブスクリプションに関連付けられているものであることを確認してください。 このエラーは、プリンシパルが複数の Azure AD テナントに属している場合に表示される場合があります。

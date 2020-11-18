---
title: Azure App Configuration REST API - 認証
description: Azure App Configuration REST API を使用した認証についての参照ページ
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 21a43a005b78c8916d06e97ca9d2ba21d5a585a3
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423843"
---
# <a name="authentication"></a>認証

すべての HTTP 要求が認証される必要があります。 以下の認証スキームがサポートされています。

## <a name="hmac"></a>HMAC

[HMAC 認証](./rest-api-authentication-hmac.md)では、ランダムに生成されたシークレットを使用して要求ペイロードに署名します。 この認証方法を使用した要求がどのように認可されるかの詳細については、[HMAC 認証](./rest-api-authorization-hmac.md)に関するセクションを参照してください。

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD) 認証](/azure/active-directory/authentication/overview-authentication)では、Azure Active Directory から取得されるベアラー トークンを利用して要求を認証します。 この認証方法を使用した要求がどのように認可されるかの詳細については、[Azure AD 認証](./rest-api-authorization-azure-ad.md)に関するセクションを参照してください。

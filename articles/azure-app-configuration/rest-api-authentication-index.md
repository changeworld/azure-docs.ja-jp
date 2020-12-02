---
title: Azure App Configuration REST API - 認証
description: Azure App Configuration REST API を使用した認証についての参照ページ
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 56416009395ebf8270ad0fa8d141277424dd6d9a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183466"
---
# <a name="authentication"></a>認証

すべての HTTP 要求が認証される必要があります。 以下の認証スキームがサポートされています。

## <a name="hmac"></a>HMAC

[HMAC 認証](./rest-api-authentication-hmac.md)では、ランダムに生成されたシークレットを使用して要求ペイロードに署名します。 この認証方法を使用した要求がどのように認可されるかの詳細については、[HMAC 認証](./rest-api-authorization-hmac.md)に関するセクションを参照してください。

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD) 認証](../active-directory/authentication/overview-authentication.md)では、Azure Active Directory から取得されるベアラー トークンを利用して要求を認証します。 この認証方法を使用した要求がどのように認可されるかの詳細については、[Azure AD 認証](./rest-api-authorization-azure-ad.md)に関するセクションを参照してください。
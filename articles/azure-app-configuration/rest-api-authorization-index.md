---
title: Azure App Configuration REST API - 認可
description: Azure App Configuration REST API を使用した認可についての参照ページ
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 54f9cfab1f49837a3765c978de6deeb9e5e7d644
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423723"
---
# <a name="authorization"></a>承認

認可とは、要求を行うときに呼び出し元が持つアクセス許可を決定するために使用される手順のことです。 複数の認可モデルがあります。 要求に使用される認可モデルは、使用される[認証](./rest-api-authentication-index.md)方法によって異なります。 以下に認可モデルの一覧を示します。

## <a name="hmac"></a>HMAC

HMAC 認証に関連付けられている[認可モデル](./rest-api-authorization-hmac.md) モデルでは、アクセス許可が、読み取り専用または読み取り/書き込みに分割されています。 詳細については、[HMAC 認可](./rest-api-authorization-hmac.md)に関するページを参照してください。

## <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) 認証に関連付けられている[認可モデル](./rest-api-authorization-azure-ad.md)では、Azure RBAC を使用してアクセス許可を制御します。 詳細については、[Azure AD 認可](./rest-api-authorization-azure-ad.md)に関するページを参照してください。

---
title: Azure App Configuration REST API - Azure Active Directory 認証
description: REST API を使用した Azure App Configuration に対する認可に Azure Active Directory を使用する
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: bebab7c06062726f7b5c7868f984cadda3b4c98e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423724"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Azure Active Directory 認可 - REST API リファレンス

Azure Active Directory (Azure AD) 認証を使用する場合、認可は Azure ロール ベースのアクセス制御 (RBAC) によって処理されます。 Azure RBAC では、リソースへのアクセスを付与するために、ユーザーがロールに割り当てられている必要があります。 各ロールには、ロールに割り当てられたユーザーが実行できるようになる一連のアクションが含まれています。

## <a name="roles"></a>ロール

以下のロールは、Azure サブスクリプションで既定で使用できる組み込みのロールです。

- **Azure App Configuration データ所有者**:このロールでは、すべての操作に対するフルアクセスを提供します。
- **Azure App Configuration データ閲覧者**:このロールでは、読み取り操作が有効になります。

## <a name="actions"></a>Actions

ロールには、そのロールに割り当てられたユーザーが実行できるアクションの一覧が含まれています。 Azure App Configuration では、以下のアクションがサポートされています。

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`:このアクションでは、App Configuration のキー値リソース (/kv や /labels など) に対する読み取りアクセスを許可します。
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`:このアクションでは、App Configuration のキー値リソースに対する書き込みアクセスを許可します。
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`:このアクションでは、App Configuration のキー値リソースを削除できます。 リソースを削除すると、削除されたキー値が返されることに注意してください。

## <a name="errors"></a>エラー

```http
HTTP/1.1 403 Forbidden
```

**理由:** 要求元のプリンシパルには、要求された操作を実行するために必要なアクセス許可がありません。
**解決方法:** 要求された操作を実行するために必要なロールを、要求元のプリンシパルに割り当てます。

## <a name="managing-role-assignments"></a>ロールの割り当ての管理

ロールの割り当ての管理は、すべての Azure サービスにわたる標準である [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) の手順を使用して行います。 これは、Azure CLI、PowerShell、Azure portal などを通して行うことができます。 ロールの割り当てを作成する方法に関する公式ドキュメントは、[ここ](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)にあります。

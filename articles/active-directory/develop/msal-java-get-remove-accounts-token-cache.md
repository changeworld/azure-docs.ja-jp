---
title: トークン キャッシュのアカウントを取得および削除する (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for Java を使用してトークン キャッシュのアカウントを表示および削除する方法を確認します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: marsma
ms.reviewer: navyasri.canumalla
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 8ea51bfd356e7479f421a6db804ec94bf656284a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612531"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>MSAL for Java を使用したトークン キャッシュのアカウントの取得と削除

MSAL for Java は、既定でインメモリ トークン キャッシュを備えています。 アプリケーション インスタンスの期間、インメモリ トークン キャッシュは継続します。

## <a name="see-which-accounts-are-in-the-cache"></a>キャッシュ内に存在するアカウントを確認する

次の例に示すように、`PublicClientApplication.getAccounts()` を呼び出して、キャッシュ内に存在するアカウントを確認できます。

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>キャッシュからアカウントを削除する

キャッシュからアカウントを削除するには、次の例に示すように、削除する必要があるアカウントを見つけて、`PublicClientApplication.removeAccount()` を呼び出します。

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>詳細情報

MSAL for Java を使用している場合、[MSAL for Java のカスタム トークン キャッシュのシリアル化](msal-java-token-cache-serialization.md)について学習してください。

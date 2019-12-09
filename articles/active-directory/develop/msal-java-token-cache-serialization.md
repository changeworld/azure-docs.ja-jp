---
title: MSAL for Java でのカスタム トークン キャッシュのシリアル化
titleSuffix: Microsoft identity platform
description: MSAL for Java 用のトークン キャッシュをシリアル化する方法について説明します
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7323a47095c58958503cecf16189ed875fac3b4d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452607"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>MSAL for Java でのカスタム トークン キャッシュのシリアル化

アプリケーション間のトークン キャッシュを永続的にするには、シリアル化をカスタマイズする必要があります。 トークン キャッシュのシリアル化に関係のある Java のクラスとインターフェイスは次のとおりです。

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): セキュリティ トークン キャッシュを表すインターフェイス。
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): アクセスの前後にコードを実行する操作を表すインターフェイス。 キャッシュのシリアル化と逆シリアル化を行うロジックを使用して、*beforeCacheAccess* および *afterCacheAccess* をオーバーライド (@Override) します。
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): トークン キャッシュにアクセスするコンテキストを表すインターフェイス。 

次に、トークンキャッシュのシリアル化/逆シリアル化のカスタム シリアル化の単純な実装を示します。 これをコピーして運用環境に貼り付けることは避けてください。

```Java
static class TokenPersistence implements ITokenCacheAccessAspect {
String data;

TokenPersistence(String data) {
        this.data = data;
}

@Override
public void beforeCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        iTokenCacheAccessContext.tokenCache().deserialize(data);
}

@Override
public void afterCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        data = iTokenCacheAccessContext.tokenCache().serialize();
}
```

```Java
// Loads cache from file
String dataToInitCache = readResource(this.getClass(), "/cache_data/serialized_cache.json");

ITokenCacheAccessAspect persistenceAspect = new TokenPersistence(dataToInitCache);

// By setting *TokenPersistence* on the PublicClientApplication, MSAL will call *beforeCacheAccess()* before accessing the cache and *afterCacheAccess()* after accessing the cache. 
PublicClientApplication app = 
PublicClientApplication.builder("my_client_id").setTokenCacheAccessAspect(persistenceAspect).build();
```

## <a name="learn-more"></a>詳細情報

[MSAL for Java を使用したトークン キャッシュのアカウントの取得と削除](msal-java-get-remove-accounts-token-cache.md)について説明します。

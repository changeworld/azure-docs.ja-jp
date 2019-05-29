---
title: HttpClient およびプロキシ (MSAL.NET) を提供する | Azure
description: Microsoft Authentication Library for .NET (MSAL.NET) を使用して Azure AD に接続するための独自の HttpClient およびプロキシを提供する方法について説明します。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 234c9d0724021017ec8c411d637420b05284ea52
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544173"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>MSAL.NET を使用した独自の HttpClient およびプロキシの提供
パブリック クライアント アプリケーションを初期化するときに、`.WithHttpClientFactory method` を使用して、独自の HttpClient を提供できます。  独自の HttpClient を提供すると、(たとえば、ASP.NET Core Web アプリ/API で) HTTP プロキシのきめ細やかな制御、ユーザー エージェント ヘッダーのカスタマイズ、MSAL に対する特定の HttpClient の使用の強制など、高度なシナリオを有効にします。

## <a name="initialize-with-httpclientfactory"></a>HttpClientFactory での初期化
次の例では、`HttpClientFactory` を作成し、続いてそれを使用してパブリック クライアント アプリケーションを初期化します。

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient および Xamarin iOS
Xamarin iOS を使用する場合は、明示的に iOS 7 以降用の `NSURLSession` ベースのハンドラー を使用する `HttpClient` を作成することをお勧めします。 MSAL.NET は、iOS 7 以降用の `NSURLSessionHandler` を使用する `HttpClient` を自動的に作成します。 詳細については、[HttpClient 用の Xamarin iOS ドキュメント](/xamarin/cross-platform/macios/http-stack)に関するページを参照してください。
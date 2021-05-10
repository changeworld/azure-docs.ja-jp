---
title: Media Services v3 に認証トークンを渡す | Microsoft Docs
description: クライアントから Media Services v3 キー配信サービスに認証トークンを送信する方法をについて説明します
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 590309ad392876ba3c5cb6d21abe777ab5a93efb
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281622"
---
# <a name="pass-tokens-to-the-azure-media-services-v3-key-delivery-service"></a>Azure Media Services v3 キー配信サービスにトークンを渡す

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

プレーヤーがキーを取得できるよう検証を受けるために Azure Media Services キー配信サービスにトークンを渡す方法についてお客様から質問を受けることがよくあります。 Media Services は、単純 Web トークン (SWT) 形式と JSON Web トークン (JWT) 形式をサポートしています。 システムで共通暗号化または Advanced Encryption Standard (AES) エンベロープ暗号化のどちらが使われていても、すべての種類のキーにトークン認証が適用されます。

 対象のプレーヤーとプラットフォームに応じて、プレーヤーは次の方法でトークンを渡すことができます。

## <a name="pass-a-token-through-the-http-authorization-header"></a>HTTP の Authorization ヘッダーを使用してトークンを渡す

> [!NOTE]
> OAuth 2.0 仕様に従い、"Bearer" プレフィックスが予期されます。 ビデオ ソースを設定するには、**AES (JWT トークン)** または **AES (SWT トークン)** を選んでください。 トークンは Authorization ヘッダーで渡されます。

## <a name="pass-a-token-via-the-addition-of-a-url-query-parameter-with-tokentokenvalue"></a>"token=tokenvalue" で URL クエリ パラメーターを追加することでトークンを渡します。

> [!NOTE]
> "Bearer" プレフィックスは想定されていません。 トークンは URL で送信されるので、トークン文字列の防御が必要です。 これを行う方法の C# サンプル コードを次に示します。

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

## <a name="pass-a-token-through-the-customdata-field"></a>CustomData フィールドを使用してトークンを渡す

このオプションは PlayReady ライセンス取得の場合だけであり、PlayReady ライセンス取得チャレンジの CustomData フィールドを使います。 この場合、トークンは次に示す XML ドキュメントの内部に存在する必要があります。

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```

認証トークンを Token 要素の中に置きます。

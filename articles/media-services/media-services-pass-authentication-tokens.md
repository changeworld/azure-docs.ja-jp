---
title: "Azure Media Services に認証トークンを渡す | Microsoft Docs"
description: "クライアントから Azure Media Services キー配信サービスに認証トークンを送信する方法をについて説明します"
services: media-services
keywords: "コンテンツ保護, DRM, トークン認証"
documentationcenter: 
author: dbgeorge
manager: jasonsue
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: dwgeo
ms.openlocfilehash: 0e56726266898e5738dd797a8a019987d457170e
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2017
---
# <a name="how-clients-pass-tokens-to-azure-media-services-key-delivery-service"></a>クライアントが Azure Media Services キー配信サービスにトークンを渡す方法
プレーヤーがトークンをキー配信サービスに渡し、検証されたキーをプレーヤーが取得する方法に関する質問をよく受け取ります。 サポートされているトークン形式は、単純 Web トークン (SWT) と JSON Web トークン (JWT) の 2 つです。 システムで行われているのが共通暗号化または AES エンベロープ暗号化のどちらであっても、任意の種類のキーにトークン認証を適用できます。

対象のプレーヤーとプラットフォームにより、プレーヤーは次の方法でトークンを渡すことができます。
- HTTP の Authorization ヘッダーを使います。
> [!NOTE]
> OAuth 2.0 仕様に従い、"Bearer" プレフィックスが予期されることに注意してください。 トークンが構成されたサンプル プレーヤーが、Azure Media Player の[デモ ページ](http://ampdemo.azureedge.net/)でホストされています。 AES (JWT トークン) または AES (SWT トークン) を選んでビデオ ソースを設定してください。 トークンは Authorization ヘッダーで渡されます。

- "token=tokenvalue" で URL クエリ パラメーターを追加します。  
> [!NOTE]
> "Bearer" プレフィックスは必要ないことに注意してください。 トークンは URL で送信されるので、トークン文字列の防御が必要です。 これを行う方法の C# サンプル コードを次に示します。

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

- CustomData フィールドを使います。
PlayReady ライセンス取得の場合のみ、PlayReady ライセンス取得チャレンジの CustomData フィールドを使います。 この場合、トークンは次に示す xml ドキュメントの内部に存在する必要があります。

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```
認証トークンを <Token> 要素の中に置きます。

- 代替 HLS 再生リストを使います。 iOS/Safari での AES + HLS 再生用にトークン認証を構成する必要がある場合は、トークンで直接送信する方法はありません。 代わりに再生リストを使ってこのシナリオを有効にする方法については、こちらの[ブログ投稿](http://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)をご覧ください。

## <a name="next-steps"></a>次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]
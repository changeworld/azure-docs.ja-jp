---
title: .NET バックエンド モバイル サービスでカスタム API を定義する方法 | Microsoft Docs
description: .NET バックエンド モバイル サービスでカスタム API エンドポイントを定義する方法について説明します。
services: mobile-services
documentationcenter: ''
author: ggailey777
manager: erikre
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# .NET バックエンド モバイル サービスでカスタム API エンドポイントを定義する方法
> [!div class="op_single_selector"]
> * [JavaScript バックエンド](mobile-services-javascript-backend-define-custom-api.md)
> * [.NET バックエンド](mobile-services-dotnet-backend-define-custom-api.md)
> 
> 

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> このトピックに相当する Mobile Apps のバージョンについては、「[方法: カスタム API コントローラーを定義する](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-custom-api-controller)」を参照してください。
> 
> 

このトピックでは、.NET バックエンド モバイル サービスでカスタム API エンドポイントを定義する方法を紹介します。カスタム API を使用するとサーバー機能を持つカスタム エンドポイントを定義できますが、データベースの insert、update、delete、read 操作には対応しません。カスタム API を使用することにより、HTTP ヘッダーや本文の形式など、メッセージングをいっそう詳細に制御できます。

[!INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

Mobile Services クライアント ライブラリを使用し、アプリでカスタム API を呼び出す方法に関する詳細については、クライアント SDK 参照の「[カスタム API の呼び出し](mobile-services-windows-dotnet-how-to-use-client-library.md#custom-api)」を参照してください。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=AcomDC_0727_2016-->
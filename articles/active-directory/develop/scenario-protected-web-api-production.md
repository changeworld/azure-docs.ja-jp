---
title: 保護された Web API から運用環境への移行 | Azure
titleSuffix: Microsoft identity platform
description: 保護された Web API を構築する方法 (運用環境への移行) について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 98be47200c12ab3097f9416b5647abf6465a89e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675976"
---
# <a name="protected-web-api---move-to-production"></a>保護された Web API - 運用環境への移行

Web API を保護する方法について説明しました。ここでは、アプリケーションを運用環境に移行する場合に考慮すべき点をいくつか示します。

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>次のステップ

「[シナリオ:Web API を呼び出す Web API](scenario-web-api-call-api-overview.md)」でダウンストリーム API を呼び出す方法について学習します。


詳細については、GitHub のチュートリアルとサンプルを参照してください。

- [デーモンを使用することによる保護された API の呼び出し](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/2-Call-OwnApi)
- [ASP.NET Core Web API のチュートリアル](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)
- [ASP.NET Web API のサンプル](https://github.com/azureadquickstarts/appmodelv2-nativeclient-dotnet)

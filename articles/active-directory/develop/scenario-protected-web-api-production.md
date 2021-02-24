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
ms.openlocfilehash: bf8eb144ab23d45e57ee8d52f06363ea37b600ec
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2020
ms.locfileid: "94375280"
---
# <a name="protected-web-api---move-to-production"></a>保護された Web API - 運用環境への移行

Web API を保護する方法を説明したので、運用環境への移行について説明します。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>次のステップ

「[シナリオ:Web API を呼び出す Web API](scenario-web-api-call-api-overview.md)」でダウンストリーム API を呼び出す方法について学習します。


詳細については、GitHub のチュートリアルとサンプルを参照してください。

- [デーモンを使用することによる保護された API の呼び出し](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/2-Call-OwnApi)
- [ASP.NET Core Web API のチュートリアル](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)
- [ASP.NET Web API のサンプル](https://github.com/azureadquickstarts/appmodelv2-nativeclient-dotnet)

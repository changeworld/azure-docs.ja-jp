---
title: Azure AD v2 Windows デスクトップの概要 - 構成 | Microsoft Docs
description: Windows デスクトップ .NET (XAML) アプリケーションでアクセス トークンを取得し、Azure Active Directory v2 エンドポイントで保護されている API を呼び出す方法
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: e84d99d18d999a7b629f65f9d18355321f04e538
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712506"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>アプリへのアプリケーション登録情報の追加
この手順では、アプリケーション ID をプロジェクトに追加する必要があります。

1.  `App.xaml.cs` を開き、`ClientId` を含む行を以下に置き換えます。

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>次の手順

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

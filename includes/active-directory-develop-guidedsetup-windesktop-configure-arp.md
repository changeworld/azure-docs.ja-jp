---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: e7b97d0d4489544275436b8048ca033480d53c59
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843625"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>アプリへのアプリケーション登録情報の追加
この手順では、アプリケーション ID をプロジェクトに追加する必要があります。

1.  `App.xaml.cs` を開き、`ClientId` を含む行を以下に置き換えます。

```csharp
private static string ClientId = "[Enter the application Id here]";
```

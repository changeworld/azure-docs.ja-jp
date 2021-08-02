---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: fe128e7eee99d2b396c758979f0efff936ec6785
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112073068"
---
認証後、ユーザーは、ベアラー トークン認証を使用して、保護された Web API を呼び出すページに移動します。 ベアラー トークンは Azure AD B2C から取得されたアクセス トークンです。 アプリでは、HTTPS 要求の Authorization ヘッダーでトークンを渡します。 
    
```http
Authorization: Bearer <token>
```

アクセス トークンのスコープが Web API のスコープと一致しない場合、認証ライブラリでは正しいスコープの新しいアクセス トークンが取得されます。
s
---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: 3d4bb5ff840a2f4ee5c4c33e2cc51dfe440866a7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778851"
---
認証が完了した後、ユーザーがアプリと対話すると、保護された Web API が呼び出されます。 その Web API では、[ベアラー トークン](https://datatracker.ietf.org/doc/html/rfc6750)認証が使用されます。 ベアラー トークンは、アプリによって Azure AD B2C から取得されたアクセス トークンです。 アプリでは、HTTPS 要求の Authorization ヘッダーでトークンを渡します。 
    
```http
Authorization: Bearer <token>
```

アクセス トークンのスコープが Web API のスコープと一致しない場合、認証ライブラリでは正しいスコープの新しいアクセス トークンが取得されます。

---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: ebc113e993eadc41c2b1c58c9130908727101a6f
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227691"
---
認証が完了した後、ユーザーがアプリと対話すると、保護された Web API が呼び出されます。 その Web API では、[ベアラー トークン](https://datatracker.ietf.org/doc/html/rfc6750)認証が使用されます。 ベアラー トークンは、アプリによって Azure AD B2C から取得されたアクセス トークンです。 アプリでは、HTTPS 要求の Authorization ヘッダーでトークンを渡します。 
    
```http
Authorization: Bearer <token>
```

アクセス トークンのスコープが Web API のスコープと一致しない場合、認証ライブラリでは正しいスコープの新しいアクセス トークンが取得されます。

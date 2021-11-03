---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: kengaderdus
ms.openlocfilehash: 9de7912b426d659a03e8a7653c690cb16f83010e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007604"
---
認証が完了した後、ユーザーがアプリと対話すると、保護された Web API が呼び出されます。 その Web API では、[ベアラー トークン](https://datatracker.ietf.org/doc/html/rfc6750)認証が使用されます。 ベアラー トークンは、アプリによって Azure AD B2C から取得されたアクセス トークンです。 アプリでは、HTTPS 要求の Authorization ヘッダーでトークンを渡します。 
    
```http
Authorization: Bearer <token>
```

アクセス トークンのスコープが Web API のスコープと一致しない場合、認証ライブラリでは正しいスコープの新しいアクセス トークンが取得されます。

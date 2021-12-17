---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: bf7295f4cc7b2010e7c700fbde25840f7a7f0df1
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037083"
---
## <a name="pass-an-id-token-hint"></a>ID トークン ヒントを渡す

証明書利用者アプリケーションからは、OAuth2 認可要求の一部としてインバウンド JSON Web トークン (JWT) を送信できます。  インバウンド トークンは、ユーザーまたは認可要求に関するヒントです。 Azure AD B2C によってトークンが検証された後、クレームが抽出されます。

認証要求に ID トークン ヒントを含めるには、次のようにします。
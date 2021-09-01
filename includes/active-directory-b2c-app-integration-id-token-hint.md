---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: f66eac86f4267a7b824eb551cdd877d3d34472a0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723209"
---
## <a name="pass-an-id-token-hint"></a>ID トークン ヒントを渡す

証明書利用者アプリケーションからは、OAuth2 認可要求の一部としてインバウンド JSON Web トークン (JWT) を送信できます。  インバウンド トークンは、ユーザーまたは認可要求に関するヒントです。 Azure AD B2C によってトークンが検証された後、クレームが抽出されます。

認証要求に ID トークン ヒントを含めるには、次のようにします。
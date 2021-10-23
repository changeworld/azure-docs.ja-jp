---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: 33b42af7804c2c48a0112e0f8eb502c253813f47
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037096"
---
## <a name="use-a-custom-domain"></a>カスタム ドメインの使用

[カスタム ドメイン](../articles/active-directory-b2c/custom-domain.md)を使用すると、認証 URL を完全にブランド化できます。 ユーザーの観点からは、認証プロセスの間、ユーザーは Azure AD B2C b2clogin.com ドメイン名にリダイレクトされるのではなく、ドメインにとどまります。

URL 内の "b2c" へのすべての参照を削除するために、認証要求 URL の B2C テナント名 contoso.onmicrosoft.com をテナント ID GUID に置換することもできます。 たとえば、`https://fabrikamb2c.b2clogin.com/contoso.onmicrosoft.com/` を `https://account.contosobank.co.uk/<tenant ID GUID>/` に変更できます。

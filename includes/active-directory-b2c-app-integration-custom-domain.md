---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: e6ab23e9d98d2bb1dfea21a7b4f681dbd5be6e14
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801862"
---
## <a name="use-a-custom-domain"></a>カスタム ドメインの使用

[カスタム ドメイン](../articles/active-directory-b2c/custom-domain.md)を使用すると、認証 URL を完全にブランド化できます。 ユーザーから見ると、認証プロセスの間、ユーザーは Azure AD B2C b2clogin.com ドメイン名にリダイレクトされるのではなく、ドメインにとどまっています。

認証要求 URL の B2C テナント名 (contoso.onmicrosoft.com) をテナント ID GUID に置換して、URL の "b2c" へのすべての参照を削除することもできます。 たとえば、`https://fabrikamb2c.b2clogin.com/contoso.onmicrosoft.com/` を `https://account.contosobank.co.uk/<tenant ID GUID>/` に変更できます。

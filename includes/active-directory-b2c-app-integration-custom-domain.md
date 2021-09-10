---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: 8547dac26c8d3a6a286a49a6b6e12919199a994b
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220331"
---
## <a name="use-a-custom-domain"></a>カスタム ドメインの使用

[カスタム ドメイン](../articles/active-directory-b2c/custom-domain.md)を使用すると、認証 URL を完全にブランド化できます。 ユーザーの観点からは、認証プロセスの間、ユーザーは Azure AD B2C b2clogin.com ドメイン名にリダイレクトされるのではなく、ドメインにとどまります。

URL 内の "b2c" へのすべての参照を削除するために、認証要求 URL の B2C テナント名 contoso.onmicrosoft.com をテナント ID GUID に置換することもできます。 たとえば、`https://fabrikamb2c.b2clogin.com/contoso.onmicrosoft.com/` を `https://account.contosobank.co.uk/<tenant ID GUID>/` に変更できます。

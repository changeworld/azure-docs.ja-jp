---
ms.openlocfilehash: fa43176a945a01651dc768558d14604d0a15b300
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113726836"
---
コードを更新するための以下の手順は、すべての機密クライアント シナリオに適用されます。

1. ソース コードに MSAL.NET 名前空間を追加します: `using Microsoft.Identity.Client;`。
2. `AuthenticationContext` をインスタンス化する代わりに、`ConfidentialClientApplicationBuilder.Create` を使用して `IConfidentialClientApplication` をインスタンス化します。
3. MSAL.NET は、`resourceId` 文字列ではなくスコープを使用します。 ADAL.NET を使用するアプリケーションは事前認証されているので、常に次のスコープを使用できます: `new string[] { $"{resourceId}/.default" }`。
4. `AuthenticationContext.AcquireTokenAsync` への呼び出しを `IConfidentialClientApplication.AcquireTokenXXX` への呼び出しで置き換えます。*XXX* は、シナリオによって異なります。

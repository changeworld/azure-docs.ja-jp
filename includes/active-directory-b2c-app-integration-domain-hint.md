---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: 8363f7d13c2a991df031e3a2ca53efb8c1b11761
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037101"
---
## <a name="preselect-an-identity-provider"></a>ID プロバイダーを事前に選択する

Facebook、LinkedIn、Google などのソーシャル アカウントを含むようにアプリケーションのサインイン プロセスを構成した場合は、`domain_hint` パラメーターを指定できます。 このクエリ パラメーターは、サインインに使用する必要があるソーシャル ID プロバイダーに関するヒントを Azure AD B2C に提供します。 たとえば、アプリケーションで `domain_hint=facebook.com` を指定した場合、サインイン フローで Facebook のサインイン ページに直接移動します。 

外部 ID プロバイダーにユーザーをリダイレクトするには、以下を実行します。

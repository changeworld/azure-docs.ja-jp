---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: bade30eb42235e65170fc59ad77fa2f4ce1114c3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723212"
---
## <a name="preselect-an-identity-provider"></a>ID プロバイダーを事前に選択する

Facebook、LinkedIn、Google などのソーシャル アカウントを含むようにアプリケーションのサインイン プロセスを構成した場合は、`domain_hint` パラメーターを指定できます。 このクエリ パラメーターは、サインインに使用する必要があるソーシャル ID プロバイダーに関するヒントを Azure AD B2C に提供します。 たとえば、アプリケーションで `domain_hint=facebook.com` を指定した場合、サインイン フローで Facebook のサインイン ページに直接移動します。 

外部 ID プロバイダーにユーザーをリダイレクトするには、以下を実行します。

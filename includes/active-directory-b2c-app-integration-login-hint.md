---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: 9fb96a0427ecaa3431eddb47ca89458a6dcf5901
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723206"
---
## <a name="prepopulate-the-sign-in-name"></a>サインイン名を事前入力する

サインイン ユーザー体験中に、アプリが特定のユーザーをターゲットにする場合があります。 アプリがユーザーをターゲットにするとき、認可要求でユーザー サインイン名と共に `login_hint` クエリ パラメーターを指定できます。 Azure AD B2C によってサインイン名が自動的に入力されるので、ユーザーはパスワードを入力するだけで済みます。 

サインイン名を事前入力するには、次の手順を実行します。
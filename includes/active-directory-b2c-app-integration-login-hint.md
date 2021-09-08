---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: 2bab69917caf103cd4e2f0b10e28b46acd8e48c3
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220252"
---
## <a name="prepopulate-the-sign-in-name"></a>サインイン名を事前入力する

サインイン ユーザー体験中に、アプリが特定のユーザーをターゲットにする場合があります。 アプリがユーザーを対象とする場合は、認証要求にユーザーのサインイン名を含む `login_hint` クエリ パラメーターを指定できます。 Azure AD B2C によってサインイン名が自動的に入力されるので、ユーザーはパスワードを入力するだけで済みます。 

サインイン名を事前入力するには、次の手順を実行します。
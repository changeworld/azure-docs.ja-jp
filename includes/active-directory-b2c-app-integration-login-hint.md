---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: c2409ef7d846ff7d4d67d7bf79ab96760375da81
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037056"
---
## <a name="prepopulate-the-sign-in-name"></a>サインイン名を事前入力する

サインイン ユーザー体験中に、アプリが特定のユーザーをターゲットにする場合があります。 アプリがユーザーを対象とする場合は、認証要求にユーザーのサインイン名を含む `login_hint` クエリ パラメーターを指定できます。 Azure AD B2C によってサインイン名が自動的に入力されるので、ユーザーはパスワードを入力するだけで済みます。 

サインイン名を事前入力するには、次の手順を実行します。
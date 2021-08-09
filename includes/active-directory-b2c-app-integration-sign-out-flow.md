---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: f4677d9800f0c94d9071838b9addbeb3cd1231c9
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071341"
---
サインアウト フローには、次の手順が含まれます。

1. ユーザーがアプリから **[サインアウト]** を選択します。
1. アプリによってそのセッション Cookie がクリアされ、認証ライブラリによってそのトークン キャッシュがクリアされます。
1. アプリによって、ユーザーが Azure AD B2C ログアウト エンドポイントにリダイレクトされ、Azure AD B2C セッションが終了されます。
1. ユーザーは元のアプリにリダイレクトされます。
---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: f21c0a37d169885e60ae9dea34663dc0a86e161f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723287"
---
サインアウト フローには、次の手順が含まれます。

1. アプリから、ユーザーがサインアウトします。
1. アプリによってそのセッション オブジェクトがクリアされ、認証ライブラリによってそのトークン キャッシュがクリアされます。
1. アプリによってユーザーが Azure AD B2C ログアウト エンドポイントに移動し、Azure AD B2C セッションが終了されます。
1. ユーザーが再びアプリにリダイレクトされます。

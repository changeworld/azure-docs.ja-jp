---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: 7196d4596a3e67f1482fe2955a933fbf9efc8d47
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227802"
---
サインアウト フローには、次の手順が含まれます。

1. アプリから、ユーザーがサインアウトします。
1. アプリによってそのセッション オブジェクトがクリアされ、認証ライブラリによってそのトークン キャッシュがクリアされます。
1. アプリによってユーザーが Azure AD B2C サインアウト エンドポイントに移動し、Azure AD B2C セッションが終了されます。
1. ユーザーが再びアプリにリダイレクトされます。

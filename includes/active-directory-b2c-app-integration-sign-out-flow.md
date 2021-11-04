---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: kengaderdus
ms.openlocfilehash: 0a29cf2d195e1fdd0bda03f718b1693fffaa5e9c
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007591"
---
サインアウト フローには、次の手順が含まれます。

1. アプリから、ユーザーがサインアウトします。
1. アプリによってそのセッション オブジェクトがクリアされ、認証ライブラリによってそのトークン キャッシュがクリアされます。
1. アプリによってユーザーが Azure AD B2C サインアウト エンドポイントに移動し、Azure AD B2C セッションが終了されます。
1. ユーザーが再びアプリにリダイレクトされます。

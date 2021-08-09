---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: 6d62dda341c6acb783bd2525c2f6ba7a76b312ec
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112073069"
---
1. **[管理]** の下にある **[API のアクセス許可]** を選択します。
1. **[構成されたアクセス許可]** の下で **[アクセス許可の追加]** を選択します。
1. **[自分の API]** タブを選択します。
1. Web アプリケーションにアクセスを許可する API を選択します。 たとえば、*my-api1* です。
1. **[アクセス許可]** で、 **[タスク]** を展開し、前に定義したスコープを選択します。 たとえば、*tasks.read* や *tasks.write* です。
1. **[アクセス許可の追加]** を選択します.
1. **[<テナント名> に管理者の同意を与えます]** を選択します。
1. **[はい]** を選択します。
1. **[更新]** を選択し、両方のスコープの **[状態]** に、"... に付与されました" が表示されていることを確認します。
1. **[構成されたアクセス許可]** の一覧からスコープを選択し、スコープの完全な名前をコピーします。 

    ![Web アプリケーションのスコープを取得する](./media/active-directory-b2c-app-integration-grant-permissions/get-azure-ad-b2c-app-permissions.png)  
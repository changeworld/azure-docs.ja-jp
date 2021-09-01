---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: mimart
ms.openlocfilehash: b356480175c4b3da537b1e3e49fcdca676b2d6d7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778850"
---
アプリ (アプリ ID: 1) にアクセス許可を付与するには、次の手順のようにします。 

1. **[アプリの登録]** を選択し、作成したアプリを選択します (アプリ ID: 1)。
1. **[管理]** の下にある **[API のアクセス許可]** を選択します。
1. **[構成されたアクセス許可]** の下で **[アクセス許可の追加]** を選択します。
1. **[自分の API]** タブを選択します。
1. Web アプリケーションにアクセスを許可する必要のある API を選択します (アプリ ID: 2)。 たとえば、*my-api1* です。
1. **[アクセス許可]** で、 **[タスク]** を展開し、前に定義したスコープを選択します(たとえば、*tasks.read* と *tasks.write*)。
1. **[アクセス許可の追加]** を選択します.
1. **[\<*your tenant name*> に管理者の同意を与えます]** を選択します。
1. **[はい]** を選択します。
1. **[更新]** を選択し、両方のスコープの **[状態]** に、"... に付与されました" が表示されていることを確認します。
1. **[構成されたアクセス許可]** の一覧からスコープを選択し、スコープの完全な名前をコピーします。 

    ![読み取りアクセス許可が付与されていることを示す、[構成されたアクセス許可] ペインのスクリーンショット。](./media/active-directory-b2c-app-integration-grant-permissions/get-azure-ad-b2c-app-permissions.png)  
---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: kengaderdus
ms.openlocfilehash: eba3d57bab13cf573cd6e4478c0df672e3b34458
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007586"
---
アプリ (**アプリ ID: 1**) にアクセス許可を付与するには、次の手順をおこないます。 

1. **[アプリの登録]** を選択し、作成したアプリを選択します (**アプリ ID: 1**)。
1. **[管理]** の下にある **[API のアクセス許可]** を選択します。
1. **[構成されたアクセス許可]** の下で **[アクセス許可の追加]** を選択します。
1. **[自分の API]** タブを選択します。
1. Web アプリケーションへのアクセス許可が必要な API を選択します (**アプリ ID: 2**)。 たとえば、「**my-api1**」と入力します。
1. **[アクセス許可]** で、 **[タスク]** を展開し、前に定義したスコープを選択します(たとえば、**tasks.read** と **tasks.write**)。
1. **[アクセス許可の追加]** を選択します.
1. **[\<*your tenant name*> に管理者の同意を与えます]** を選択します。
1. **[はい]** を選択します。
1. **[最新の情報に更新]** を選択し、両方のスコープの **[状態]** に、**Granted for ...(... に付与されました)** と表示されていることを確認します。
1. **[Configured permissions (構成済みのアクセス許可)]** の一覧からスコープを選択し、スコープの完全な名前をコピーします。 

    ![読み取りアクセス許可が付与されたことを示す、[Configured permissions (構成済みのアクセス許可)] ペインのスクリーンショット。](./media/active-directory-b2c-app-integration-grant-permissions/get-azure-ad-b2c-app-permissions.png)  
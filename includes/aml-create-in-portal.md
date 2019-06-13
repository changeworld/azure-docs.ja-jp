---
title: インクルード ファイル
description: インクルード ファイル
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/21/2019
ms.openlocfilehash: 72f23b10047928f32886d9054f4dd1abdc569bd8
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/30/2019
ms.locfileid: "66396905"
---
1. 使用する Azure サブスクリプションの資格情報を使って、[Azure portal](https://portal.azure.com/) にサインインします。 

   ![Azure ポータル](./media/aml-create-in-portal/portal-dashboard-05-2019.png)

1. ポータルの左上隅にある **[+ リソースの作成]** を選択します。

   ![Azure Portal でのリソースの作成](./media/aml-create-in-portal/portal-create-a-resource-05-2019.png)

1. 検索バーに「**Machine Learning**」と入力します。 **[Machine Learning service workspace]** という検索結果を選択します。

   ![ワークスペースを検索する](./media/aml-create-in-portal/allservices-search-05-2019.png)

1. **[ML サービス ワークスペース]** ウィンドウで、 **[作成]** を選択して開始します。

    ![[作成] ボタン](./media/aml-create-in-portal/portal-create-button-05-2019.png)

1. **[ML service Workspace]\(ML サービス ワークスペース\)** ウィンドウで、ワークスペースを構成します。

   フィールド|説明
   ---|---
   ワークスペース名 |ワークスペースを識別する一意名を入力します。 この例では、**docs-ws** を使用します。 名前は、リソース グループ全体で一意である必要があります。 覚えやすく、他のユーザーが作成したワークスペースと区別しやすい名前を使用します。  
   サブスクリプション |使用する Azure サブスクリプションを選択します。
   リソース グループ | サブスクリプションの既存のリソース グループを使用するか、任意の名前を入力して新しいリソース グループを作成します。 リソース グループは、Azure ソリューションの関連するリソースを保持します。 この例では、**docs-aml** を使用します。 
   Location | ユーザーとデータ リソースに最も近い場所を選択します。 この場所に、ワークスペースが作成されます。

   ![ワークスペースの作成](./media/aml-create-in-portal/workspace-create-main-tab-05-2019.png)

1. 作成プロセスを開始するには、 **[確認および作成]** を選択します。

    ![Create](./media/aml-create-in-portal/workspace-create-main-review-button-05-2019.png)

1. ワークスペースの構成を確認します。 正しい場合は、 **[作成]** を選択します。 ワークスペースの作成には、しばらく時間がかかる場合があります。

    ![Create](./media/aml-create-in-portal/workspace-create-review-tab-05-2019.png)

1. デプロイの状態を確認するには、ツール バーの [通知] アイコン (**ベル**) を選択します。

1. プロセスが完了すると、デプロイ成功メッセージが表示されます。 このメッセージは、通知セクションにも表示されます。 新しいワークスペースを表示するには、 **[Go to resource]\(リソースに移動\)** を選択します。

   ![ワークスペースの作成の状態](./media/aml-create-in-portal/notifications-05-2019.png)

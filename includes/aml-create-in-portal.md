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
ms.date: 09/24/2018
ms.openlocfilehash: edcb2ecb74255ddbb8d601cb69565fb401b756d2
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52886307"
---
使用する Azure サブスクリプションの資格情報を使って、[Azure portal](https://portal.azure.com/) にサインインします。 

ポータルのワークスペース ダッシュボードは、Edge、Chrome、および Firefox ブラウザーのみでサポートされます。

   ![Azure ポータル](./media/aml-create-in-portal/portal-dashboard.png)

ポータルの左上隅にある **[+ リソースの作成]** を選択します。

   ![Azure Portal でのリソースの作成](./media/aml-create-in-portal/portal-create-a-resource.png)

検索バーに「**Machine Learning**」と入力します。 **[Machine Learning service workspace]** という検索結果を選択します。

   ![ワークスペースを検索する](./media/aml-create-in-portal/allservices-search.PNG)

**[Machine Learning service workspace]** ウィンドウを一番下までスクロールし、**[作成]** を選択して開始します。

   ![Create](./media/aml-create-in-portal/portal-create-button.png)

**[ML service Workspace]\(ML サービス ワークスペース\)** ウィンドウで、ワークスペースを構成します。

   フィールド|説明
   ---|---
   ワークスペース名 |ワークスペースを識別する一意名を入力します。 ここでは、docs-ws を使用します。 名前は、リソース グループ全体で一意である必要があります。 覚えやすく、他のユーザーが作成したワークスペースと区別しやすい名前を使用します。  
   サブスクリプション |使用する Azure サブスクリプションを選択します。
   リソース グループ | サブスクリプションの既存のリソース グループを使用するか、任意の名前を入力して新しいリソース グループを作成します。 リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 ここでは、docs-aml を使用します。 
   場所 | ユーザーとデータ リソースに最も近い場所を選択します。 この場所に、ワークスペースが作成されます。

   ![ワークスペースの作成](./media/aml-create-in-portal/workspace-create.png)

作成プロセスを開始するには、**[作成]** を選択します。 ワークスペースの作成には、しばらく時間がかかる場合があります。

デプロイの状態を確認するには、ツール バーの [通知] アイコン (ベル) を選択します。

   ![ワークスペースの作成の状態](./media/aml-create-in-portal/notifications.png)

プロセスが完了すると、デプロイ成功メッセージが表示されます。 このメッセージは、通知セクションにも表示されます。 新しいワークスペースを表示するには、**[Go to resource]\(リソースに移動\)** を選択します。

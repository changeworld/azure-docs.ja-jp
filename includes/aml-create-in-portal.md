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
ms.openlocfilehash: 4b4f926009f94162b43dcbb8934e44c1924e91df
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239534"
---
使用する Azure サブスクリプションの資格情報を使用して [Azure Portal](https://portal.azure.com/) にサインインします。 Azure サブスクリプションをお持ちでない場合は、ここで[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

ポータルのワークスペース ダッシュボードは、Edge、Chrome、および Firefox ブラウザーでのみでサポートされます。

   ![Azure ポータル](./media/aml-create-in-portal/portal-dashboard.png)

ポータルの左上隅にある **[リソースの作成]** ボタン (+) を選択します。

   ![Azure Portal でのリソースの作成](./media/aml-create-in-portal/portal-create-a-resource.png)

検索バーに「**Machine Learning**」と入力します。 **[Machine Learning service workspace]** という検索結果を選択します。

   ![ワークスペースを検索する](./media/aml-create-in-portal/allservices-search.PNG)

**[Machine Learning service workspace]** ウィンドウを一番下までスクロールし、**[作成]** を選択して開始します。

   ![create](./media/aml-create-in-portal/portal-create-button.png)

**[ML service Workspace]\(ML サービス ワークスペース\)** ウィンドウで、ワークスペースを構成します。

   フィールド|説明
   ---|---
   ワークスペース名 |ワークスペースを識別する一意名を入力します。  ここでは、docs-ws を使用します。 名前は、リソース グループ全体で一意である必要があります。 憶えやすく、他のユーザーが作成したワークスペースと区別しやすい名前を使用します。  
   サブスクリプション |使用する Azure サブスクリプションを選択します。 複数のサブスクリプションをお持ちの場合は、リソースが課金の対象となっている適切なサブスクリプションを選択してください。
   リソース グループ | サブスクリプションの既存のリソース グループを使用するか、任意の名前を入力して新しいリソース グループを作成します。 リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。  ここでは、docs-aml を使用します。 
   Location | ユーザーとデータ リソースに最も近い場所を選択します。 ここにワークスペースが作成されます。

   ![ワークスペースを作成する](./media/aml-create-in-portal/workspace-create.png)

**[作成]** を選択して、作成プロセスを開始します。  ワークスペースの作成には、しばらく時間がかかる場合があります。

   デプロイの状態を確認するには、ツール バーの [通知] アイコン (ベル) を選択します。

   ![ワークスペースを作成する](./media/aml-create-in-portal/notifications.png)

   完了すると、デプロイ成功メッセージが表示されます。  通知セクションにも表示されます。   新しいワークスペースを表示するには、**[リソースに移動]** ボタンをクリックします。

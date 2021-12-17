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
ms.date: 05/11/2021
ms.openlocfilehash: 42af7691d7401e397e7e0d9819fded60b3821b26
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2021
ms.locfileid: "109785322"
---
1. ご利用の Azure サブスクリプションの資格情報を使って、[Azure portal](https://portal.azure.com/) にサインインします。

1. Azure portal の左上隅で 3 本のバーを選択し、 **[+ リソースの作成]** を選択します。

    :::image type="content" source="media/aml-create-in-portal/create-resource.png" alt-text="[+ リソースの作成] を示すスクリーンショット。":::

1. 検索バーを使用して、**Machine Learning** を見つけます。

1. **[Machine Learning]** を選択します。

   :::image type="content" source="media/aml-create-in-portal/machine-learning.png" alt-text="[Machine Learning] を選択するための検索結果を示すスクリーンショット。":::


1. **[Machine Learning]** ウィンドウで、 **[作成]** を選択して開始します。

1. 新しいワークスペースを構成するには、次の情報を指定します。

   フィールド|説明
   ---|---
   ワークスペース名 |ワークスペースを識別する一意の名前を入力します。 この例では、**docs-ws** を使用します。 名前は、リソース グループ全体で一意である必要があります。 覚えやすく、他のユーザーが作成したワークスペースと区別しやすい名前を使用します。
   サブスクリプション |使用する Azure サブスクリプションを選択します。
   Resource group | サブスクリプションの既存のリソース グループを使用するか、任意の名前を入力して新しいリソース グループを作成します。 リソース グループは、Azure ソリューションの関連するリソースを保持します。 この例では、**docs-aml** を使用します。 
   場所 | ユーザーとデータ リソースに最も近い場所を選択し、ワークスペースを作成します。

1. ワークスペースの構成が完了したら、 **[確認および作成]** を選択します。
1. **[作成]** を選択して、ワークスペースを作成します。

   > [!Warning]
   > クラウドへのワークスペースの作成には数分かかる場合があります。

   プロセスが完了すると、デプロイ成功メッセージが表示されます。
 
 1. 新しいワークスペースを表示するには、 **[Go to resource]\(リソースに移動\)** を選択します。
 1. ワークスペースのポータル ビューから、 **[スタジオの起動]** を選択して、Azure Machine Learning スタジオに移動します。 


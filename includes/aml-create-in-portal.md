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
ms.date: 11/04/2019
ms.openlocfilehash: f5f132d257e30cd8f4fa1153087bf0df2f0f5b2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91841852"
---
1. ご利用の Azure サブスクリプションの資格情報を使って、[Azure portal](https://portal.azure.com/) にサインインします。

1. Azure portal の左上隅にある **[+ リソースの作成]** を選択します。

    ![[リソースの作成] オプションを示すスクリーンショット。](media/aml-create-in-portal/create-workspace.gif)

1. 検索バーを使用して、**Machine Learning** を見つけます。

1. **[Machine Learning]** を選択します。

1. **[Machine Learning]** ウィンドウで、 **[作成]** を選択して開始します。

1. 新しいワークスペースを構成するには、次の情報を指定します。

   フィールド|説明
   ---|---
   ワークスペース名 |ワークスペースを識別する一意の名前を入力します。 この例では、**docs-ws** を使用します。 名前は、リソース グループ全体で一意である必要があります。 覚えやすく、他のユーザーが作成したワークスペースと区別しやすい名前を使用します。
   サブスクリプション |使用する Azure サブスクリプションを選択します。
   Resource group | サブスクリプションの既存のリソース グループを使用するか、任意の名前を入力して新しいリソース グループを作成します。 リソース グループは、Azure ソリューションの関連するリソースを保持します。 この例では、**docs-aml** を使用します。 
   Location | ユーザーとデータ リソースに最も近い場所を選択し、ワークスペースを作成します。
   ワークスペースのエディション | このチュートリアルでは、ワークスペースの種類として **[Basic]** を選択します。 ワークスペースの種類によって、アクセス可能で価格設定の対象となる機能が決まります。 このチュートリアルの内容はすべて、Basic と Enterprise のどちらのワークスペースでも実行できます。

1. ワークスペースの構成が完了したら、 **[確認および作成]** を選択します。

   > [!Warning]
   > クラウドへのワークスペースの作成には数分かかる場合があります。

   プロセスが完了すると、デプロイ成功メッセージが表示されます。
 
 1. 新しいワークスペースを表示するには、 **[Go to resource]\(リソースに移動\)** を選択します。


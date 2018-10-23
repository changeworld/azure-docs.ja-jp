---
title: インクルード ファイル
description: インクルード ファイル
services: machine-learning
author: j-martens
ms.service: machine-learning
ms.author: jmartens
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 07/27/2018
ms.openlocfilehash: 300b4a3e5c6c52f1c09f8e70a72f08406861a5d1
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400263"
---
>[!IMPORTANT]
>作成したリソースは、Azure Machine Learning サービスに関連したその他のチュートリアルおよびハウツー記事の前提条件として使用できます。 


ここで作成したリソースを今後使用する予定がない場合は、課金が発生しないように削除します。

1. Azure Portal で、左端にある **[リソース グループ]** を選択します。
 
   ![Azure Portal での削除](./media/aml-delete-resource-group/delete-resources.png)

1. 作成したリソース グループを一覧から選択します。

1. **[リソース グループの削除]** を選択します。

1. リソース グループの名前を入力し、**[削除]** を選択します。

   "入れ子になっているリソースを削除する前にリソースを削除することはできません" というエラー メッセージが表示される場合は、入れ子になったリソースを先に削除する必要があります。 入れ子になったリソースを削除する方法については、[このトラブルシューティングのセクション](../articles/machine-learning/desktop-workbench/known-issues-and-troubleshooting-guide.md#cant-delete-experimentation-account)を参照してください。 

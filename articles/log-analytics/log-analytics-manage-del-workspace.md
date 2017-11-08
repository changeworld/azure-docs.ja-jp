---
title: "Azure Log Analytics ワークスペースを削除する | Microsoft Docs"
description: "個人用の月額プランで Log Analytics ワークスペースを作成した場合や、ワークスペース モデルを再構築する場合に、Log Analytics ワークスペースを削除する方法について説明します。"
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 3d99f9869dfdfbe18f82e873bd367cc85f9414f1
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/01/2017
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Azure Portal で Azure Log Analytics ワークスペースを削除する
このトピックでは、不要になった Azure Log Analytics ワークスペースを Azure Portal を使用して削除する方法を示します。 

## <a name="to-delete-a-workspace"></a>ワークスペースを削除するには 
Log Analytics ワークスペースを削除すると、そのワークスペースに関連するすべてのデータが 30 日以内にサービスから削除されます。  サービス操作に悪影響を及ぼす重要なデータや構成が含まれていることがあるため、ワークスペースを削除する場合は注意が必要です。  
 
ユーザーが管理者で、ワークスペースに関連付けられている複数のユーザーが存在する場合は、それらのユーザーとワークスペースの間の関連付けがなくなります。 ユーザーが他のワークスペースに関連付けられている場合は、その他のワークスペースを使用して Log Analytics を継続して使用できます。 ただし、他のワークスペースに関連付けられていない場合、Log Analytics を使用するには、ワークスペースを作成する必要があります。 

1. [Azure Portal](http://portal.azure.com) にサインインします。 
2. Azure Portal で、左下隅にある **[その他のサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。
3. Log Analytics サブスクリプション ウィンドウでワークスペースを選択して、中央のウィンドウの上部にある **[削除]** をクリックします。<br><br> ![ワークスペースのプロパティ ウィンドウの削除オプション](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. ワークスペースの削除を確定するよう求める確認メッセージ ウィンドウが表示されたら、**[はい]** をクリックします。<br><br> ![ワークスペースの削除の確定](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)


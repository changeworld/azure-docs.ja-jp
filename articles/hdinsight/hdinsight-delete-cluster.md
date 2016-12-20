---
title: "HDInsight クラスターを削除する方法 | Microsoft Docs"
description: "HDInsight クラスターの各種削除方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 55f7838b-9786-47ff-96db-1b64437bd0bb
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/28/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 67442bf4b04f6f3799d30f7ce26547c8145d9168


---
# <a name="how-to-delete-an-hdinsight-cluster"></a>HDInsight クラスターを削除する方法
HDInsight クラスターは、クラスターが作成されたときに開始され、クラスターが削除されたときに停止し、分単位で課金されます。そのため、使用しなくなる場合は、常にクラスターを削除する必要がありあす。 このドキュメントでは、Azure ポータル、Azure PowerShell、および Azure CLI を使用してクラスターを削除する方法について説明します。

> [!IMPORTANT]
> HDInsight クラスターを削除しても、そのクラスターに関連付けられている Azure Storage アカウントは削除されません。 このため、クラスターに格納されているすべてのデータを保持し、再利用することができます。
> 
> 

## <a name="azure-portal"></a>Azure ポータル
1. [Azure ポータル](https://portal.azure.com) にログインして、HDInsight クラスターを選択します。 HDInsight クラスターがダッシュボードにピン留めされていない場合は、ナビゲーション バーの右側にある検索フィールド (虫眼鏡アイコン) で名前を指定して検索することができます。
   
    ![ポータルの検索](./media/hdinsight-delete-cluster/navbar.png)
2. ブレードでクラスターが表示されたら、**[削除]** アイコンを選択します。 メッセージが表示されたら、**[はい]** を選択してクラスターを削除します。
   
    ![削除アイコン](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell
クラスターを削除するには、PowerShell プロンプトで次のコマンドを実行します。

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

**CLUSTERNAME** を、使用する HDInsight クラスターの名前に置き換えます。

## <a name="azure-cli"></a>Azure CLI
クラスターを削除するには、プロンプトで次のコマンドを実行します。

    azure hdinsight cluster delete CLUSTERNAME

**CLUSTERNAME** を、使用する HDInsight クラスターの名前に置き換えます。




<!--HONumber=Nov16_HO3-->



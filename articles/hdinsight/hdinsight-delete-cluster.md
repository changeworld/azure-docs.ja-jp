---
title: "HDInsight クラスターを削除する方法 - Azure | Microsoft Docs"
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
ms.date: 05/03/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: 7d3aed71d9b5867a87954a60b1e470def0563f71
ms.contentlocale: ja-jp
ms.lasthandoff: 06/13/2017

---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>ブラウザー、PowerShell、または Azure CLI を使用して HDInsight クラスターを削除する

HDInsight クラスターの課金は、クラスターが作成されると開始し、クラスターが削除されると停止します。 課金は分単位なので、クラスターを使わなくなったら必ず削除してください。 このドキュメントでは、Azure Portal、Azure PowerShell、および Azure CLI 1.0 を使ってクラスターを削除する方法について説明します。

> [!IMPORTANT]
> HDInsight クラスターを削除しても、そのクラスターに関連付けられている Azure Storage アカウントは削除されません。 これにより、後でデータを再利用できます。

## <a name="azure-portal"></a>Azure ポータル

1. [Azure Portal](https://portal.azure.com) にログインして、HDInsight クラスターを選びます。 HDInsight クラスターがダッシュボードにピン留めされていない場合は、検索フィールドで名前を指定して検索することができます。
   
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



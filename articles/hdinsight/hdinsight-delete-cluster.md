---
title: HDInsight クラスターを削除する方法 - Azure
description: HDInsight クラスターの各種削除方法について説明します。
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: jasonh
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 737cc120877a9d0f06a1f6d209bcf9a233aa7d19
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091311"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>ブラウザー、PowerShell、または Azure CLI を使用して HDInsight クラスターを削除する

HDInsight クラスターの課金は、クラスターが作成されると開始し、クラスターが削除されると停止します。 課金は分単位なので、クラスターを使わなくなったら必ず削除してください。 このドキュメントでは、Azure Portal、Azure PowerShell、および Azure CLI 1.0 を使ってクラスターを削除する方法について説明します。

> [!IMPORTANT]
> HDInsight クラスターを削除しても、そのクラスターに関連付けられている Azure Storage アカウントまたは Data Lake Store は削除されません。 これらのサービスに格納されているデータは、将来再利用できます。

## <a name="azure-portal"></a>Azure ポータル

1. [Azure Portal](https://portal.azure.com) にログインして、HDInsight クラスターを選びます。 HDInsight クラスターがダッシュボードにピン留めされていない場合は、検索フィールドで名前を指定して検索することができます。
   
    ![ポータルの検索](./media/hdinsight-delete-cluster/navbar.png)

2. クラスター設定から、**[削除]** アイコンをクリックします。 メッセージが表示されたら、**[はい]** を選択してクラスターを削除します。
   
    ![削除アイコン](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

クラスターを削除するには、PowerShell プロンプトで次のコマンドを実行します。

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

**CLUSTERNAME** を、使用する HDInsight クラスターの名前に置き換えます。

## <a name="azure-cli-10"></a>Azure CLI 1.0

クラスターを削除するには、プロンプトで次のコマンドを実行します。

    azure hdinsight cluster delete CLUSTERNAME

**CLUSTERNAME** を、使用する HDInsight クラスターの名前に置き換えます。

> [!NOTE]
> 現時点 (2017 年 10 月 23 日) では、Azure CLI 2.0 は HDInsight クラスターの削除をサポートしていません。

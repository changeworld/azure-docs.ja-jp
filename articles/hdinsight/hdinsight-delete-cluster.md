<properties
pageTitle="HDInsight クラスターを削除する方法 | Azure"
description="HDInsight クラスターの各種削除方法について説明します。"
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/09/2016"
ms.author="larryfr"/>

#HDInsight クラスターを削除する方法

HDInsight クラスターは、クラスターが作成されたときに開始され、クラスターが削除されたときに停止し、分単位で課金されます。そのため、使用しなくなる場合は、常にクラスターを削除する必要がありあす。このドキュメントでは、Azure ポータル、Azure PowerShell、および Azure CLI を使用してクラスターを削除する方法について説明します。

> [AZURE.IMPORTANT] HDInsight クラスターを削除しても、そのクラスターに関連付けられている Azure Storage アカウントは削除されません。このため、クラスターに格納されているすべてのデータを保持し、再利用することができます。

##Azure ポータル

1. [Azure ポータル](https://portal.azure.com)にログインして、HDInsight クラスターを選択します。HDInsight クラスターがダッシュボードにピン留めされていない場合は、ナビゲーション バーの右側にある検索フィールド (虫眼鏡アイコン) で名前を指定して検索することができます。

    ![ポータルの検索](./media/hdinsight-delete-cluster/navbar.png)

2. ブレードでクラスターが表示されたら、__[削除]__ アイコンを選択します。メッセージが表示されたら、__[はい]__ を選択してクラスターを削除します。

    ![削除アイコン](./media/hdinsight-delete-cluster/deletecluster.png)

##Azure PowerShell

クラスターを削除するには、PowerShell プロンプトで次のコマンドを実行します。

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

__CLUSTERNAME__ を、使用する HDInsight クラスターの名前に置き換えます。

##Azure CLI

クラスターを削除するには、プロンプトで次のコマンドを実行します。

    azure hdinsight cluster delete CLUSTERNAME
    
__CLUSTERNAME__ を、使用する HDInsight クラスターの名前に置き換えます。

<!---HONumber=AcomDC_0914_2016-->
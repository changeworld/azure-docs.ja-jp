<properties
pageTitle="HDInsight クラスターを削除する方法 | Azure"
description="HDInsight クラスターの各種削除方法について説明します。"
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="03/07/2016"
ms.author="larryfr"/>

#HDInsight クラスターを削除する方法

HDInsight クラスターの課金は時間単位で行われますので、クラスターの使用を止める場合には必ず削除してください。このドキュメントでは、Azure ポータル、Azure PowerShell、および Azure CLI を使用してクラスターを削除する方法について説明します。

> [AZURE.IMPORTANT] HDInsight クラスターを削除しても、そのクラスターに関連付けられている Azure Storage アカウントは削除されません。このため、クラスターに格納されているすべてのデータを保持し、再利用することができます。

##Azure ポータル

1. [Azure ポータル](https://portal.azure.com)にログインして、HDInsight クラスターを選択します。HDInsight クラスターがダッシュボードにピン留めされていない場合は、ナビゲーション バーの右側にある検索フィールド (虫眼鏡アイコン) で名前を指定して検索することができます。

    ![ポータルの検索](./media/hdinsight-delete-cluster/navbar.png)

2. ブレードでクラスターが表示されたら、__[削除]__ アイコンを選択します。メッセージが表示されたら、__[はい]__ を選択してクラスターを削除します。

    ![削除アイコン](./media/hdinsight-delete-cluster/deletecluster.png)

##Azure PowerShell

> [AZURE.NOTE] Azure PowerShell をインストールおよび構成していない場合は、「[Azure PowerShell のインストールと構成](../powershell-install-configure.md)」の手順を実行してください。

クラスターを削除するには、PowerShell プロンプトで次のコマンドを実行します。

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

__CLUSTERNAME__ を、使用する HDInsight クラスターの名前に置き換えます。

##Azure CLI

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

クラスターを削除するには、プロンプトで次のコマンドを実行します。

    azure hdinsight cluster delete CLUSTERNAME
    
__CLUSTERNAME__ を、使用する HDInsight クラスターの名前に置き換えます。

<!---HONumber=AcomDC_0420_2016-->
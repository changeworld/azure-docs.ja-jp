---
title: 'クイック スタート:コンピューティングの一時停止と再開 - Azure portal '
description: Azure portal を使用して、SQL プールのコンピューティングを一時停止してコストを節約します。 データ ウェアハウスを使用する準備ができたら、コンピューティングを再開します。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 49684e6844c2d9f58e9b750b12991428218e4426
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200630"
---
# <a name="quickstart-pause-and-resume-compute-in-azure-synapse-analytics-sql-pool-in-the-azure-portal"></a>クイック スタート:Azure Synapse Analytics の SQL プールのコンピューティングを Azure portal で一時停止、再開する

Azure portal を使用して、SQL プールのコンピューティングを一時停止してコストを節約します。 データ ウェアハウスを使用する準備ができたら、[コンピューティングを再開](sql-data-warehouse-manage-compute-overview.md)します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="before-you-begin"></a>開始する前に

[ポータルでの作成と接続](create-data-warehouse-portal.md)に関する記事に従って、**mySampleDataWarehouse** という名前の SQL プールを作成してください。 

## <a name="pause-compute"></a>コンピューティングの一時停止

コストを節約するために、オンデマンドでコンピューティング リソースを一時停止および再開できます。 たとえば、夜間と週末にデータベースを使用しない場合、その期間にデータベースを一時停止して、日中に再開することができます。 データベースが一時停止されている間、コンピューティング リソースへの課金は行われません。 ただし、ストレージに対する課金は引き続き行われます。 

SQL プールを一時停止するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. Azure portal の左側のナビゲーション ページで **[Azure Synapse Analytics (以前の SQL DW)]** をクリックします。
2. **[Azure Synapse Analytics (以前の SQL DW)]** ページから **[mySampleDataWarehouse]** を選択して SQL プールを開きます。 
3. **[mySampleDataWarehouse]** ページで **[状態]** が **[オンライン]** になっていることがわかります。

    ![コンピューティングがオンライン](media/pause-and-resume-compute-portal/compute-online.png)

4. SQL プールを一時停止するには、 **[一時停止]** ボタンをクリックします。 
5. 続行してよいかどうかを確認するメッセージが表示されます。 **[はい]** をクリックします。
6. 少し待つと、 **[状態]** が **[一時停止中]** になります。

    ![一時停止中](media/pause-and-resume-compute-portal/pausing.png)

7. 一時停止操作が完了すると、状態が **[一時停止]** になり、オプション ボタンが **[再開]** になります。
8. これで SQL プールのコンピューティング リソースがオフラインになりました。 サービスを再開するまで、コンピューティングの料金は発生しません。

    ![コンピューティングがオフライン](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>コンピューティングの再開

SQL プールを再開するには、次の手順を実行します。

1. Azure portal の左側のページで **[Azure Synapse Analytics (以前の SQL DW)]** をクリックします。
2. **[Azure Synapse Analytics (以前の SQL DW)]** ページから **[mySampleDataWarehouse]** を選択して SQL プール ページを開きます。 
3. **[mySampleDataWarehouse]** ページで **[状態]** が **[一時停止]** になっていることがわかります。

    ![コンピューティングがオフライン](media/pause-and-resume-compute-portal/compute-offline.png)

4. SQL プールを再開するには、 **[再開]** をクリックします。 
5. 開始してよいかどうかを確認するメッセージが表示されます。 **[はい]** をクリックします。
6. **[状態]** が **[再開中]** になっていることがわかります。

    ![再開中](media/pause-and-resume-compute-portal/resuming.png)

7. SQL プールがオンライン状態に戻ると、状態が **[オンライン]** になり、オプション ボタンが **[一時停止]** になります。
8. これで SQL プールのコンピューティング リソースがオンラインになりました。サービスを使用することができます。 コンピューティングの課金が再開されます。

    ![コンピューティングがオンライン](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

データ ウェアハウス ユニットとデータ ウェアハウスに格納されているデータに対して課金されます。 これらのコンピューティングとストレージのリソースは別々に請求されます。 

- データをストレージ内に保持する場合は、コンピューティングを一時停止します。
- それ以上課金されないようにする場合は、SQL プールを削除できます。 

必要に応じて、以下の手順でリソースをクリーンアップします。

1. [Azure portal](https://portal.azure.com) にサインインし、SQL プールをクリックします。

    ![リソースをクリーンアップする](media/pause-and-resume-compute-portal/clean-up-resources.png)

1. コンピューティング リソースを一時停止するには、 **[一時停止]** ボタンをクリックします。 

2. コンピューティング リソースやストレージに課金されないように SQL プールを削除するには、 **[削除]** をクリックします。

3. 作成した SQL Server を削除するには、**sqlpoolservername.database.windows.net** をクリックした後、 **[削除]** をクリックします。  サーバーを削除すると、サーバーに割り当てられているすべてのデータベースが削除されるので、削除には注意してください。

4. リソース グループを削除するには、**myResourceGroup** をクリックして、 **[リソース グループの削除]** をクリックします。


## <a name="next-steps"></a>次のステップ

SQL プールに対するコンピューティングの一時停止と再開を行いました。 次のステップとして、データの読み込みに関するチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [SQL プールにデータを読み込む](load-data-from-azure-blob-storage-using-polybase.md)

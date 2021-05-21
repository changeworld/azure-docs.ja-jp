---
title: タグを使用して Azure NetApp Files の課金を管理する | Microsoft Docs
description: タグを使用して Azure NetApp Files の課金を管理する方法について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/06/2021
ms.author: b-juche
ms.openlocfilehash: b0efd8c580b0d4f1ae94d4dfc655bb351e084a67
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2021
ms.locfileid: "109480893"
---
# <a name="manage-billing-by-using-capacity-pool-billing-tags"></a>容量プールの課金タグを使用して課金を管理する

タグは、リソースを分類し、統合された課金を表示できるようにする、名前と値のペアです。 同じタグを複数のリソースとリソース グループに適用できます。  タグの詳細については、「[タグを使用して Azure リソースと管理階層を整理する](../azure-resource-manager/management/tag-resources.md)」を参照してください。  

タグを使用すると、Azure NetApp Files の課金と費用の管理に役立ちます。 たとえば、会社には Azure サブスクリプションが 1 つしかありませんが、Azure リソースを使用し、費用が発生する部門が複数存在する場合があります。 容量プール レベルでリソースに部門名のタグを付けることができます。 対応するタグが請求書に表示され、各部門によって発生した費用の確認に役立ちます。   

課金タグは、ボリューム レベルではなく、容量プール レベルで割り当てられます。

## <a name="steps"></a>手順

1. 容量プールでタグを追加または編集するには、**容量プール** に移動し、 **[タグ]** を選択します。   

2. **名前** と **値** のペアを入力します。  **[Apply]** をクリックします。

    > [!IMPORTANT] 
    > タグのデータはグローバルにレプリケートされます。 そのため、リソースのセキュリティを損なう可能性があるタグ名や値は使用しないでください。 たとえば、個人や機密の情報を含むタグ名は使用しません。 

      ![容量プールの [タグ] ウィンドウを示すスナップショット。](../media/azure-netapp-files/billing-tags-capacity-pool.png)

3. タグ付けされたリソースに関する情報は、[Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) ポータルを使用して表示およびダウンロードできます。 
    1. **[コスト分析]** をクリックし、 **[リソースごとのコスト]** ビューを選択します。    
      [ ![Azure Cost Management の [コスト分析] を示すスクリーンショット](../media/azure-netapp-files/cost-analysis.png) ](../media/azure-netapp-files/cost-analysis.png#lightbox)  

    2. 請求書をダウンロードするには、 **[請求書]** 、 **[ダウンロード]** ボタンの順に選択します。   
      [ ![Azure Cost Management の [請求書] を示すスクリーンショット](../media/azure-netapp-files/azure-cost-invoices.png) ](../media/azure-netapp-files/azure-cost-invoices.png#lightbox)  

    1. 表示される [ダウンロード] ウィンドウで、使用状況の詳細をダウンロードします。 ダウンロードした `csv` ファイルには、対応するリソースの容量プールの課金タグが含まれます。   
       ![Azure Cost Management の [ダウンロード] ウィンドウを示すスナップショット。](../media/azure-netapp-files/invoice-download.png)   

       [ ![ダウンロードされたスプレッドシートを示すスクリーンショット。](../media/azure-netapp-files/spreadsheet-download.png) ](../media/azure-netapp-files/spreadsheet-download.png#lightbox)

## <a name="next-steps"></a>次のステップ

[Azure NetApp Files のコスト モデル](azure-netapp-files-cost-model.md) 
---
title: Azure NetApp Files の容量プールまたはボリュームをサイズ変更する | Microsoft Docs
description: 容量プールまたはボリュームのサイズを変更する方法について説明します。 容量プールをサイズ変更すると、購入した Azure NetApp Files の容量が変更されます。
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
ms.date: 04/30/2021
ms.author: b-juche
ms.openlocfilehash: 419ae4e99ce5b8327ced306dea398ef82d5588db
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265114"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>容量プールまたはボリュームをサイズ変更する
ボリュームまたは容量プールがいっぱいになったときなど、必要に応じて容量プールまたはボリュームのサイズを変更できます。 

ボリュームの容量の監視に関する詳細については、「[ボリュームの容量を監視する](monitor-volume-capacity.md)」を参照してください。

## <a name="resize-the-capacity-pool-using-the-azure-portal"></a>Azure portal を使用して容量プールのサイズを変更する 

容量プールのサイズは、1 TiB 単位で増減できます。 ただし、容量プールのサイズは 4 TiB 未満にすることはできません。 容量プールをサイズ変更すると、購入した Azure NetApp Files の容量が変更されます。

1. [NetApp アカウント] ビューから **[容量プール]** にアクセスし、サイズ変更する容量プールをクリックします。
2. 容量プールの名前を右クリックするか、容量プールの行の最後にある […] アイコンをクリックして、コンテキスト メニューを表示します。 **[サイズ変更]** をクリックします。 

    ![プールのコンテキスト メニューを示すスクリーンショット。](../media/azure-netapp-files/resize-pool-context-menu.png)  

3. [プールのサイズ変更] ウィンドウで、プール サイズを指定します。  **[OK]** をクリックします。

    ![[プールのサイズ変更] ウィンドウを示すスクリーンショット。](../media/azure-netapp-files/resize-pool-window.png) 

## <a name="resize-a-volume-using-the-azure-portal"></a>Azure portal を使用してボリュームのサイズを変更する

ボリュームは、必要に応じてサイズ変更できます。 ボリュームの容量消費は、そのプールのプロビジョニング容量を前提としてカウントされます。

1. [NetApp アカウント] ビューから **[ボリューム]** にアクセスし、サイズを変更するボリュームをクリックします。
2. ボリュームの名前を右クリックするか、ボリュームの行の最後にある […] アイコンをクリックして、コンテキスト メニューを表示します。 **[サイズ変更]** をクリックします。

    ![ボリュームのコンテキスト メニューを示すスクリーンショット。](../media/azure-netapp-files/resize-volume-context-menu.png) 
    
3. [ボリュームのクォータを更新] ウィンドウで、ボリュームのクォータを指定します。 **[OK]** をクリックします。   

    ![[ボリュームのクォータを更新] ウィンドウを示すスクリーンショット。](../media/azure-netapp-files/resize-volume-quota-window.png) 

## <a name="resizing-the-capacity-pool-or-a-volume-using-azure-cli"></a>Azure CLI を使用して容量プールまたはボリュームのサイズを変更する  

[Azure コマンドライン (CLI) ツール](azure-netapp-files-sdk-cli.md)の次のコマンドを使用して、容量プールまたはボリュームのサイズを変更できます。

* [`az netappfiles pool`](/cli/azure/netappfiles/pool?preserve-view=true&view=azure-cli-latest)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume?preserve-view=true&view=azure-cli-latest)

## <a name="resizing-the-capacity-pool-or-a-volume-using-rest-api"></a>REST API を使用して容量プールまたはボリュームのサイズを変更する

容量プールとボリュームのサイズ変更を処理するオートメーションを構築できます。   

[Azure NetApp Files 用の REST API](azure-netapp-files-develop-with-rest-api.md) に関するページ、および [Azure NetApp Files 用の PowerShell を使用する REST API](develop-rest-api-powershell.md) に関するページを参照してください。 

Azure NetApp Files 用の REST API の仕様とコード例は、[resource-manager GitHub ディレクトリ](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable)で入手できます。 

## <a name="resize-a-cross-region-replication-destination-volume"></a>リージョン間レプリケーションの宛先ボリュームのサイズを変更する 

[リージョン間のレプリケーション](cross-region-replication-introduction.md)関係では、宛先ボリュームは、ソース ボリュームのサイズに基づいて、自動的にサイズ変更されます。 そのため、宛先ボリュームを個別にサイズ変更する必要はありません。 この自動サイズ変更の動作は、ボリュームがアクティブなレプリケーション関係にある場合、または[再同期操作](cross-region-replication-manage-disaster-recovery.md#resync-replication)でレプリケーション ピアリングが切断された場合に適用されます。 

次の表では、[ミラーの状態](cross-region-replication-display-health-status.md)に基づいた宛先ボリュームのサイズ変更の動作について説明します。

|  ミラーの状態  | 宛先ボリュームのサイズ変更の動作 |
|-|-|
| *ミラー化* | 宛先ボリュームが初期化済みで、ミラー化の更新を受信する準備ができている場合、ソース ボリュームをサイズ変更すると、宛先ボリュームが自動的にサイズ変更されます。 |
| *中断* | ソース ボリュームをサイズ変更し、ミラーの状態が *中断* の場合、宛先ボリュームは [再同期操作](cross-region-replication-manage-disaster-recovery.md#resync-replication)によって自動的にサイズ変更されます。  |
| *未初期化* | ソース ボリュームをサイズ変更し、ミラーの状態がまだ *未初期化* の場合は、宛先ボリュームのサイズ変更を手動で行う必要があります。 そのため、ソース ボリュームをサイズ変更する場合は、初期化が完了する (つまり、ミラーの状態が *ミラー化* になる) まで待つことをお勧めします。 | 

> [!IMPORTANT]
> リージョン間レプリケーションのソース ボリュームと宛先ボリュームの両方に対して、容量プールに十分なヘッドルームがあることを確認します。 ソース ボリュームのサイズを変更すると、宛先ボリュームは自動的にサイズ変更されます。 ただし、宛先ボリュームをホストしている容量プールのヘッドルームが十分でない場合は、ソース ボリュームと宛先ボリュームの両方のサイズ変更が失敗します。

## <a name="next-steps"></a>次のステップ

- [容量プールの作成](azure-netapp-files-set-up-capacity-pool.md)
- [手動 QoS 容量プールを管理する](manage-manual-qos-capacity-pool.md)
- [ボリュームのサービス レベルを動的に変更する](dynamic-change-volume-service-level.md) 
- [ボリューム クォータについて](volume-quota-introduction.md)
- [ボリュームの容量を監視する](monitor-volume-capacity.md)
- [容量管理に関する FAQ](faq-capacity-management.md)

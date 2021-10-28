---
title: Azure NetApp Files ボリュームの容量を監視する |Microsoft Docs
description: Azure NetApp Files ボリュームの容量使用率を監視する方法について説明します。
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
ms.openlocfilehash: 8f2dfe5d185b7de08dca189a83d856ac04d5404e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224118"
---
# <a name="monitor-the-capacity-of-a-volume"></a>ボリュームの容量を監視する  

この記事では、Azure NetApp Files ボリュームの容量使用率を監視する方法について説明します。  

## <a name="using-windows-or-linux-clients"></a>Windows または Linux クライアントの使用

このセクションでは、Windows または Linux クライアントを使用してボリューム容量を監視する方法について説明します。 このセクションで説明するシナリオでは、4 TiB の Ultra サービス レベルの容量プールで 1 TiB のサイズ (クォータ) で構成されたボリュームを想定しています。 

### <a name="windows-smb-clients"></a>Windows (SMB) クライアント

Windows クライアントを使用すると、ネットワーク マップ ドライブのプロパティを使用して、ボリュームの使用済みおよび使用可能な容量を確認できます。 次の 2 つの方法のいずれかを使用できます。 

* エクスプローラーに移動して、マップされたドライブを右クリックし、 **[プロパティ]** を選択して容量を表示します。  

    [ ![エクスプローラーのドライブのプロパティとボリュームのプロパティを示しているスクリーンショット。](../media/azure-netapp-files/monitor-explorer-drive-properties.png) ](../media/azure-netapp-files/monitor-explorer-drive-properties.png#lightbox)

* コマンド プロンプトで `dir` コマンドを使用します。 

    ![dir コマンドを使用した容量の表示を示しているスクリーンショット。](../media/azure-netapp-files/monitor-volume-properties-dir-command.png) 

"*使用可能な領域*" は、エクスプローラーまたは `dir` コマンドを使用する場合は正確です。 ただし、"*消費済み/使用領域*" は、ボリュームでスナップショットが生成された時点の推定値になります。 "[消費済みスナップショット容量](azure-netapp-files-cost-model.md#capacity-consumption-of-snapshots)"  は、ボリュームの消費済み合計容量にカウントされます。 スナップショットで使用される容量を含め、ボリュームの絶対消費量を取得するには、Azure portal で [Azure NetApp メトリック](azure-netapp-files-metrics.md#volumes)を使用します。 

### <a name="linux-nfs-clients"></a>Linux (NFS) クライアント 

Linux クライアントでは、[df コマンド](https://linux.die.net/man/1/df)を使用して、ボリュームの使用済みおよび使用可能な容量を確認できます。  

`-h` オプションを使用すると、サイズ (使用済みおよび使用可能な領域を含む) が、ユーザーが判読できる形式で (M、G、T 単位のサイズを使用して) 表示されます。

次のスナップショットは、Linux でのボリューム容量レポートを示しています。

![Linux でのボリューム容量レポートを示すスクリーンショット。](../media/azure-netapp-files/monitor-volume-properties-linux-command.png) 

"*使用可能な領域*" は、`df` コマンドを使用する場合は正確です。 ただし、"*消費済み/使用領域*" は、ボリュームでスナップショットが生成された時点の推定値になります。 "[消費済みスナップショット容量](azure-netapp-files-cost-model.md#capacity-consumption-of-snapshots)"  は、ボリュームの消費済み合計容量にカウントされます。 スナップショットで使用される容量を含め、ボリュームの絶対消費量を取得するには、Azure portal で [Azure NetApp メトリック](azure-netapp-files-metrics.md#volumes)を使用します。 

## <a name="using-azure-portal"></a>Azure Portal の使用
Azure NetApp Files では、標準の [Azure Monitor](../azure-monitor/overview.md) 機能を活用します。 そのため、Azure Monitor を使用して Azure NetApp Files ボリュームを監視することができます。  

## <a name="using-azure-cli"></a>Azure CLI の使用  

[Azure コマンド ライン (CLI) ツール](azure-netapp-files-sdk-cli.md)の [`az netappfiles volume`](/cli/azure/netappfiles/volume?view=azure-cli-latest&preserve-view=true) コマンドを使用して、ボリュームを監視することができます。
 
## <a name="using-rest-api"></a>REST API の使用  

[Azure NetApp Files 用の REST API](azure-netapp-files-develop-with-rest-api.md) に関するページ、および [Azure NetApp Files 用の PowerShell を使用した REST API](develop-rest-api-powershell.md) に関するページを参照してください。 

Azure NetApp Files の REST API 仕様とコード例は、[resource Manager GitHub ディレクトリ](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable)で入手できます。 

## <a name="next-steps"></a>次のステップ

* [ボリューム クォータについて](volume-quota-introduction.md)
* [Azure NetApp Files のコスト モデル](azure-netapp-files-cost-model.md)
* [容量プールまたはボリュームのサイズを変更する](azure-netapp-files-resize-capacity-pools-or-volumes.md)
* [容量管理に関する FAQ](faq-capacity-management.md)
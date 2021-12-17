---
title: Azure NetApp Files を使用してオンデマンド スナップショットを作成する | Microsoft Docs
description: Azure NetApp Files を使用してオンデマンド スナップショットを作成する方法について説明します。
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
ms.date: 10/25/2021
ms.author: b-juche
ms.openlocfilehash: c6bbc11fc77de516d2e163d1463444f2a3c0210e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069940"
---
# <a name="create-an-on-demand-snapshot-for-a-volume"></a>ボリュームのオンデマンド スナップショットを作成する

Azure NetApp Files では、オンデマンド [スナップショット](snapshots-introduction.md)の作成と、スナップショット ポリシーを使用した自動スナップショット作成のスケジュール設定がサポートされています。 また、[新しいボリュームにスナップショットを復元](snapshots-restore-new-volume.md)したり、[クライアントを使用して 1 つのファイルを復元したり](snapshots-restore-file-client.md)、[スナップショットを使用して既存のボリュームを元に戻したり](snapshots-revert-volume.md)することもできます。 この記事では、ボリュームのオンデマンド スナップショットを作成する方法について説明します。 

> [!NOTE] 
> * リージョン間レプリケーションでのスナップショット管理に関する考慮事項については、「[リージョン間レプリケーションを使用するための要件と考慮事項](cross-region-replication-requirements-considerations.md)」を参照してください。
> * Azure アプリケーション整合スナップショットツール (AzAcSnap) を使用して、サードパーティのデータベースのデータ保護を有効にすることができます。 詳細については、「 [Azure アプリケーション整合性スナップショットツール](azacsnap-introduction.md)とは」を参照してください。
 
## <a name="steps"></a>手順

1.  スナップショットを作成するボリュームに移動します。 **[スナップショット]** をクリックします。

    ![スナップショット ブレードに移動する方法を示すスクリーンショット。](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  **[+ スナップショットの追加]** をクリックして、ボリュームのオンデマンド スナップショットを作成します。

    ![スナップショットを追加する方法を示すスクリーンショット。](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  [新しいスナップショット] ウィンドウで、作成する新しいスナップショットの名前を指定します。   

    ![[新しいスナップショット] ウィンドウを示すスクリーンショット。](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. **[OK]** をクリックします。 

## <a name="next-steps"></a>次のステップ

* [スナップショットの詳細を確認する](snapshots-introduction.md)
* [Azure NetApp Files でスナップショット ポリシーを管理する](snapshots-manage-policy.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files のスナップショット 101 ビデオ](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
* [Azure アプリケーション整合性スナップショット ツールとは](azacsnap-introduction.md)

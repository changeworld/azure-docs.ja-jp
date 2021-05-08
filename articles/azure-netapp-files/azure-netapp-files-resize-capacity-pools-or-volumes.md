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
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: 869f46207b940521ee0b66b5afa9c6e2718ab04f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594480"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>容量プールまたはボリュームをサイズ変更する
容量プールまたはボリュームは、必要に応じてサイズ変更できます。 

## <a name="resize-the-capacity-pool"></a>容量プールをサイズ変更する 

容量プールのサイズは、1 TiB 単位で増減できます。 ただし、容量プールのサイズは 4 TiB 未満にすることはできません。 容量プールをサイズ変更すると、購入した Azure NetApp Files の容量が変更されます。

1. [Manage NetApp Account]\(NetApp アカウントの管理\) ブレードで、サイズ変更する容量プールをクリックします。 
2. 容量プールの名前を右クリックするか、容量プールの行の最後にある「…」アイコンをクリックして、コンテキスト メニューを表示します。 
3. コンテキスト メニューのオプションを使用して、容量プールをサイズ変更するか、削除します。

## <a name="resize-a-volume"></a>ボリュームをサイズ変更する

ボリュームは、必要に応じてサイズ変更できます。 ボリュームの容量消費は、そのプールのプロビジョニング容量を前提としてカウントされます。

1. [Manage NetApp Account]\(NetApp アカウントの管理\) ブレードで、 **[ボリューム]** をクリックします。 
2. サイズ変更するボリュームの名前を右クリックするか、ボリュームの行の最後にある「...」アイコンをクリックして、コンテキスト メニューを表示します。
3. コンテキスト メニューのオプションを使用して、ボリュームをサイズ変更するか、削除します。

## <a name="resize-a-cross-region-replication-destination-volume"></a>リージョン間レプリケーションの宛先ボリュームのサイズを変更する 

[リージョン間のレプリケーション](cross-region-replication-introduction.md)関係では、宛先ボリュームは、ソース ボリュームのサイズに基づいて、自動的にサイズ変更されます。 そのため、宛先ボリュームを個別にサイズ変更する必要はありません。 この自動サイズ変更の動作は、ボリュームがアクティブなレプリケーション関係にある場合、または[再同期操作](cross-region-replication-manage-disaster-recovery.md#resync-replication)でレプリケーション ピアリングが切断された場合に適用されます。 

次の表では、[ミラーの状態](cross-region-replication-display-health-status.md)に基づいた宛先ボリュームのサイズ変更の動作について説明します。

|  ミラーの状態  | 宛先ボリュームのサイズ変更の動作 |
|-|-|
| *ミラー化* | 宛先ボリュームが初期化済みで、ミラー化の更新を受信する準備ができている場合、ソース ボリュームをサイズ変更すると、宛先ボリュームが自動的にサイズ変更されます。 |
| *中断* | ソース ボリュームをサイズ変更し、ミラーの状態が *中断* の場合、宛先ボリュームは[再同期操作](cross-region-replication-manage-disaster-recovery.md#resync-replication)によって自動的にサイズ変更されます。  |
| *未初期化* | ソース ボリュームをサイズ変更し、ミラーの状態がまだ *未初期化* の場合は、宛先ボリュームのサイズ変更を手動で行う必要があります。 そのため、ソース ボリュームをサイズ変更する場合は、初期化が完了する (つまり、ミラーの状態が *ミラー化* になる) まで待つことをお勧めします。 | 

> [!IMPORTANT]
> リージョン間レプリケーションのソース ボリュームと宛先ボリュームの両方に対して、容量プールに十分なヘッドルームがあることを確認します。 ソース ボリュームのサイズを変更すると、宛先ボリュームは自動的にサイズ変更されます。 ただし、宛先ボリュームをホストしている容量プールのヘッドルームが十分でない場合は、ソース ボリュームと宛先ボリュームの両方のサイズ変更が失敗します。

## <a name="next-steps"></a>次のステップ

- [容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)
- [手動 QoS 容量プールを管理する](manage-manual-qos-capacity-pool.md)
- [ボリュームのサービス レベルを動的に変更する](dynamic-change-volume-service-level.md) 
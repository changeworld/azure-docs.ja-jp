---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 77fc6070010791bf96c944114929eba95842c9d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471700"
---
プレビュー段階では、共有ディスクが有効になっているマネージド ディスクには、次の制限があります。

- 現在、Premium SSD でのみ利用可能です。
- 現在、米国中西部リージョンのみでサポートされています。
- ディスクを共有するすべての仮想マシンは、同じ[近接通信配置グループ](../articles/virtual-machines/windows/proximity-placement-groups.md)にデプロイする必要があります。
- OS ディスクではなく、データ ディスクでのみ有効にすることができます。
- Windows Server フェールオーバー クラスターの一部のバージョンでは、ベーシック ディスクのみを使用できます。詳細については、「[フェールオーバー クラスタリングのハードウェア要件と記憶域オプション](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)」を参照してください。
- ReadOnly ホスト キャッシュは、`maxShares>1` の Premium SSD では使用できません。
- 可用性セットと仮想マシン スケール セットは、`FaultDomainCount` が 1 に設定されている場合にのみ使用できます。
- Azure Backup および Azure Site Recovery のサポートは、まだ使用できません。

共有ディスクの使用に関心がある場合は、[プレビューにサインアップ](https://aka.ms/AzureSharedDiskPreviewSignUp)します。

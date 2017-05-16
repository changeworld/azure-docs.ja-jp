---
title: "Azure での Linux VM の VM 保持メンテナンス | Microsoft Docs"
description: "メモリ保持更新のためのインプレース VM 移行。"
services: virtual-machines-windows
documentationcenter: 
author: 
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 08da5407cc5ddceeba21a558dc0de1008a566bab
ms.lasthandoff: 04/03/2017


---



# <a name="vm-preserving-maintenance-in-place-vm-migration"></a>VM 保持メンテナンス (インプレース VM 移行)

多くの更新はホステッド VM に影響がありませんが、コンポーネントまたはサービスの更新によって、実行中の VM に対するごくわずかな干渉が発生する (仮想マシンの完全な再起動は不要) ことがあります。

これらの更新は、インプレース ライブ移行 ("メモリ保持更新" とも呼ばれます) を実現するテクノロジによって完了します。 ホストの更新時は仮想マシンが "一時停止" 状態になり、RAM 内のメモリが保持されます。一方、ホスティング環境 (基礎となるオペレーティング システムなど) には必要な更新プログラムと修正プログラムが適用されます。
仮想マシンは、一時停止後 30 秒以内に再開されます。
再開後、仮想マシンのクロックは自動的に同期されます。

このメカニズムを使用してすべての更新をデプロイできるわけではありませんが、一時停止の期間が短い場合、この方法で更新をデプロイすることにより、仮想マシンへの影響が大幅に軽減されます。

複数インスタンスの更新 (可用性セット内の VM) が、一度に 1 つの更新ドメインに適用されます。

仮想マシン内で実行されているアプリケーションは、メタデータ サービスのスケジュールされたイベントを呼び出すことで今後の更新を確認できます。 スケジュールされたイベントの詳細については、「[Azure Metadata Service: スケジュールされたイベント](../virtual-machines-scheduled-events.md)」を参照してください。

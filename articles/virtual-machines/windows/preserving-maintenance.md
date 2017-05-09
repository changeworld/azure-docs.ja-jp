---
title: "Azure での Windows VM の VM 保持メンテナンス | Microsoft Docs"
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
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: bc903f7523295da704ea8f0128dd553e3fdd96a9
ms.lasthandoff: 03/31/2017


---



# <a name="vm-preserving-maintenance-with-in-place-vm-migration"></a>インプレース VM 移行による VM 保持メンテナンス

多くの更新はホステッド VM に影響がありませんが、コンポーネントまたはサービスの更新によって、実行中の VM に対する干渉が最小限になる (仮想マシンの完全な再起動が不要になる) ことがあります。

これらの更新は、インプレース ライブ移行 ("メモリ保持" 更新とも呼ばれます) を実現するテクノロジによって達成されます。 ホストの更新時、仮想マシンは "一時停止" 状態になり、RAM 内のメモリは保持されます。一方、ホスティング環境 (基礎となるホスト オペレーティング システム) は必要な更新プログラムと修正プログラムを適用します。
仮想マシンは、30 秒以内の一時停止で再開されます。
再開後、仮想マシンのクロックは自動的に同期されます。

このメカニズムを使用してすべての更新をデプロイできるわけではありませんが、一時停止の期間が短い場合、この方法で更新をデプロイすることにより、仮想マシンへの影響が大幅に軽減されます。

複数インスタンスの更新 (可用性セット内の VM) が、一度に 1 つの更新ドメインに適用されます。

仮想マシン内で実行されているアプリケーションは、メタデータ サービスのスケジュールされたイベントを呼び出すことで今後の更新を確認できます。 スケジュールされたイベントの詳細については、「[Azure Metadata Service: スケジュールされたイベント](../virtual-machines-scheduled-events.md)」を参照してください。

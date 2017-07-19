---
title: "Azure での Windows VM の VM 保護メンテナンス | Microsoft Docs"
description: "メモリ保持更新のためのインプレース VM 移行。"
services: virtual-machines-windows
documentationcenter: 
author: zivr
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
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 8888bafbc3aba24168312b611a9b4fbde25f376d
ms.contentlocale: ja-jp
ms.lasthandoff: 05/31/2017


---



# <a name="vm-preserving-maintenance-with-in-place-vm-migration"></a>インプレース VM 移行による VM 保持メンテナンス

多くの更新はホステッド VM に影響がありませんが、コンポーネントまたはサービスの更新によって、実行中の VM に対するごくわずかな干渉が発生する (仮想マシンの完全な再起動は不要) ことがあります。

これらの更新は、インプレース ライブ移行 ("メモリ保護更新" とも呼ばれます) を可能にするテクノロジによって実現します。 ホストの更新時は仮想マシンが "一時停止" 状態になり、RAM 内のメモリが保護されます。一方、ホスティング環境 (基礎となるオペレーティング システムなど) には必要な更新プログラムと修正プログラムが適用されます。
仮想マシンは、一時停止後 30 秒以内に再開されます。
再開後、仮想マシンのクロックは自動的に同期されます。

このメカニズムを使用してすべての更新をデプロイできるわけではありませんが、一時停止の期間が短い場合、この方法で更新をデプロイすることにより、仮想マシンへの影響が大幅に軽減されます。

複数インスタンスの更新 (可用性セット内の VM) が、一度に 1 つの更新ドメインに適用されます。

一部のアプリケーションは、これらの更新プログラムによって他のアプリケーションよりも影響を受ける可能性があります。 リアルタイム イベント処理、メディア ストリーミングまたはコード変換、あるいは高スループットのネットワーク シナリオなどを実行するアプリケーションは、30 秒の一時停止を許容するようには設計されていない可能性があります。 仮想マシン内で実行されているアプリケーションは、[Azure Metadata Service](../virtual-machines-instancemetadataservice-overview.md) の[スケジュールされたイベント](../virtual-machines-scheduled-events.md)の API を呼び出すことで、今後の更新プログラムを確認できます。


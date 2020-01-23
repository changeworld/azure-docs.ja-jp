---
title: 既存の可用性セットへの Azure VM の追加のサポート | Microsoft Docs
description: この記事では、同じ可用性セットに混在させることができる VM シリーズに関するサポート マトリックスを提供します
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: cb0034f2b353284e94d6f1508541b31040a5b076
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028411"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>既存の可用性セットへの Azure VM の追加のサポート

既存の可用性セットに新しい仮想マシン (VM) を追加するときに、制限されることがあります。 次の表では、同じ可用性セットに混在させることができる VM シリーズについて詳しく説明します。

異なる種類の VM の混在に関するサポート マトリックスを次に示します。

シリーズと可用性セット|第 2 の VM|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|第 1 の VM|||||||
|A||[OK]|[OK]|[OK]|[OK]|[OK]|
|Av2||[OK]|[OK]|[OK]|[OK]|[OK]|
|D||[OK]|[OK]|[OK]|[OK]|[OK]|
|Dv2||[OK]|[OK]|[OK]|[OK]|[OK]|
|Dv3||[OK]|[OK]|[OK]|[OK]|[OK]|

他のすべてのシリーズは、特定のハードウェアが必要なため、同じ可用性セットに存在することはできません。

専用 RDMA バックエンド ネットワークに関する要件のため、A8/A9 サイズの VM を混在させることはできません。
